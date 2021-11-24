---
layout: post
title: redis hash的使用场景
categories: [Go]
tags: [Go]
---

# 背景
最近在做电商商品列表页和详情页的价格缓存. 在设计缓存数据结构时,用到了hash结构。

这里记录一下redis hash的主要的使用场景.

具体参考附录1,非常详细的描述了redis的hash解决的问题,以及其底层的存储结构。

# 参考
1.[Redis 几种数据类型及应用场景](https://juejin.cn/post/6844903951502934030)