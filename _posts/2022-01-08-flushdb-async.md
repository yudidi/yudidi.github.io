---
layout: post
title: redis4.0新特性-非阻塞删除
categories: [redis]
tags: [redis]
---

# 背景
最近做商品全量静态化的线上测试.偶尔需要清空全量缓存。
因为之前有同事线上执行keys等命令导致redis不可用。所以我也不敢盲目flushdb。

# 知识点
// 附录1
Redis作为一个单线程模型的服务，当执行一些耗时的命令时，比如使用DEL删除一个大key（元素超大的集合类型key），或者使用FLUSHDB 和 FLUSHALL 清空数据库，会造成redis阻塞，影响redis性能，甚至导致集群发生故障转移。另外redis在删除过期数据或因内存超过容量淘汰内存数据时，也有可能因为大key导致redis阻塞。

为了解决以上问题，redis 4.0 引入了惰性删除lazyfree的机制，它可以将删除键或数据库的操作放在后台线程里执行，删除对象时只是进行逻辑删除，从而尽可能地避免服务器阻塞。

lazyfree使用
针对以上两种场景，redis分别新增了几个命令和配置选项，同时lazyfree的使用分为2类：第一类是与DEL命令对应的主动删除，第二类是过期key删除、key淘汰删除。

# 公司redis版本
redis-5.0_0.5.7

# flushdb async
对于清空数据库命令flushall/flushdb，添加了async异步清理选项，使得redis在清空数据库时都是异步操作。

实现逻辑是为数据库新建一个新的空的字典，把原有旧的数据库字典给后台线程来逐一删除其中的数据，释放内存。

# 参考
1.[[redis学习笔记]redis4.0新特性-非阻塞删除](https://luoming1224.github.io/2018/11/11/%5Bredis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%5Dredis4.0%E6%96%B0%E7%89%B9%E6%80%A7-%E9%9D%9E%E9%98%BB%E5%A1%9E%E5%88%A0%E9%99%A4/)