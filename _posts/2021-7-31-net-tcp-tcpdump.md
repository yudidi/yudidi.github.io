---
layout: post
title: 从mysql连接池说起,分析golang tcp连接池 TODO
---

# 为什么有这篇
[主要看了这篇文章里面的一句话:为了解决TCP无限创建与TCP频繁创建销毁带来的资源耗尽、性能下降问题。
MySQL服务器里有专门的TCP连接池限制接数，采用长连接模式复用TCP连接，来解决上述问题。](https://mp.weixin.qq.com/s/GMiwroGgBP9hAyzyv9_uKg)

# Q:尝试学习下，建立连接需要消耗哪些资源
//附录1
那么单条 SQL 执行时间是多少呢？我们统计了一段时间的 SQL 执行时间，发现 SQL 的平均执行时间大概是 1ms，也就是说相比于 SQL 的执行，MySQL 建立连接的过程是比较耗时的。

这在请求量小的时候其实影响不大，因为无论是建立连接还是执行 SQL，耗时都是毫秒级别的。

可是请求量上来之后，如果按照原来的方式建立一次连接只执行一条 SQL 的话，1s 只能执行 200 次数据库的查询，而数据库建立连接的时间占了其中 4/5。


# Q:明确连接池为什么可以提高效率
A: 那么为什么频繁创建连接会造成响应时间慢呢？来看一个实际的测试。

# Q:怎么写连接池，主要注意啥

[Golang 连接池的几种实现案例](https://learnku.com/articles/41137)

[golang tcp连接池](https://github.com/nange/easypool)

[golang server epoll client 使用连接池 15万+ qps](https://segmentfault.com/a/1190000019736068)


# mysql群,面试问题
## Q: Mysql内部会有连接池这种机制吗？


# 如何设计一个连接池--golang数据库组件database/sql连接池的实现
// 附录2

# 参考
1.[07丨池化技术：如何减少频繁创建数据库连接的性能损耗？](https://zq99299.github.io/note-architect/hc/02/01.html#%E7%94%A8%E8%BF%9E%E6%8E%A5%E6%B1%A0%E9%A2%84%E5%85%88%E5%BB%BA%E7%AB%8B%E6%95%B0%E6%8D%AE%E5%BA%93%E8%BF%9E%E6%8E%A5)
2.[golang http服务器连接重用_系统优化连接池技术原理与实现
-如何设计一个连接池 本文例子主要讲解golang数据库组件database/sql连接池的实现](https://blog.csdn.net/weixin_39855186/article/details/112099125?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-0.no_search_link&spm=1001.2101.3001.4242.1)