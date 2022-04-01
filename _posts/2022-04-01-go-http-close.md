---
layout: post
title: golang标准库http的client为什么必须手动关闭resp.Body
categories: [go]
tags: [go http]
---

# 背景
对为什么要执行下面的语句有些忘了，这里记录一下。

```go
defer resp.Body.Close()
```

# 参考
[golang标准库http的client为什么必须手动关闭resp.Body](https://blog.csdn.net/bdss58/article/details/102225632)