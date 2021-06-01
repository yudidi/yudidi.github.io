---
layout: post
title: 大数据量sql分页查询很慢，有哪些优化方案？ TODO
---

# 为什么慢
针对查询偏移量的测试：
```
select * from orders_history where type=8 limit 100,100;
select * from orders_history where type=8 limit 1000,100;
select * from orders_history where type=8 limit 10000,100;
select * from orders_history where type=8 limit 100000,100;
select * from orders_history where type=8 limit 1000000,100;
```

三次查询时间如下：

查询100偏移：25ms 24ms 24ms
查询1000偏移：78ms 76ms 77ms
查询10000偏移：3092ms 3212ms 3128ms
查询100000偏移：3878ms 3812ms 3798ms
查询1000000偏移：14608ms 14062ms 14700ms

随着查询偏移的增大，尤其查询偏移大于10万以后，查询时间急剧增加。


这种分页查询方式会从数据库第一条记录开始扫描，所以越往后，查询速度越慢，而且查询的数据越多，也会拖慢总查询速度。

# 如何改进

# 参考
1.[大数据量sql分页查询很慢，有哪些优化方案？](https://zhuanlan.zhihu.com/p/85728202)
