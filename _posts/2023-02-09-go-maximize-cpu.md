---
layout: post 
title: go语言如何最大化利用cpu时间?&&Go为什么这么快?
categories: [go]
tags: [go]
---

# 1.Go调度器经过几个大版本的迭代才有今天的优异性能
// 附录2


# Q:4. go语言如何(在windows, linux)最大化利用 cpu 时间？ (讲出实现原理)
// 附录1

Go 程序提供了**网络轮询器（NetPoller）**来处理网络请求和 IO 操作的问题，其后台通过 kqueue（MacOS），epoll（Linux）或 iocp（Windows）来实现 IO 多路复用。


A:
1.对于系统调用,用了epoll,让cpu最大化的有用户太线程干活儿

2.对于非系统调用,一直有goroutine在运行,内核线程不会停


本文主要从 Go 调度器架构层面上介绍了 G-P-M 模型，通过该模型怎样实现少量内核线程支撑大量 Goroutine 的并发运行。

以及通过 NetPoller、sysmon 等帮助 Go 程序减少线程阻塞，充分利用已有的计算资源，从而最大限度提高 Go 程序的运行效率。


# 参考
1.[Go 为什么这么快 GPM模型简介](http://shanks.link/blog/2021/07/08/go-%E4%B8%BA%E4%BB%80%E4%B9%88%E8%BF%99%E4%B9%88%E5%BF%AB-gpm%E6%A8%A1%E5%9E%8B%E7%AE%80%E4%BB%8B/)
2.[](https://draveness.me/golang/docs/part3-runtime/ch06-concurrency/golang-goroutine/)
3.[可视化GMP分析trace(TODO)](https://bbs.huaweicloud.com/blogs/327068)