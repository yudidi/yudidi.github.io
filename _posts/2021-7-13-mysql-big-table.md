---
layout: post
title: 大表处理思路(单表大于50G)
---

# 背景
2021上半年大促发放了大量的优惠券,导致优惠券表数据量暴增,t表半年约新增3亿条记录,参考附录1查看表数据信息。

* 查看指定数据库各表容量大小
```sql
select 
table_schema as '数据库',
table_name as '表名',
table_rows as '记录数',
truncate(data_length/1024/1024, 2) as '数据容量(MB)',
truncate(index_length/1024/1024, 2) as '索引容量(MB)'
from information_schema.tables
where table_schema='xxx'
order by data_length desc, index_length desc;
```
* 查询结果如下,t单表大于50GB:数据40G+索引22G

| 数据库   | 表名             | 记录数       | 数据容量(MB) | 索引容量(MB) |
|-------|----------------|-----------|----------|----------|
| xxx | t | 343112190 | 39595.23 | 22068.64 |

* t表结构示意如下
```sql
-- 主要存放用户领取和使用优惠券的数据
CREATE TABLE `t` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '自动编号',
  `user_id` int(10) unsigned NOT NULL COMMENT '所属用户',
  `voucher_id` int(10) unsigned NOT NULL COMMENT '优惠券',
  `voucher_type` enum('all','category','brand') NOT NULL DEFAULT 'all' COMMENT '优惠券类型',
  `used` tinyint(4) NOT NULL DEFAULT '0' COMMENT 'bool状态，是否已经使用',
  `create_time` int(11) unsigned NOT NULL COMMENT '领取的时间',
  PRIMARY KEY (`id`),
  KEY `user_id` (`user_id`),
  KEY `voucher_id` (`voucher_id`),
) ENGINE=InnoDB AUTO_INCREMENT=383790307 DEFAULT CHARSET=utf8 ROW_FORMAT=DYNAMIC;
```

# 三个方案

## 1.分库分表的方案
略

## 2.冷热分离的方案
分库分表会导致多个业务线和统计业务的编码复杂化,所以不考虑分库分表,采用一种类似冷热分离的思路: 使用2个表。

* 1个热点数据表(阿里云rds),给业务用。单表总量基本可以维持在一亿以内,每几个月清理一次数据,性能尚好。

* 1个全量表(阿里云adb),给统计用。单表一年新增10亿以内,性能还好。

每n天通过脚本将一段时间内的热点数据表的数据(`insert|update|delete`),增量的同步到全量表。

## 3.[阿里云的drds](https://www.aliyun.com/product/drds)
有了方案2,暂时不考虑这个。原理可参考附录2。

# 最终方案
方案2

# 效果
待我搞完才知道,应该能支持个3-5年。

# 经验总结
尽量避免分库分表,采用冷热分离的思路

# LOG
发到一些技术交流群中,收到如下反馈

## Q:如果要查冷数据,那怎么办？
A(我): 确实有一部分冷数据一直保留在热点表里面的，就是那种只能领取一次的券，需要校验用户只能领取一次。
   除此之外，只有统计需要查询其他的冷数据，业务上不需要查。
   所以删除的冷数据仅仅指的是，业务上不再需要查询的那部分冷数据，这部分数据是大头。
   业务上需要查询的冷数据，则保留在热点表中。
   
A(我): 估计分库分表我要重新考虑下,在多个群被质疑了

A(其他): 分库分表分场景,优惠券适合冷备吧

A(其他): 哦,有大量的优惠券没有领取

A(我): 恩,所以很多都没意义了，没有领取又失效了，那么业务上就没意义了，只有统计上需要。

## Q: 云原生分布式数据库 PolarDB-X,这个方案如何?
https://www.aliyun.com/product/drds

A(我): 还在调研,暂未考虑。

## Q: 全量表三年以后不是要有50亿,30亿数据还怎么查?
A: 估计分库分表我要重新考虑下,在多个群被质疑了

# 附录
1.[MySQL查看数据库表容量大小-3.查看指定数据库容量大小](https://blog.csdn.net/fdipzone/article/details/80144166)
2.[首页 > PolarDB-X 云原生分布式数据库 > 技术白皮书 > 技术原理](https://help.aliyun.com/document_detail/261147.html?spm=a2c4g.11186623.2.8.285b7942kkETra)