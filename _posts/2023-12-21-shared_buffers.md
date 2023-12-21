---
layout: post
title: pg什么时候加载shared_buffers
categories: [pg]
tags: [性能优化 pg]
---

# Q: 什么时候加载shared_buffers？
A: 1.在访问数据时，数据会先加载到os缓存，然后再加载到shared_buffers，这个加载过程可能是一些查询，也可以使用pg_prewarm预热缓存。

A:
PostgreSQL缓存读顺序share_buffers -> 操作系统缓存 -> 硬盘。

那么也可能是操作系统缓存不足，而不定是share_buffers。

通过文章中vmstat命令看到cache有260G，free值也很稳定，所以应该检查PostgreSQL的缓存。（老师执行vmstat是不是埋了个伏笔）。

# 参考

1.[32丨当Postgres磁盘读引起I/O高的时候，应该怎么办？](https://time.geekbang.org/column/article/207096)

2.[什么时候加载shared_buffers](https://www.cnblogs.com/zhangfx01/p/postgresql_shared_buffer.html)