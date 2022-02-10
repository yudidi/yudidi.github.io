---
layout: post
title: mysql-sql.DB-SetMaxOpenConns和SetMaxIdleConns,SetConnMaxLifetime
---

# 背景
线上一个定时任务,使用gorm框架,读写阿里云RDS,在创建连接和udpate操作中时不时出现这个错误

```cmd
[mysql] 2021/08/01 18:30:31 packets.go:37: read tcp 172.17.0.3:48356->172.16.0.25:3306: i/o timeout
panic: 定时改价任务报警: invalid connection; invalid connection,update price,pid:6809290
```

# 从附录1可以学到
1.基准测试验证不同配置对性能的影响

2.MaxIdleConns应该始终小于或等于MaxOpenConns.

Q:空闲连接是否可以设置得很大 TODO

A:
* 空闲连接多的好处
理论上因为空闲连接节省了新新连接的时间,有利于提高性能,从这个角度看,空闲连接越多越好。
* 空闲连接过多的坏处
1.空闲连接占用内存
2.如果一个连接空闲太久，那么它也可能会变得不可用。
因此，将MaxIdleConns设置得太高实际上可能会导致连接变得不可用，并且使用的资源比使用较小的空闲连接池（使用的连接更少，使用频率更高）的情况下要多。
所以只有你很可能马上再次使用这些连接，你才会保持这些连接空闲。

* 空闲连接设置的准则
配置合适的连接数,使得这些连接可以频繁的被用到.

# 附录
1.[[译]配置 sql.DB 获得更好的性能](https://colobu.com/2020/05/18/configuring-sql-DB-for-better-performance-2020/)