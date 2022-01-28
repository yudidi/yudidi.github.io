---
layout: post
title: mysql字符集
categories: [mysql]
tags: [mysql]
---

# 背景
有时需要调整某个表所有字段的字符集和排序规则。

# 
ALTER TABLE xxxx CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci;

# 参考
1.[]()