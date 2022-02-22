---
layout: post
title: 服务超时:接口,mysql
categories: [go超时]
tags: [go超时]
---

# 背景
今天线上除了事故导致APP挂掉. 原因是阿里云数据库被大量查询访问导致超载,响应慢。

我们的调用链路是:

1.APP -> 2.php服务端 -> 3.Go基础服务端 -> 4.阿里云数据库

# 附录1描述了取消释放资源的不同情况 TODO

关于超时，可以把开发者分为两类：一类是了解超时多么难以捉摸的人，另一类是正在感受超时如何难以捉摸的人。

# 附录
1.[使用 timeout、deadline 和 context 取消参数使 Go net/http 服务更灵活](https://jishuin.proginn.com/p/763bfbd2fb6a)
