---
layout: post
title: 为什么会精度丢失？
---

# 背景
```go

```
f := float64(2.9) + float64(1.11) - float64(4.01)
 fmt.Println(f)

线上结果是float64(-4.440892098500626e-16)，本地是0

## 原因

和编译器有关系

同样的操作 你在mac下面和linux下面可能数据不一样
 
# 

# 处理方案

# 附录
1.[为什么会精度丢失？教你看懂 IEEE-754](https://segmentfault.com/a/1190000024578628)
