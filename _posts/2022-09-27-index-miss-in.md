---
layout: post 
title: mysql索引失效-IN语句参数过多(线上事故)
categories: [mysql]
tags: [索引失效 线上事故]
---


## case IN语句包含过多的id时,mysql优化器估算走索引比全表扫描慢,从而全表扫描
// 附录4

1. IN底层执行是通过多次执行range范围查询来处理所有IN参数的。随着IN参数过多,mysql优化器估计走索引不比全表扫描成本低,所以走全表扫描.

![_config.yml]({{ site.baseurl }}/images/content/20220926-mysql-index-miss-by-in.png)


2. 系统变量eq_range_index_dive_limit对IN子句的影响

![_config.yml]({{ site.baseurl }}/images/content/20220926-index-miss-in.png)

The optimizer will use existing index statistics instead of doing index dives for equality ranges if the number of equality ranges for the index is larger than or equal to this number. If set to 0, index dives are always used.


# 参考资料

1.[我们都是小青蛙MySQL系列文章.pdf--包含IN语句的执行过程]()
2.TODO [MySQL not using indexes with WHERE IN clause?--IN不走索引比较细致的分析](https://blog.csdn.net/h2453532874/article/details/108455874)
2.1 
3.[MySQL索引原理及慢查询优化](https://tech.meituan.com/2014/06/30/mysql-index.html)
4.[终极版本:美团开发的基于sql自动创建索引的系统方案--基于代价的慢查询优化建议](https://tech.meituan.com/2022/04/21/slow-query-optimized-advice-driven-by-cost-model.html)