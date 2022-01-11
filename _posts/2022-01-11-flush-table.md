---
layout: post
title: FLUSH TABLES WITH READ LOCK
categories: [mysql]
tags: [mysql]
---

# 背景

今天数据团队: 大量抽取主库数据，导致线上部分数据库交互功能无法使用。

以下是同事写的事故报告和经验总结:

* 事故说明:
 
 今天下午14:40从主库同步部分RDS主库数据至CH，支持新需求的开发，但在同步前没有了解CH同步数据的机制，导致数据库不可用。CH同步数据的机制是使用”FLUSH TABLES WITH READ LOCK“，备份工具获取一致性备份(数据与binlog位点匹配)。
 由于FTWRL总共需要持有两把全局的MDL锁，并且还需要关闭所有表对象，此次是这个命令导致库hang住。影响线上数据库交互业务。

* 整改措施:

1、后续应避免从生产读写库同步数据，应从只读备库同步
2、后续在同步数据时需提前了解工具特性，思考同步机制可能带来的影响，并做好测试
3、如必须在生产库同步，应避在业务量最低的时候执行（凌晨4~5点）
4、数据同步方案应与上下游干系人提前沟通，并监控抽取后的一段时间

FLUSH TABLES WITH READ LOCK简称(FTWRL)，该命令主要用于备份工具获取一致性备份(数据与binlog位点匹配)。由于FTWRL总共需要持有两把全局的MDL锁，并且还需要关闭所有表对象，因此这个命令的杀伤性很大，执行命令时容易导致库hang住。如果是主库，则业务无法正常访问；如果是备库，则会导致SQL线程卡住，主备延迟。

# 参考
1.[FLUSH TABLES WITH READ LOCK](https://developer.aliyun.com/article/446698)