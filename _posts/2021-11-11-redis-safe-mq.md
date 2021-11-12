---
layout: post
title: RocketMQpush模式的处理过程
categories: [RocketMQ]
tags: [RocketMQ]
---

# 背景

什么时候用RocketMQ队列，什么时候用redis队列

或者说持久化，不丢消息，何时仍MQ，何时仍redis(list,hash)

#  
1.[模式： 安全的队列](http://redisdoc.com/list/rpoplpush.html#id3)
2.[Redis 消息队列的三种方案（List、Streams、Pub/Sub）](https://stor.51cto.com/art/202101/640335.htm)