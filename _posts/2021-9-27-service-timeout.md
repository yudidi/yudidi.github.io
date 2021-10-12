---
layout: post
title: 服务超时:接口,mysql,
---

# 背景
今天线上除了事故导致APP挂掉. 原因是阿里云数据库被大量查询访问导致超载,响应慢。

我们的调用链路是:

1.APP -> 2.php服务端 -> 3.Go基础服务端 -> 4.阿里云数据库


# 附录
1.[使用 timeout、deadline 和 context 取消参数使 Go net/http 服务更灵活](https://jishuin.proginn.com/p/763bfbd2fb6a)
