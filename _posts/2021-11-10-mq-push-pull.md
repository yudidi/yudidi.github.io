---
layout: post
title: RocketMQpush模式的处理过程
categories: [RocketMQ]
tags: [RocketMQ]
---

# 背景
最近在做一个较大的性能优化,就是把商品的价格从实时计算改为离线计算。
这需要捕捉到所有影响商品价格的因子,这些因子主要是mysql的表.
所以考虑使用我们现有的canal来捕捉binlog变化,进而刷新价格.

但是商品数量是比较大，大几十万，不知道canal投递MQ的性能和消费的性能如何,所以需要测试。

RocketMQ有pull,puss两种消费模式。

pull每次能拉16

push则不受限制

# TODO push模式的处理过程
附录1,2,3 // 附录4 

1. 而push方式是broker主動推送消息，但是push方式的底層實質是異步去broker上拉取消息，然後主動推送給consumer。
// http://hk.noobyard.com/article/p-hyydxnqw-ty.html

2.整个push模式的处理过程，其中几个关键点： // 附录2
通过pull模式实现
主动发起消息的接取，RebalanceImpl类的子类RebalancePushImpl是拉取消息的入口
在rebalance后会触发消息的拉取
拉取成功后，调用listener的consumeMessage方法

#
1.[消息队列推拉push/pull模式优缺点及使用改进的长轮询模式的意义](https://blog.csdn.net/wabiaozia/article/details/102057941)
2.[RocketMQ的push模式机制](https://zhuanlan.zhihu.com/p/89169364)
3.[MQ中Pull和Push的两种消费方式](https://www.cnblogs.com/fanguangdexiaoyuer/p/10510634.html)
4.[首页>消息队列RocketMQ版>最佳实践>消息堆积和延迟问题-产品详情-消息堆积和延迟问题](https://help.aliyun.com/document_detail/193875.html)