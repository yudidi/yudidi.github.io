---
layout: post
title: 工作中遇到的问题-如何避免长事务对业务的影响？
categories: [mysql]
tags: [mysql undo log]
---

# 其次，从数据库端来看： 【TODO】都值得试一试
1.监控 information_schema.Innodb_trx 表，设置长事务阈值，超过就报警 / 或者 kill；

2.Percona 的 pt-kill 这个工具不错，推荐使用； // 附录2,3

pt-kill字面意思就是: kill掉MySQL满足某些特征的query语句

3.在业务功能测试阶段要求输出所有的 general_log，分析日志行为提前发现问题；【TODO】工作中试下

4.如果使用的是 MySQL 5.6 或者更新版本，把 innodb_undo_tablespaces 设置成 2（或更大的值）。
如果真的出现大事务导致回滚段过大，这样设置后清理起来更方便。 // 附录5 【TODO】

# 参考
1.[如何避免长事务对业务的影响？](https://time.geekbang.org/column/article/69236)

2.[pt-tools系列：pt-kill 官方使用文档](https://developer.aliyun.com/article/672763)

3.[Percona-Toolkit系列之pt-kill杀会话利器](https://www.fordba.com/percona-toolkit-pt-kill.html)

4.[general_log]()

5.[innodb_undo_tablespaces的含义。undolog表空间清理](https://www.google.com/search?q=innodb_undo_tablespaces+%E4%BD%BF%E7%94%A8%E6%B8%85%E7%90%86)
执行一个长事务，可以肉眼观察到undolog日志文件的膨胀。