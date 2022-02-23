---
layout: post
title: 关于新同事接手新项目的一点思考
categories: [如何接手新项目]
tags: [如何接手新项目]
---

# 从内聚性的角度思考为什么项目代码中要有某些对象,这些对象为什么要有这些属性和方法

Q:从内聚性的角度思考为什么要有ProductInfo对象,这个对象应该有哪些属性和行为(方法),为什么这些属性和方法应该放在这一个类中? // 对象+交互

```
r, err = (&models.ProductInfo{
        ProductId: product_id,
        UserId:       user_id,
        TmNow:        tm_now,
        IsFirstOrder: firstOrder}).
        GetProductInfo(models.DetailFormat)
```