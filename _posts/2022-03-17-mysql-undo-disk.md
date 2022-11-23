---
layout: post
title: update导致undolog膨胀,mysql磁盘占用膨胀 && kill不掉的语句
categories: [mysql]
tags: [mysql undo_log]
---

# 背景

```sql
update t_user_voucher set seller_support=0 where id in(select id from tmp_muvids_0311) limit 1;
```

最近同事在做重构,涉及数据迁移和数据修复,线上执行了这个语句。

阿里云就收到了报警:

```bash
云数据库RDS版 发生告警
时间: 15:13
实例名称: 线上mysql_8
实例详情: xxxx
监控指标: 磁盘使用率
报警条件: （80.99>80）
持续时间: 8小时4分钟
```

原因定位过程

1.最开始是以为有什么上线，导致数据增长比较多。所以问了下3个业务组最近有哪些上线。结果没有上上线。

2. 因为我们线上是一主三从，然后一个同事发现, 只有主库报警，从库没有报警，主库磁盘占用 比 从库磁盘占用 多了60G。

3. 然后阿里云提了工单，给我们看了mysql8数据库的数据库文件相关的信息
```bash
// 我做了一定处理
库	269G	no	
.库/表.ibd	14G
.库/表.ibd	13G
```

## undo log存放的位置是 TODO
A: 好像是在.idb文件中???
```sql
show variables like 'innodb_file_per_table';
说明线上为独占表空间

同时发现
show variables like 'innodb_undo_tablespaces';
```

然后参数`innodb_max_undo_log_size`为1073741824。

# 如何处理巨大的回滚段? TODO

## 我们执行kill语句,发现不能kill。
// 参考附录3
![_config.yml]({{ site.baseurl }}/images/content/20220317-为什么发送了kill信号但是不能马上执行kill(因为没走到检查信号的埋点).png)

# 参考
1.[03 | 事务隔离：为什么你改了我还看不见?](https://time.geekbang.org/column/article/68963)

  1.1 回滚日志总不能一直保留吧，什么时候删除呢？

  1.2 回滚段被清理，文件也不会变小? TODO 这是为啥

  1.3 基于上面的说明，我们来讨论一下为什么建议你尽量不要使用长事务。
      长事务意味着系统里面会存在很老的事务视图。由于这些事务随时可能访问数据库里面的任何数据，所以这个事务提交之前，数据库里面它可能用到的回滚记录都必须保留，这就会导致大量占用存储空间。

2.[参考我之前博客](https://yudidi.github.io/articles/2021-06-23/mysql-long-transaction-disadvantage)

3.[MySQL UNDO表空间独立和截断](http://blog.itpub.net/25583515/viewspace-2719969/)

4.[万答#18，MySQL8.0 如何快速回收膨胀的UNDO表空间](https://www.1024sou.com/article/624560.html)

5.[32 | 为什么还有kill不掉的语句？](https://time.geekbang.org/column/article/79026)
   所以，如果你发现一个线程处于 Killed 状态，你可以做的事情就是，通过影响系统环境，让这个 Killed 状态尽快结束。