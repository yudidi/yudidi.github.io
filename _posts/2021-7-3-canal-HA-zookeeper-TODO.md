---
layout: post
title: 【转载】阿里canal是怎么通过zookeeper实现HA机制的？
---

最近升级公司的canal集群,同时也了解canal集群的基本交互流程和HA的实现方法。

# 不同server上的instance要求同一时间只能有一个处于running，其他的处于standby状态
为了保证有序性，一份 instance 同一时间只能由一个 canal client 进行get/ack/rollback操作，否则客户端接收无法保证有序。

# 理解canal的server和instance // 附录2
* server代表一个canal运行实例，相当于一个jvm
 
* instance相当于一个数据队列，即一个mysql实例 （1个server可以对应多个instance)

## 线上查看zookeeper目录树
```
[zk: localhost:2181(CONNECTED) 30] ls /otter/canal
[cluster, destinations]
[zk: localhost:2181(CONNECTED) 31] ls /otter/canal/cluster // 表示集群???
[192.168.0.64:11111]
[zk: localhost:2181(CONNECTED) 32] ls /otter/canal/destinations   //这下面表示目前的数据库实例
[dev01db, dev02db, dev022db]
```

# 参考
1.[图解阿里canal是怎么通过zookeeper实现HA机制的？](https://segmentfault.com/a/1190000023297973)
2.[理解canal的server和instance](https://blog.csdn.net/XDSXHDYY/article/details/97825508)
3.[Canl实战HA配置](https://segmentfault.com/a/1190000023297973)
4.[「从零单排canal 02」canal集群版 + admin控制台 最新搭建姿势（基于1.1.4版本）](https://www.cnblogs.com/awan-note/p/13089193.html)