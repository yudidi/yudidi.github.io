---
layout: post
title: 阿里云RDS(mysql)慢sql-自动化报警脚本
---

# 背景
公司的mysql数据库是购买的阿里云RDS,1主4从,只有1个主库(读写),其中1个从库只用于统计业务,部分业务只需要读取从库。
比如我负责的搜索业务只需读从库.

# 问题
各个业务先共享这1个主库，会导致以下问题:
1.主库或从库如果被慢sql把cpu打满100%,会影响公司的所有主库操作。
比如APP首页无法打开,出现乱码等严重问题。
2.慢sql不好定位. 因为我们公司的慢sql相对较多,主要后台管理有相当多的复杂sql(几百行),这样平时就有一定数量的慢sql了.
于是最近新写的慢sql和既有的慢sql混杂在一起,不是很好排查。
目前遇到慢sql打满cpu导致首页不能访问时,我们都是由技术总监在阿里云后台不停的杀死目前执行的全部慢sql,直到首页恢复访问才停止。

这样,基本上从发现到恢复都要10多分钟,不仅吓人,也劳心劳力.
经过搜索一些资料,考虑讲刚刚的过程进行一定脚本自动化。

# 自动化脚本思路
从`information_schema.PROCESSLIST`查询出执行时间超过10s的sql,然后发送钉钉报警,这样加快慢sql的定位速度.
```
SELECT * FROM `information_schema`.`PROCESSLIST` Where COMMAND = 'Query' AND Time >= 10;
```

# 后续改进
1.这个脚本只是发现sql,并没有自动kill掉这个慢sql。

# 参考
1.[MySQL之查询截取分析（慢查询日志等 ）](https://www.huaweicloud.com/articles/206e8f5e82331018f8cfde939455c319.html)
2.[MySQL 锁表后快速解决方法 及 MySQL中的锁](http://weikeqin.com/2019/09/05/mysql-lock-table-solution/)