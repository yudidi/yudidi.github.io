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
分库分表会导致多个业务线和统计业务的编码复杂化,所以不考虑分库分表,采用一种类似冷热分离的思路: 改为弄2个表，冷热分离的思路。

2.1 1个热点数据表(阿里云rds),给业务用。单表总量基本可以维持在一亿以内,每几个月清理一次数据,性能尚好。

2.2 1个全量表(阿里云adb),给统计用。单表一年新增10亿以内,性能还好。

每n天通过脚本将一段时间内的热点数据表的数据(`insert|update|delete`),增量的同步到全量表。

## 3.[阿里云的drds](https://www.aliyun.com/product/drds)
有了方案2,暂时不考虑这个

# 最终方案
方案2

# 效果
待我搞完才知道,应该能支持个3-5年。

# 经验总结
尽量避免分库分表,采用冷热分离的思路

# 附录
1.[MySQL查看数据库表容量大小-3.查看指定数据库容量大小](https://blog.csdn.net/fdipzone/article/details/80144166)