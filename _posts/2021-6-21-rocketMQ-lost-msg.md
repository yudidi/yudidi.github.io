---
layout: post
title: 阿里云RocketMQ订阅关系不一致导致丢消息问题记录
---

# 问题
用户服务的线上环境和预发布环境对RocketMQ的使用,没有遵循订阅关系一致性，导致丢消息。// 参考附录

线上: GID_a&topic=EVENT_USER&tag=AD_SOURCE

预发布: GID_aE&topic=EVENT_USER&tag=AD_SOURCE_PRE

# 原因
```
消息队列RocketMQ版里的一个消费者Group ID代表一个Consumer实例群组。对于大多数分布式应用来说，一个消费者Group ID下通常会挂载多个Consumer实例。
由于消息队列RocketMQ版的订阅关系主要由Topic+Tag共同组成，因此，保持订阅关系一致意味着同一个消费者Group ID下所有的实例需在以下两方面均保持一致：
1.订阅的Topic必须一致
2.订阅的Topic中的Tag必须一致（包括Tag的数量和Tag的顺序）
```

# 参考
[订阅关系一致文档](https://help.aliyun.com/document_detail/43523.html?spm=a2c4g.11174283.6.776.22d7347doZEAZ6)
