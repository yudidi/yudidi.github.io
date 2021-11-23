---
layout: post
title: 深入golang runtime的调度
categories: [Go]
tags: [Go]
---

# 背景
技术群遇到一个面试题分享:

Q: 为什么优先从p的freeg中获取，这样做有什么优点?

他描述的问题应该是为什么优先从本地队列拿，而不是steal或者全局。

当一个g执行完成之后，并不是直接收回g的内存空间，而是把g加入到p所对应的freeg列表当中，这就是空闲的g。

操作全局队列要加锁。如果是为啥要从本地队列里面拿，好像是为了避免多个p竞争锁？

# 参考
1.[深入golang runtime的调度](https://zboya.github.io/post/go_scheduler/)
2.[Golang深入理解GPM模型](https://blog.csdn.net/dream_1996/article/details/118051217)