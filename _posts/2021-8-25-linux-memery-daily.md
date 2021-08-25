---
layout: post
title: 面试题:一个进程最多可以申请多少物理内存?
---

# 背景
技术群讨论,面试题

## Q:问各位一个问题：4g服务器，一个进程最多可以申请多少物理内存（不是虚拟内存的寻址范围）

A: 我记得可以申请无限制的
先申请无限的，然后缺页时加载到物理内存，如果物理内存满了，那就交换到磁盘里面去?

最多申请: 磁盘+物理内存 那么多?

# linux内存工作原理
// 附录1
![_config.yml]({{ site.baseurl }}/images/content/20210825-linux-malloc-mem.png)

# 如何查看内存使用情况
// 附录1

* 64位的Linux默认占用256TB中的128TB，程序只能使用剩下的128TB。
// 附录2

# 附录
1.[15 | 基础篇：Linux内存是怎么工作的？虚拟地址(内核空间和用户空间)+缺页异常+物理内存](https://time.geekbang.org/column/article/74272)
2.[一个程序最多可以使用多少内存？-操作系统还要占用内存](https://blog.csdn.net/weixin_42709563/article/details/106234230)
