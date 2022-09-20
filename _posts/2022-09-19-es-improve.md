---
layout: post 
title: es奔溃和优化
categories: [性能优化]
tags: [es]
---

# 背景


#  

* 1.查看old分区的gc耗时

从平时的不到0.5ms变为3ms,扩大6倍

![_config.yml]({{ site.baseurl }}/images/content/220220919-old_gc耗时.png)

* 2.查看old分区使用情况

old分区应该是2g。

差不多30分钟-90分钟就会从900MB达到1.8G触发old gc

注意到，Old区的内存持续增长，30-90分钟内存就会将Old区填满，经过Old GC几乎全部可以回收掉一半。

* 3.old区gc频次

每次在old区快用完时,gc频次监控都有个尖刺。

![_config.yml]({{ site.baseurl }}/images/content/20220919-old_gc频率_load信息.png)

* 4.注意到同时也有full gc

* ![_config.yml]({{ site.baseurl }}/images/content/20220919-full_gc.png)


# 猜测

1.es大量写入，refresh和fsync操作比较费资源，导致es负载增加

# 参考
1.[Elasticsearch GC 优化实战](https://yuerblog.cc/2021/03/12/elasticsearch-gc%E4%BC%98%E5%8C%96%E5%AE%9E%E6%88%98/)
2.[es写入原理](https://zhuanlan.zhihu.com/p/205211099)