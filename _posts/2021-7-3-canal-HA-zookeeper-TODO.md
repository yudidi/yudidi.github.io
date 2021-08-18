---
layout: post
title: 阿里canal是怎么通过zookeeper实现HA机制的？
---

最近升级公司的canal集群,同时也了解canal集群的基本交互流程和HA的实现方法。

# 不同server上的instance要求同一时间只能有一个处于running，其他的处于standby状态
为了保证有序性，一份 instance 同一时间只能由一个 canal client 进行get/ack/rollback操作，否则客户端接收无法保证有序。

# 理解canal的server和instance // 附录2
* server代表一个canal运行实例，相当于一个jvm
 
* instance相当于一个数据队列，即一个mysql实例 （1个server可以对应多个instance)


# HA机制设计的大致实现 // 附录5
canal的ha分为两部分，canal server和canal client分别有对应的ha实现



## 线上查看zookeeper目录树
```
[zk: localhost:2181(CONNECTED) 30] ls /otter/canal
[cluster, destinations]
[zk: localhost:2181(CONNECTED) 31] ls /otter/canal/cluster // 表示集群???
[192.168.0.64:11111]
[zk: localhost:2181(CONNECTED) 32] ls /otter/canal/destinations   //这下面表示目前的intances实例
[dev01db, dev02db, dev022db]
```


# TODO canal配置文件和zk里面数据的对应关系

* canal.properties        
```bash
# tcp bind ip
canal.ip =
# register ip to zookeeper
canal.register.ip =
canal.port = 11111  #canal server提供socket tcp服务的端口  #canal-server接收zk通知的端口吗? 这个端口谁来访问?
canal.metrics.pull.port = 11112 #c-s暴露的监控数据抓取接口. 可以配置到prometheus.yml的targets里面
# canal instance user/passwd
canal.user = canal
canal.passwd = E3619321C1A937C46A0D8BD1DAC39F93B27D4458

canal.zkServers = 192.168.0.64:2181
```

* zk: ls /otter/canal/destinations

* zk: get /otter/canal/destinations

# 参考
1.[图解阿里canal是怎么通过zookeeper实现HA机制的？](https://segmentfault.com/a/1190000023297973)
2.[理解canal的server和instance](https://blog.csdn.net/XDSXHDYY/article/details/97825508)
3.[Canl实战HA配置](https://segmentfault.com/a/1190000023297973)
4.[「从零单排canal 02」canal集群版 + admin控制台 最新搭建姿势（基于1.1.4版本）](https://www.cnblogs.com/awan-note/p/13089193.html)
5.[canal工作原理及简单案例演示-2.3.7 HA机制设计](https://blog.csdn.net/u012637358/article/details/107976074)