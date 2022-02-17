---
layout: post
title: docker容器占用磁盘大,迁移数据目录 TODO
categories: [docker]
tags: [docker]
---
# 本文回答2个问题

1.docker容器占用磁盘大,如何做数据迁移。TODO 应该是迁移容器层的东西?

2.OverlayFS 是怎么工作

# 背景
测试环境用docker运行了很多容器。且几年前安装时默认使用的系统盘存储。
想迁移到数据盘中。

# Q: 1.docker容器占用磁盘大,如何做数据迁移。
附录3,4 分别对应软链接和移动全部数据的方式

# Q:2.OverlayFS 是怎么工作
// 附录1,2
OverlayFS 也是把多个目录合并挂载，被挂载的目录分为两大类：lowerdir 和 upperdir。

lowerdir 允许有多个目录，在被挂载后，这些目录里的文件都是不会被修改或者删除的，也就是只读的；

upperdir 只有一个，不过这个目录是可读写的，挂载点目录中的所有文件修改都会在 upperdir 中反映出来。

容器的镜像文件中各层正好作为 OverlayFS 的 lowerdir 的目录，然后加上一个空的 upperdir 一起挂载好后，就组成了容器的文件系统。


# 参考
1.[OverlayFS 也是把多个目录合并挂载，被挂载的目录分为两大类：lowerdir 和 upperdir](https://time.geekbang.org/column/article/318173)

2.[07 | 白话容器基础（三）：深入理解容器镜像](https://time.geekbang.org/column/article/17921?cid=100015201)

3.[最方便的docker数据目录迁移教程-TODO这个好像不行.--必须使用mv命令，cp会丢失权限信息，可能会导致意想不到的bug](https://blog.csdn.net/laogouhuli/article/details/92831024?spm=1001.2101.3001.6650.1&utm_medium=distribute.wap_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.wap_blog_relevant_default&depth_1-utm_source=distribute.wap_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.wap_blog_relevant_default)

4.[Docker数据目录(/var/lib/docker)迁移](https://blog.csdn.net/hanly_jiang/article/details/115964569)