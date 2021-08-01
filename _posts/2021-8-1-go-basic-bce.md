---
layout: post
title: Go-去掉BCE(边界检查),提高运行时的效率
---

# 背景
```
Q13 如何判断 2 个字符串切片（slice) 是相等的？
针对这个中的b = b[:len(a)]，使用go 1.15.6编译，已经没有效果了，在len判断的时候就已经做了优化
```

# 遍历slice时处理越界的一个小技巧
```go
b = b[:len(a)] // 这种写法是为了优化边界检查从而提升运行时效率的。在go语言中称之为 Bounds Check Elimination，简称为 BCE。

_ = b[:len(a)]

针对这个中的b = b[:len(a)]，使用go 1.15.6编译，已经没有效果了，在len判断的时候就已经做了优化
```

# 验证

```go
package main

func StringSliceEqualBCE(a, b []string) bool {
	if len(a) != len(b) {
		return false
	}
	if (a == nil) != (b == nil) {
		return false
	}
	b = b[:len(a)]
	for i, v := range a {
		if v != b[i] {
			return false
		}
	}
	return true
}

func f(s []int) {
	_ = s[2] // 只有这里有边界检查
	_ = s[1]
	_ = s[0]
}

func main() {
	StringSliceEqualBCE([]string{}, []string{})
	f([]int{})
}
```

```bash
(base) Didis-MacBook-Pro-4:BCE-边界检查 didiyu$ go build -gcflags="-d=ssa/check_bce/debug=1" main.go
# command-line-arguments
./main.go:20:7: Found IsInBounds
```


[Q13 如何判断 2 个字符串切片（slice) 是相等的？](https://geektutu.com/post/qa-golang-1.html)