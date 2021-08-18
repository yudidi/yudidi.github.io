---
layout: post
title: RocketMQ Name Server的功能与定位
---

# Q:为什么需要Name Server这个角色的存在？

A: Producer、Consumer在发送、消费的消息都是与Broker打交到，那它们是怎么知道与哪个Broker去交互呢？

类似这样的信息就需要从Name Server去获取。Name Server提供的最主要的功能就是Broker信息的发现、Topic路由信息的维护。

Name Server承担的就是服务发现、配置分发功能。


# 附录
[RocketMQ Name Server的功能与定位](http://soliloquize.org/2018/08/18/RocketMQ-Name-Server%E7%9A%84%E5%8A%9F%E8%83%BD%E4%B8%8E%E5%AE%9A%E4%BD%8D/)