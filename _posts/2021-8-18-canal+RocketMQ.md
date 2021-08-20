---
layout: post
title: Canal+阿里云的RocketMQ
---

# 主要内容
1.RocketMQ Name Server的功能与定位

2.搭建基于Canal+阿里云的RocketMQ的数据同步环境的一些问题的原因解析

# 背景

最近在搭建另一套基于Canal+阿里云的RocketMQ的数据同步环境,在canal-server投递到阿里云RocketMQ时,不能投递成功,从canal-instance日志中遇到一些报错:
```bash

```

但是我在本地搭建了一个RocketMQ,是可以正常投递的.



TODO 画一些交互图,时序图 来记录动态运行的流程.

## Q:为什么需要Name Server这个角色的存在？

A: Producer、Consumer在发送、消费的消息都是与Broker打交到，那它们是怎么知道与哪个Broker去交互呢？

类似这样的信息就需要从Name Server去获取。Name Server提供的最主要的功能就是Broker信息的发现、Topic路由信息的维护。

Name Server承担的就是服务发现、配置分发功能。

A: 简单总结

producer和consumer 都需要 先去 broker(服务注册中心) 拿到对应微服务(broker)的地址, 然后根据这个地址去和对应微服务(broker)进行数据交互.

broker告诉producer标记不同topic的消息应该发往哪个broker。

broker告诉consuer需要去哪个broker获取指定topic的消息。


## Q:
Name Server提供的最重要的功能就是Topic路由信息的维护，任何发送消费的消息都归属于某个Topic，发送消费都需要和Broker打交道。Name Server维护了Topic/Broker的关联关系。

Topic/Broker之间的关系则是Broker定期上报Name Server，Name Server自身定期检查Broker信息是否有效，如果Broker宕机或下线，对应的路由信息会被移除。

Name Server中用于维护相关信息的代码实现主要位于RouteInfoManager，其关键用于记录的成员为，


A: 简单总结

1. broker相当于微服务,name-sever相当于服务注册和发现中心

2.微服务(broker)通过发送心跳包刷新自身的存活状态. 如果微服务(broker)挂了,那么就将这个微服务(broker)从注册中心摘掉。



# 附录
1.[RocketMQ Name Server的功能与定位](http://soliloquize.org/2018/08/18/RocketMQ-Name-Server%E7%9A%84%E5%8A%9F%E8%83%BD%E4%B8%8E%E5%AE%9A%E4%BD%8D/)