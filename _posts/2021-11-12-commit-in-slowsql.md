---
layout: post
title: 慢sql日志中出现大量Commit语句
categories: [RocketMQ]
tags: [RocketMQ]
---

# 背景
线上报错
```
更新商品时出现 invalid connection 的错误.
```

查看阿里云慢sql日志,发现都是COMMIT语句,看不到sql,如图:
![_config.yml]({{ site.baseurl }}/images/content/20211112-polardb-slowsql-COMMIT.png)

![_config.yml]({{ site.baseurl }}/images/content/20211112-slowsql-COMMIT-IO.png)

查看自动提交配置

![_config.yml]({{ site.baseurl }}/images/content/20211112-autocommit.png)


# 原因猜测 
DB默认是自动提交的，不存在"COMMIT"； 既然是commit，肯定是会话被关闭了自动提交；

但是为什么没有记录对应的语句，而只记录了COMMIT;呢 ？

1.IO不足导致COMMIT出现在SLOW LOG中.
2.业务启用了大事务， 例如BEGIN开启事务后，里面执行了大量的INSERT语句，然后再做COMMIT提交；
  
# 验证 TODO

#  
1.[mysql慢日志都是COMMIT](https://blog.csdn.net/JSWANGCHANG/article/details/108856869)
2.[COMMIT IN SLOW LOG浅析](https://www.modb.pro/db/52829)