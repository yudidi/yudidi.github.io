---
layout: post
title: RocketMQ一个新的消费组初次启动时从何处开始消费呢？
categories: [RocketMQ]
tags: [RocketMQ]
---

# 背景
使用mq报错如下
```json
{"level":"warn","ts":"2022-01-13 20:48:11","logger":"ROCKET","msg":"delete mq from offset table","consumerGroup":"实例xxx%GID_Canal_price_t_product","MessageQueue":"MessageQueue [topic=实例xxx%aplum_t_product, brokerName=bjshare6-06, queueId=0]"}
{"level":"error","ts":"2022-01-13 20:48:11","logger":"ROCKET","msg":"fecth offset of mq from broker error","consumerGroup":"实例xxx%GID_Canal_price_t_product","MessageQueue":"MessageQueue [topic=实例xxx%aplum_t_product, brokerName=bjshare6-06, queueId=0]","underlayError":"broker response code: 22, remarks: Not found, V3_0_6_SNAPSHOT maybe this group consumer boot first"}
```
RocketMQ一个新的消费组初次启动时从何处开始消费呢？

最后发现是我启动go程序,启动mq subscibe后,程序直接退出了,在mq的日志离开看到上述报错。

看着报错日志很懵,是因为对apache rocketmq sdk的处理没有理解得很好。

# 参考
1.[RocketMQ一个新的消费组初次启动时从何处开始消费呢？]()