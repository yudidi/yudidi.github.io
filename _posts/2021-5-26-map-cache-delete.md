---
layout: post
title: go map相关操作
categories: [go]
tags: [map]
---

# 主要疑问
## 2.delete全部key可以清空整个map吗?
A:内存没有释放。清空只是修改了一个标记，底层内存还是被占用了

## 3.如何真正释放内存？
A: map = nil
这之后坐等垃圾回收器回收就好了。

## 3.1 验证是否释放内存
```
var intMap map[int]int
var cnt = 8192

func main() {
	printMemStats()

	initMap()
	runtime.GC()
	printMemStats()

	log.Println(len(intMap))
	for i := 0; i < cnt; i++ {
		delete(intMap, i)
	}
	log.Println(len(intMap))

	runtime.GC()
	printMemStats()

	intMap = nil
	runtime.GC()
	printMemStats()
}

func initMap() {
	intMap = make(map[int]int, cnt)

	for i := 0; i < cnt; i++ {
		intMap[i] = i
	}
}

func printMemStats() {
	var m runtime.MemStats
	runtime.ReadMemStats(&m)
	log.Printf("Alloc = %v TotalAlloc = %v Sys = %v NumGC = %v\n", m.Alloc/1024, m.TotalAlloc/1024, m.Sys/1024, m.NumGC)
}
//Output
2021/05/26 20:40:31 Alloc = 114 TotalAlloc = 114 Sys = 68290 NumGC = 0
2021/05/26 20:40:31 Alloc = 421 TotalAlloc = 435 Sys = 70016 NumGC = 1
2021/05/26 20:40:31 8192
2021/05/26 20:40:31 0
2021/05/26 20:40:31 Alloc = 421 TotalAlloc = 437 Sys = 70080 NumGC = 2
2021/05/26 20:40:31 Alloc = 109 TotalAlloc = 439 Sys = 70080 NumGC = 3
```

## 3.2 用map做缓存存在内存不释放的问题
如果你用map做缓存，而每次更新只是部分更新，更新的 key 如果偏差比较大，有可能会有内存逐渐增长而不释放的问题。要注意。
或者map

## 4.为什么这么设计:删除时只标记不释放内存,只有置为nil才会释放内存.
我们可以在遍历map的时候删除里面的元素，而且可以删除没有遍历到的元素，为了保证删除了之后遍历不发生异常，才这么设计的吧。

并且删除之后,range不会再访问到删除的元素
比如，我有k1,k2,假设依次遍历
```
func main() {
	m := make(map[int]int)
	m[1] = 1
	m[2] = 2
	for k := range m {
		fmt.Println(len(m))
		delete(m, k)
		delete(m, 2)
		fmt.Println(k, len(m))
	}
}
//Output
2
1 0
```

## 5.map的写入如何覆盖bucket里面的entry？TODO

# 参考
1.[Golang map 如何进行删除操作？](https://blog.cyeam.com/json/2017/11/02/go-map-delete)