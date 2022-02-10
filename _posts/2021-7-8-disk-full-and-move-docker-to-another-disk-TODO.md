---
layout: post
title: 【线上事故处理】磁盘满了的处理步骤复盘总结
---

# 事故简述
一早上来到公司，看到报警有大量报警信息。
```
云服务器实例ID=xxxx，device=/dev/vda1] /dev/vda1 ，磁盘使用率（100%>=97% ）（100%>=97%），持续时间5小时46分钟 
```
这个服务主要有2个功能
1.作为定时任务的主要执行机器。
2.部署了archery这个sql审核平台。

一开始怀疑是某个定时任务产生了大量日志文件，问了周围同事，最近并没有新增定时任务。

靠经验判断不行，那只能去定位大文件的位置，然后再继续分析问题了。

# 磁盘满了,定位大文件的通常步骤

* 1.使用df -h查看磁盘空间占用情况。

可以发现是`/dev/vda1`磁盘占用较多，对应`/`根目录

```bash
aplum@task:/ df -h
Filesystem                                      Size  Used Avail Use% Mounted on
/dev/vda1                                        99G   93G    2G  95% /
/dev/vdb1                                       985G  322G  618G  35% /data
```

* 2.使用 `du -s /* | sort -nr` 命令查看那个目录占用空间大

因为没有权限,所以我这里给出示意结果。

```bash
/data/aplum/Archery-1.7.10/src/docker-compose  占用很大
```

du -lh --max-depth=1 |sort -n


* 3.然后看哪个目录占用多,再通过 `du -s 怀疑的路径 | sort -nr` 一层层排查，找到占用文件多的地方。

我今天发现的问题是`/data/aplum/Archery-1.7.10/src/docker-compose/mysql/datadir/`

* 4.查看archery的docker-compose.yaml文件,发现mysql容器挂载到了该目录。

archery应该执行某种操作，导致archery的mysql插入了大量的数据.
```bash
version: '3'

networks: 略

services:
  redis:略

  mysql:
    image: mysql:5.7
    container_name: mysql
    restart: always
    ports:
      - "3306:3306"
    volumes:
      - "./mysql/my.cnf:/etc/mysql/my.cnf"
      - "./mysql/datadir:/var/lib/mysql"  # mysql容器挂载到了该目录
    environment:
      MYSQL_DATABASE: archery
      MYSQL_ROOT_PASSWORD: xxx
    networks:
      - cow-cow

  inception: 略
  goinception: 略
  archery: 略
```

* 5.因为昨天同事通过archery平台提交了一个大表的删除语句,该表一共3000w+的数据,此次删除2000w+,按照id拆分为十多个如下删除语句:

```sql
DELETE FROM 某个大表 where user_id = 0 and id BETWEEN 0 and 5000000;
```

* 6.结合阿里云的云盘监控可以看到`2021-07-08 04:30:00`左右,云盘使用空间从75%猛增为100%。
这个sql执行时间为`2021-07-08 04:05:00`,正好接近磁盘分区(/dev/vda1)的使用空间陡增的时间,

![_config.yml]({{ site.baseurl }}/images/content/20210708-task-archery-disk-used.png)

## 发现到大文件后,不能删除,需要移动到`/dev/vdb1`这个大磁盘中

因为这是archery的docker集群的一个挂载文件,所以只能把archer整体迁移。

正好这个机器还有1个空间充足的磁盘`/dev/vdb1`,mv过去即可。

# 迁移docker-compose部署的archery的一些问题

1.首先，Archery的安装方式设计的比较方便迁移, 解压Archery-1.7.10.tar.gz后,所有东西都在里面了.
尤其是docker-compose.yaml中的`volumes挂载都是相对路径`,这样我只需要把解压后的目录整体移动,其中的数据也会一起迁移,因为mysql的挂载目录也在里面。

2.实际处理问题的时候,因为对于docker-compose的up,down,start,stop命令不够熟悉,导致没有及时解决问题,这里记录一下。

* 我们错误的迁移步骤

1.我们执行了stop命令

2.然后执行mv迁移解压后Archery目录到`/dev/vdb1`

3.然后切入到docker-compose的目录,执行docker start,发现启动不了,【报错信息说原来的挂载路径找不到】

//原因是:stop命令只是停止容器,而我们已经移动了挂载的目录,重写删除容器,重新挂载。

4.所以执行down命令删除容器和挂载关系。

5.然后执行docker-compose up命令,archery就在迁移后成功重启了，且其中mysql的挂载目录也切换到了新的磁盘空间。

```bash
aplum@task:/data/aplum docker-compose -h
Define and run multi-container applications with Docker.
Usage:
  docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
  docker-compose -h|--help
Options:
Commands:
  down               Stop and remove containers, networks, images, and volumes # 停止和删除容器、网络、卷、镜像，这些内容是通过docker-compose up命令创建的. 默认值删除 容器 网络，可以通过指定 rmi 、volumes参数删除镜像和卷。
  start              Start services # 启动已经存在的服务容器。
  stop               Stop services # 停止已经处于运行状态的容器,但不删除它。通过 docker-compose start 可以再次启动这些容器
  up                 Create and start containers # 它尝试自动完成包括构建镜像，（重新）创建服务，启动服务，并关联服务相关容器的一些列操作。链接的服务都将会被自动启动，除非已经处于运行状态。多数情况下我们可以直接通过该命令来启动一个项目。
```

# 总结
1. linux磁盘已满，查看哪个文件占用多。
2. 如何知道哪个磁盘增长最多. 一般来说,就是文件最大的那个增长最多,有时也需要结合经验判断。
3. 对于大文件,要么删除,要么移动。我们对于此次问题,因为我们的大文件是mysql容器挂载的数据文件,所以不能删除,需要移动。

# archery删除大表时的回滚机制是怎么做的，为什么会占用大量的磁盘空间?
// 附录5
archery每执行一个sql,就会在备份库里生成对应的sql语句，而且产生的回滚语句都是针对一行的，
所以今天的大表的删除语句产生的回滚sql应是2千多万行，导致磁盘占用从75G变为100G，基本对得上。


# 附录6:
统计当前文件夹(目录)大小，并按文件大小排序：
du -sh * | sort -n
du -lh --max-depth=1 |sort -n

# 参考
1.[linux磁盘已满，查看哪个文件占用多](https://blog.csdn.net/a854517900/article/details/80824966)
2.[goInception Docs-TODO回滚机制](https://hanchuanchuan.github.io/goInception/#architecture)
3.[docker compose 几个命令](https://yeasy.gitbook.io/docker_practice/compose/commands#stop)
4.[docker compose down命令的作用](https://maizitoday.github.io/post/docker%E7%B3%BB%E5%88%97-compose/#down)
5.[Inception 备份功能说明](https://inception-document.readthedocs.io/zh_CN/latest/backup/)
6.[Linux查看文件或文件夹大小: du命令](https://blog.csdn.net/duan19920101/article/details/104823301)