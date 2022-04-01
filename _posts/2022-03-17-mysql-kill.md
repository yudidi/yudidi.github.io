---
layout: post
title: MySQL中的kill命令，你用过吗？
categories: [mysql]
tags: [mysql kill]
---

# 背景
在MySQL中，kill命令分为如下两种：

1、kill query + pid

2、kill connection + pid 其中connection可以省略

# information_schema.INNODB_TRX列解析
// 附录2
```sql
SELECT * FROM information_schema.INNODB_TRX\G
```

# kill后回滚整个过程解析
// 附录2
查看innodb的事务表，发现刚才kill的语句正在进行回滚操作。
trx_rows_modified 代表锁影响的行数，当数值为0时，锁将会释放。

```sql
mysql> SELECT * FROM information_schema.INNODB_TRX\G
*************************** 1. row ***************************
                    trx_id: 71735
                 trx_state: ROLLING BACK
               trx_started: 2021-06-03 14:17:40
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 462200354
       trx_mysql_thread_id: 8
                 trx_query: create table fact_sale_new as select * from fact_sale
       trx_operation_state: rollback of SQL statement
       ///////////略
```

# 参考
1.[MySQL中的kill命令，你用过吗？](https://cloud.tencent.com/developer/article/1634259)
2.[MySQL kill进程后出现killed](https://www.modb.pro/db/69243)
    information_schema.INNODB_TRX列解析
    kill后回滚整个过程解析