---
layout: post
title: MySQL优化实战-冷热分离
categories: [Mysql]
tags: [Mysql]
---

# 背景
目前接手过的业务, 优惠券和广告,都存在单表大表的的情况。

优惠券: 单表5亿+,大几十G。查询操作较多: 发券,领券 都需要进行查询和写入操作。

广告: 单表 5kw+,40G+。 CRUD qps > 1000。

# 参考
1.[MySQL优化实战：冷热分离](https://juejin.cn/post/6999655801929334814#heading-1)