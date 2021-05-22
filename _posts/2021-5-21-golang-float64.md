---
layout: post
title: golang浮点数判断相等和大小比较的坑
---

1.[golang比较浮点数是否相等](https://www.jianshu.com/p/3d937b539336)

```
const Accuracy = 0.000001

// MIN 为用户自定义的比较精度
func IsFloat64Equal(a, b float64) bool {
	return math.Abs(a-b) < Accuracy
}

func IsFloat64Greater(a, b float64) bool {
	return math.Max(a, b) == a && math.Abs(a-b) > Accuracy
}

func IsFloat64Smaller(a, b float64) bool {
	return math.Max(a, b) == b && math.Abs(a-b) > Accuracy
}

func IsFloat64GreaterOrEqual(a, b float64) bool {
	return math.Max(a, b) == a || math.Abs(a-b) < Accuracy
}

func IsFloat64SmallerOrEqual(a, b float64) bool {
	return math.Max(a, b) == b || math.Abs(a-b) < Accuracy
}
```