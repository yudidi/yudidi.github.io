---
layout: post
title: es查询时分词和索引时分词
---

![_config.yml]({{ site.baseurl }}/images/content/golang-gc.png)

一般而言只有升级go版本才能提升gc的能力。

同一个go版本，垃圾更少，gc次数更少, 提高的是吞吐量，而不是gc的能力，垃圾收集器清理垃圾的能力并没有提高。

比如:
10w次请求，产生垃圾w。

优化后。

20w次请求，才产生垃圾w。

[区分 提升gc能力 和 通过减少分配的对象进而减少gc次数 是两种不同的思路](https://www.ardanlabs.com/blog/2018/12/garbage-collection-in-go-part1-semantics.html)