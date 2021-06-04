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


# 参考
1.[database/sql: Stmt的使用以及坑](https://studygolang.com/articles/1795)