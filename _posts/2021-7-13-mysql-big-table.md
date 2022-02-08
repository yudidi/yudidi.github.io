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
2021-08-11更新: TODO 从Java生态中了解到了一些组件,然后在Go生态中查找了相关资料.
[分库分表实践-Sharding-JDBC](https://www.cnblogs.com/laoyeye/p/12439252.html)
[mysql sharding 官方_GitHub - endink/go-sharding: Mysql 分库分表中间件](https://blog.csdn.net/weixin_39548541/article/details/113594049)
[小米开源数据库中间件Gaea实战（有彩蛋）](https://dbaplus.cn/news-11-2645-1.html)

## 2022-02-08更新:分库分表的步骤 
// 附录11
我在公司内部一直奉承的重构思路:步子大了小心扯着蛋.

1.搜集sql,得出分库,分表或分区键
2.如果要水平分表，需要寻找自增id的替代。这种情况可以先去掉数据库中自增ID，为分片和后面的数据迁移工作提前做准备。 // 小步前进
3.双写
4.mysql或polardb-x

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
A(我): 还在调研,暂未考虑。

## Q: 全量表三年以后不是要有50亿,30亿数据还怎么查?
A: 估计分库分表我要重新考虑下,在多个群被质疑了


# 今年又进行了表数据的清理
因为今年升级了mysql8.0
// 附录3

# 表碎片清理
附录4,5
查看碎片率
```sql
SELECT CONCAT(table_schema, '.', table_name)                   AS  TABLE_NAME
      ,engine                                                  AS  TABLE_ENGINE 
      ,table_type                                              AS  TABLE_TYPE
      ,table_rows                                              AS  TABLE_ROWS
      ,CONCAT(ROUND(data_length  / ( 1024 * 1024), 2), 'M')    AS  TB_DATA_SIZE 
      ,CONCAT(ROUND(index_length / ( 1024 * 1024), 2), 'M')    AS  TB_IDX_SIZE 
      ,CONCAT(ROUND((data_length + index_length ) 
            / ( 1024 * 1024 ), 2), 'M')                        AS  TOTAL_SIZE
      ,CASE WHEN  data_length =0 THEN 0
            ELSE  ROUND(index_length / data_length, 2) END     AS  TB_INDX_RATE
    ,CONCAT(ROUND( data_free / 1024 / 1024,2), 'MB')           AS  TB_DATA_FREE 
    ,CASE WHEN (data_length + index_length) = 0 THEN 0
             ELSE ROUND(data_free/(data_length + index_length),2) 
     END                                                       AS  TB_FRAG_RATE
FROM information_schema.TABLES  
ORDER BY data_free DESC;
```

# 分库分表再提
// 附录6
业务增长,数据量增长,准备做一些分库分表的事情


# PolarDB-X如何解决不走分表键值对导致全表扫描的问题
// 附录8,9
mysql单索引下二级索引的原理:

分布式二级索引原理:

# 附录
1.[MySQL查看数据库表容量大小-3.查看指定数据库容量大小](https://blog.csdn.net/fdipzone/article/details/80144166)

2.[首页 > PolarDB-X 云原生分布式数据库 > 技术白皮书 > 技术原理](https://help.aliyun.com/document_detail/261147.html?spm=a2c4g.11186623.2.8.285b7942kkETra)

3.[MySQL8.0的information_schema.tables信息不准确怎么办](https://blog.csdn.net/w892824196/article/details/103973377)

4.[mysql - information_schema.TABLES.DATA_FREE 在 MySQL 中是什么意思？](https://www.coder.work/article/145964)

5.[MySQL表碎片化（Table Fragmentation）的原因]()

6.[超详细的mysql分库分表方案](https://blog.csdn.net/agonie201218/article/details/110823552)

7.[淘宝双11达到百亿级数据分表后，怎么实现分页查询？](https://blog.csdn.net/BF02jgtRS00XKtCx/article/details/109634540)

8.[PolarDB-X-全局二级索引.基本原理](https://help.aliyun.com/document_detail/182179.html)

9.[PolarDB-X什么样的技术，才能让你“忘掉”分区键这个东西呢。-二级索引与分区键](https://zhuanlan.zhihu.com/p/440801781)

10.[PolarDB-X-分库分表子句和参数](https://help.aliyun.com/document_detail/316575.html#title-663-dqa-9a1)

11.[分库分表带来的问题-我们的系统真的需要分库分表吗？-这种情况可以先去掉数据库中自增ID，为分片和后面的数据迁移工作提前做准备](https://www.cnblogs.com/wade-luffy/p/6096578.html)
