---
layout: post
title: mysql面试题:orderby是怎么实现的 && MySQL默认是主键排序吗?
categories: [mysql]
tags: [mysql]
---

![_config.yml]({{ site.baseurl }}/images/content/mysql-interview.png)

# 背景
做业务需求时,发现有主键排序的需求,但是排序可能导致sql扫描行数极端情况下暴增,可能导致性能问题。
但是线上版本没有加显示排序，新需求需要加上主键升序排序，所以产生了一下疑问:

```sql
旧sql:
select * from xxx where yyy limit 1;
新sql: 极端情况下这个sql可能扫描几万行数然后需要选择id最小的一个。不知道新sql是否默认按照id升序查找第一个结果的。
select * from xxx where yyy order by id limit 1;
```

# 疑问
mysql是否存在默认按照主键升序排序的情况

# 结论

# 参考 
1.[深究mysql默认排序，order by的顺序](https://zhuanlan.zhihu.com/p/150921981)
2.[MySQL默认排序真的是按主键来排序的吗](https://segmentfault.com/a/1190000016251056)