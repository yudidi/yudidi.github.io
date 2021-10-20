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

# 附录
1.[Linux软连接和硬链接](https://www.cnblogs.com/itech/archive/2009/04/10/1433052.html)
2.[图示软硬链接的区别](https://xzchsia.github.io/2020/03/05/linux-hard-soft-link/)
3.[mv命令是move的缩写，可以用来移动文件或者将文件改名（move (rename) files），是Linux系统下常用的命令，经常用来备份文件或者目录。](https://www.cnblogs.com/peida/archive/2012/10/27/2743022.html)