---
layout: post
title: 给1个sql,问如何加索引
---

Q: select count(1) form table where a = 1 and (b=2 or c = 3) and d = 4;
大佬们，这个sql应该咋加索引

A: a和d 联合索引

看区分度吧 a和d如果区分度都高的话，就加联合索引，如果某个值区分度不高就不加

a 只有1和2 d只有1

那还加毛