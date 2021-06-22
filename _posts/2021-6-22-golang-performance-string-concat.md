---
layout: post
title: insert死锁场景分析 1. duplicate key error引发的死锁
---

# 参考
[mysql insert锁机制(insert死锁)](https://blog.csdn.net/varyall/article/details/80219459)

1.insert操作会加X锁

Q:为什么insert要加X锁
A:这里的x锁是行锁，如果不加，那岂不是能插入两条id为6的值

2.insert操作如果发生重复唯一键冲突,则不会加X锁,而是加S锁

这里理解错误了: select * from information_schema.innodb_locks;里面获取的是准备获取但是还没有获取到的哪些锁.

![_config.yml]({{ site.baseurl }}/images/content/innodb_locks.png)


3. X锁和S锁是兼容的吗?


| **T1(36727)**                            | **T2(36728)**                                            | **T3(36729)**                                                                          |
|------------------------------------------|----------------------------------------------------------|----------------------------------------------------------------------------------------|
| begin;                                   | begin;                                                   | begin;                                                                                 |
| insert into aa values(6, ‘test’, 12, 3); |                                                          |                                                                                        |
|                                          | insert into aa values(6, ‘test’, 12, 3);(等待t1释放X锁,等待加S锁) |                                                                                        |
|                                          |                                                          | insert into aa values(6, ‘test’, 12, 3);(等待t1释放X锁,等待加S锁)                               |
| rollback;                                |                                                          |                                                                                        |
|                                          |                                                          | ERROR 1213 (40001): Deadlock found when trying to get lock; try restarting transaction |
|                                          | Query OK, 1 row affected (13.10 sec)                     |                                                                                        |

[MySQL · 引擎特性 · InnoDB隐式锁功能解析](http://mysql.taobao.org/monthly/2020/09/06/)
```
只有在特殊情况下，才会将隐式锁转换为显示锁。
这个转换动作并不是加隐式锁的线程自发去做的，而是其他存在行数据冲突的线程去做的。
例如事务1插入记录且未提交，此时事务2尝试对该记录加锁，那么事务2必须先判断记录上保存的事务id是否活跃，如果活跃则帮助事务1建立一个锁对象，而事务2自身进入等待事务1的状态，可以参考如下例子：
```

# INNODB_LOCKS 和 data_locks

* 表的功能
The INNODB_LOCKS table contains information about each lock that an InnoDB transaction has requested but not yet acquired, and each lock that a transaction holds that is blocking another transaction.

INNODB_LOCKS表主要包含了InnoDB事务锁的具体情况，包括事务正在申请加的锁和导致其他事务阻塞的锁。
不是已经获取到的锁。

* [INNODB_LOCKS 被 data_locks替代](https://dev.mysql.com/doc/refman/8.0/en/information-schema-innodb-locks-table.html)
This(INNODB_LOCKS) table is deprecated and is removed as of MySQL 8.0.1. Use the Performance Schema data_locks table instead. See Section 27.12.13.1, “The data_locks Table”.


# 看着有点迷糊，自己动手试试

CREATE TABLE `aa` (
`id` int( 10) unsigned NOT NULL COMMENT '主键',
`name` varchar( 20) NOT NULL DEFAULT '' COMMENT '姓名',
`age` int( 11) NOT NULL DEFAULT '0' COMMENT '年龄',
`stage` int( 11) NOT NULL DEFAULT '0' COMMENT '关卡数',
PRIMARY KEY ( `id`),
UNIQUE KEY `udx_name` ( `name`),
KEY `idx_stage` ( `stage`)
) ENGINE= InnoDB DEFAULT CHARSET=utf8;