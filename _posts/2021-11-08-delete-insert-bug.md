---
layout: post
title: 高并发情况下的业务错误记录(mysql)
categories: [mysql]
tags: [mysql]
---

# 背景
负责的广告下发服务,单机qps>400,遇到1个稍微有点特别的问题.

代码逻辑很简单
```bash
1. 查找是否存在任意一条设备did=x的记录. // select id from t where did = x limit 1;
2. 假设找到id=n的一条记录,则删除这条记录. // delete from t where id = n;
3. 然后插入一条新记录did=x // insert into t(did) values (x);
```

理论上,按照上述逻辑,t表只会存在1条did=x的记录.

但是从线上数据看,存在大量did=x的记录,并且这些记录在各种时间点都存在,没有规律。


# 原因分析

## 预备知识

delete操作是当前读,在mysql内部分为4个步骤执行

1. current read

2. read && lock

3. delete row

4. success


* 参考delete操作在mysql内部的执行流程图

![_config.yml]({{ site.baseurl }}/images/content/20211108mysql-delete.png)

## bug出现过程
在广告被刷量的情况下,单个设备did=x可能会在1秒内,请求1000次.

极端情况,可能1000个携程都执行到了step1,但是只有1个携程(1)成功,其他携程会因为行锁阻塞在step2上。

待携程(1)执行完step2,其他携程依次执行step2(删除不报错,但是RowsAffected=0),step3,step4

所以虽然执行了1000次删除语句,但是只删除了1条记录,但是我们最多可能写了999条记录。
