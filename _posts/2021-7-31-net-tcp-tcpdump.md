---
layout: post
title: 从mysql连接池说起,分析golang tcp连接池 TODO
---

# 为什么有这篇

[主要看了这篇文章里面的一句话:为了解决TCP无限创建与TCP频繁创建销毁带来的资源耗尽、性能下降问题。
MySQL服务器里有专门的TCP连接池限制接数，采用长连接模式复用TCP连接，来解决上述问题。](https://mp.weixin.qq.com/s/GMiwroGgBP9hAyzyv9_uKg)

# Q:尝试学习下，建立连接需要消耗哪些资源

# Q:明确连接池为什么可以提高效率

# Q:怎么写连接池，主要注意啥

[Golang 连接池的几种实现案例](https://learnku.com/articles/41137)

[golang tcp连接池](https://github.com/nange/easypool)

[golang server epoll client 使用连接池 15万+ qps](https://segmentfault.com/a/1190000019736068)