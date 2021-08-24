---
layout: post
title: es搜索权重调整的几种方式
---

# 背景
我负责电商的商品搜索,经常遇到这样的需求:当商品参加某个活动或满足某个条件时,希望可以在用户搜索时进行加权,曝光更靠前.

比较常用的实现思路:
1.往es写入1个字段标记是否满足某个条件
2.搜索时对这个字段,进行查询时权重调整. 
2.1 调整字段weight
2.2 将function_score查询与field_value_factor结合使用 // 附录2

# 主要内容
## 1.基于字段的权重调整
1. 在搜索时，可以将function_score查询与field_value_factor结合使用

## 2.其他权重调整

## 控制得分在固定区间
// 附录2

# 附录
1.[我们希望将更受欢迎的博客搜索结果列表中相对较上的位置--在搜索时，可以将function_score查询与field_value_factor结合使用，即将点赞数与全文相关度评分结合：](https://www.elastic.co/guide/cn/elasticsearch/guide/current/boosting-by-popularity.html)
2.[实战|Elasticsearch自定义评分的N种方法-有没有办法让同一个索引里面对固定的查询返回的相关性评分是在固定的范围之内的？比如0-100分这样的？](https://mp.weixin.qq.com/s/npOCOX8tTzk6bv_8n_7njQ)