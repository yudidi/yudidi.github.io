---
layout: post
title: redis hash的使用场景
categories: [redis]
tags: [redis]
---

# 背景
最近在做电商商品列表页和详情页的价格缓存. 在设计缓存数据结构时,用到了hash结构。

这里记录一下redis hash的主要的使用场景.

具体参考附录1,非常详细的描述了redis的hash解决的问题,以及其底层的存储结构。


# redis zset使用场景 TODO
背景:
存放用户搜索的主词和后继词.

key:主词
后继词s:按照CTR排序

```bash
r-xxx.redis.rds.aliyuncs.com:6379[11]> ZRANGE 劳力士 0 -1 WITHSCORES
男表
5.0097563200000002
手表男
5.1728499799999996
女表
5.2558560600000002
表
5.3265461399999996
劳力士男
5.7810733499999998
机械
5.7932277499999998
手表女
5.8832969500000001
劳力士女士日志
6.3485439599999998
劳力士女士
6.55728651
劳力士迪通拿
6.7381185199999996
劳力士间金
6.7414224999999997
劳力士 黑色
6.7624214499999997
劳力士36
6.7869144600000002
劳力士绿水鬼
6.7884882400000004
劳力士水鬼
6.8178139
劳力士 游艇
6.8229029700000003
劳力士鬼王
6.8240750099999996
劳力士日志
6.9103165100000004
手表
7.6918011399999999
包
9.7704448900000003
```

# 参考
1.[Redis 几种数据类型及应用场景](https://juejin.cn/post/6844903951502934030)
2.[键值对采用不同类型保存时的内存开销](https://time.geekbang.org/column/article/279649?cid=100056701)