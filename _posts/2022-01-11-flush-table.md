---
layout: post
title: FLUSH TABLES WITH READ LOCK
categories: [mysql]
tags: [mysql]
---

# 背景

今天数据团队: 大量抽取主库数据，导致线上部分数据库交互功能无法使用。

以下是同事写的事故报告:

 事故说明:
 
 今天下午14:40从主库同步部分RDS主库数据至CH，支持新需求的开发，但在同步前没有了解CH同步数据的机制，导致数据库不可用。CH同步数据的机制是使用”FLUSH TABLES WITH READ LOCK“，备份工具获取一致性备份(数据与binlog位点匹配)。
 由于FTWRL总共需要持有两把全局的MDL锁，并且还需要关闭所有表对象，此次是这个命令导致库hang住。影响线上数据库交互业务。


FLUSH TABLES WITH READ LOCK简称(FTWRL)，该命令主要用于备份工具获取一致性备份(数据与binlog位点匹配)。由于FTWRL总共需要持有两把全局的MDL锁，并且还需要关闭所有表对象，因此这个命令的杀伤性很大，执行命令时容易导致库hang住。如果是主库，则业务无法正常访问；如果是备库，则会导致SQL线程卡住，主备延迟。

# 参考
1.[FLUSH TABLES WITH READ LOCK](https://developer.aliyun.com/article/446698)