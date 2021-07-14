---
layout: post
title: 大表处理-尽量避免分库分表-采用冷热分离的思路
---

# 背景
因为业务增长了5倍以上，发放了大量的优惠券，导致优惠券表数据量暴增。
该表结构示意如下: 主要存放用户领取，使用优惠券的信息。

```sql
CREATE TABLE `t_user_voucher` (
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

## 分库分表的方案

不分库分表了，改为弄2个表，冷热分离的思路。
1个全量表，给统计用，单表一年新增几亿，走的阿里云的adb，性能还好。
1个热点数据表，给业务用，单表在一亿以内，性能勉强。
热点表 通过脚步同步数据给全量表。

## 冷热分离的方案

## 阿里云的drds
https://www.aliyun.com/product/drds