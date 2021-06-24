---
layout: post
title: 工作实践-同1台开发机器,不同账户(同事)使用统一版本的thrfit和grpc编译工具的方法
---

1. 查找thrift文件位置
/opt/xxxx/thrift/bin/thrift

2. 新同事,修改.bashrc文件, 把/opt/xxxx/thrift/bin/thrift加入到环境变量中

3. 注意设置完环境变量,需要使用source ~/.bashrc刷新一下使其生效

```
[xxxx@dev02 ~]$ whereis thrift
thrift: /opt/xxxx/thrift/bin/thrift
[xxxx@dev02 ~]$ echo $PATH
/home/xxxx/.gvm/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/xxxx/.local/bin:/home/xxxx/bin:/opt/xxxx/go1.13/bin:/opt/xxxx/thrift/bin
```

# 参考
[linux 查看环境变量和修改环境变量](https://blog.csdn.net/w6028819321/article/details/21600423)