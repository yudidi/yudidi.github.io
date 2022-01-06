---
layout: post
title: 【同事事故复盘】alter table修改枚举值导致数据被删除
categories: [es]
tags: [es]
---

# 背景
今天有同事整了个事故:

alter table修改枚举值，比如从a,b,c修改为 d,f,e，那么会删除掉旧的a,b,c的记录。

我看是同事写错了字段，把枚举字段A写成了另一个枚举字段B，然后B的枚举值，就覆盖到A字段了，相当字段A的枚举值被删除。


我一直以为alter table不会导致数据删除来着，同事的这个事故倒是弥补了我的盲区。

# 参考
1.[MySQL精选 | 枚举类型ENUM的DDL变更测试-f. 修改枚举类型字段定义，删除某个枚举元素](https://cloud.tencent.com/developer/article/1426979)