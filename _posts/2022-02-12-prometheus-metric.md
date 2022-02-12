---
layout: post
title: Prometheus Metrics 设计的最佳实践和应用实例，看这篇够了！
categories: [prometheus监控]
tags: [prometheus监控]
---

# 背景
某个业务想增加一个监控指标。

就是每天不同渠道的老客唤起数量。 每日需要重置,然后更新，然后再累加。

而 Prometheus Metrics 有四种基本的 type：

```
Counter: 只增不减的单变量
Gauge：可增可减的单变量  // 看起来需要这个变量
Histogram：多桶统计的多变量
Summary：聚合统计的多变量
```

# 

# 参考
1.[Prometheus Metrics 设计的最佳实践和应用实例，看这篇够了！](https://www.cnblogs.com/tencent-cloud-native/p/13686763.html)