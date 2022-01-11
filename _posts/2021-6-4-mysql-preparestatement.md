---
layout: post
title: 令人激动的一次优化
---

我负责的搜索服务,每次大促前都会进行一些性能优化.
今天开启mysql的PreparedStatement,p95直接从200ms提升到了100ms.吓死我了。

性能优化的惊喜-PreparedStatement的原理

# 启动了PreparedStatement之后,其他服务出现如下报警

我擦，然后就报错了。
Can't create more than max_prepared_stmt_count statements (current value: 16382)
我可以把max_prepared_stmt_count 加满吗

```
"error":"Error 1461: Can't create more than max_prepared_stmt_count statements (current value: 16382)"
```

# 金柱大哥解释
设置的大些，确认下没有拼sql，记得用最近的版本，之前用map的话没排序会产生几条
默认事务关掉性能应该还可以提升

![_config.yml]({{ site.baseurl }}/images/content/gorm-improve.png)

# TODO
preparestatement是基于session做存储，如果session读了，就会有较多的重复preparestatement

# 抓包分析preparestatement
mysql抓包能看到先发包过去编译sql

# 按照附录3查看performance_schema.prepared_statements_instances,发现没有该库,原因:
[云数据库RDS的MySQL版无法查询performance_schema值](https://help.aliyun.com/document_detail/41726.html)


# 查看max_prepared_stmt_count的使用量是否正常
//附录1.1
请确认Com_stmt_close的值是否接近于Com_stmt_prepare 

命令: show global status like 'com_stmt%';
结果:
Com_stmt_close	31552809
Com_stmt_execute	31552885
Com_stmt_fetch	0
Com_stmt_prepare	31552938
Com_stmt_reprepare	0
Com_stmt_reset	0
Com_stmt_send_long_data	0

# 2021-01-11更新
今天升级mysql8。依然出现了如下错误:

```
"error":"Error 1461: Can't create more than max_prepared_stmt_count statements (current value: 16382)"
```

查看阿里云后台主库配置是49146,从库配置还是16382。

问了下运维:只改了主库，忘了改从库了。

设置从库参数即可。

# // 附录4
Com_stmt_prepare 减去 Com_stmt_close 大于 max_prepared_stmt_count 就会出现这种错误。那么我们手动调高max_prepared_stmt_count（取值范围：0 - 1048576，默认16382）即可解决


# 参考
1.[database/sql: Stmt的使用以及坑](https://studygolang.com/articles/1795)

1.1[请确认Com_stmt_close的值是否接近于Com_stmt_prepare -- 一个可能的原因是应用端那边没有关闭prepared的语句](https://blog.csdn.net/vkingnew/article/details/80972823)

2.[PreparedStatement的原理](https://www.geek-share.com/detail/2693287038.html)

[MySQL · 特性分析 · MySQL的预编译功能](http://mysql.taobao.org/monthly/2018/04/07/)  TODO

[](https://www.cnblogs.com/micrari/p/7112781.html)

3.[mysql_stmt含义_Mysql--由prepared sql statement引发的问题](https://blog.csdn.net/weixin_39927623/article/details/114825698)

4.[max_prepared_stmt_count 问题与Sysbench 工具简介](https://zhuanlan.zhihu.com/p/67188414)