---
layout: post
title: 普通索引 & 唯一索引 区别
---

# TODO

#
普通索引 & 唯一索引 区别:

select 忽略不计
主要体现在update，普通索引不需要判断唯一性约束
change buffer，准确的说不在buffer中，有实质的差异，普通索引具备加速作用，在写多读少的场景
