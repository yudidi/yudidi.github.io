---
layout: post
title: Linux软连接和硬链接 && mv命令
---

# 发现磁盘空间不足时,需要的做软链接
1. 停止服务,备份当前日志文件
2. 删除当前日志目录,改为创建软链接到新挂载的大磁盘目录中
3. 这样应用进程就会写到新磁盘中。

TODO

可能的问题
1. 需要停止服务

2. 能否用脚本一键操作?

```shell
#  停止服务 
sh deploy/run.sh stop 
# 在新磁盘创建对应日志目录
mkdir -p /data/aplum/price-srv-logs/logs && mkdir /data/aplum/price-srv-logs/runtime && mkdir /data/aplum/price-srv-logs/runtime_canal
# 移动旧日志
mv logs/ /data/aplum/price-srv-logs && mv runtime/ /data/aplum/price-srv-logs && mv runtime_canal/ /data/aplum/price-srv-logs
# 当前目录创建软链接(logs,runtime,runtime_canal),指向新磁盘日志路径
ln -s /data/aplum/price-srv-logs/logs logs && ln -s /data/aplum/price-srv-logs/runtime runtime && ln -s /data/aplum/price-srv-logs/runtime_canal runtime_canal && sh deploy/run.sh start

# 验证下磁盘使用率
cd ~ && du -h --max-depth=1
df -lh
```

# mv命令:移动或改名,
取决于目标目录是否存在。存在则移动，不存在则无法移动就是改名
附录5

```bash
mv source_directory(目录) dest_directory(目录)	

1.目录名 dest_directory 已存在，将 source_directory 移动到目录名 dest_directory 中；
2.目录名 dest_directory 不存在则 source_directory 改名为目录名 dest_directory
```


# 附录
1.[Linux软连接和硬链接](https://www.cnblogs.com/itech/archive/2009/04/10/1433052.html)
2.[图示软硬链接的区别](https://xzchsia.github.io/2020/03/05/linux-hard-soft-link/)
3.[mv命令是move的缩写，可以用来移动文件或者将文件改名（move (rename) files），是Linux系统下常用的命令，经常用来备份文件或者目录。](https://www.cnblogs.com/peida/archive/2012/10/27/2743022.html)
4.[linux 软连接的使用](https://www.cnblogs.com/sueyyyy/p/10985443.html)
5.[Linux mv 命令](https://www.runoob.com/linux/linux-comm-mv.html)