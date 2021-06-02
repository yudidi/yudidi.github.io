---
layout: post
title: Code Review - 如何对slice赋值|slice深拷贝,浅拷贝
---

````
type AttributeValue struct {
	attr       []*proto_product.Attribute // slice被直接赋值
	expireTime int64
}

func (cache *AttributeCache) BatchGetCache(pids []int32) (map[int32][]*proto_product.Attribute, []int32) {
	cache.attributeMutex.RLock()
	defer cache.attributeMutex.RUnlock()
	missIds := []int32{}
	attrList := map[int32][]*proto_product.Attribute{}
	for _, pid := range  pids {
		if attr, ok := cache.attributes[pid]; ok {
			attrList[pid] = attr.attr // slice直接赋值,那么修改attrList[pid]本质上会修改cache.attributes[pid]
		} else {
			missIds = append(missIds, pid)
		}
	}

	return attrList, missIds
}
````

# 参考
## slice作为参数传递,在函数中进行修改,函数返回后,修改能否保留?
Another option is to use slices. 
Even though your function gets a copy of the slice variable it still references the original data.
即使函数内部得到的是 slice 的值拷贝，但依旧会更新 slice 的原始数据（底层 array）
```
func main() {  
    x := []int{1,2,3}

    func(arr []int) {
        arr[0] = 7
        fmt.Println(arr) //prints [7 2 3]
    }(x)

    fmt.Println(x) //prints [7 2 3]
}
```
## 3.1 大量内存得不到释放 TODO
https://geektutu.com/post/hpg-slice.html#3-%E6%80%A7%E8%83%BD%E9%99%B7%E9%98%B1

在已有切片的基础上进行切片，不会创建新的底层数组。因为原来的底层数组没有发生变化，内存会一直占用，直到没有变量引用该数组。
因此很可能出现这么一种情况，原切片由大量的元素构成，但是我们在原切片的基础上切片，虽然只使用了很小一段，但底层数组在内存中仍然占据了大量空间，得不到释放。
比较推荐的做法，使用 copy 替代 re-slice。

# 参考
1.[Golang 新手可能会踩的 50 个坑--12. Array|slice的值作为函数参数](https://segmentfault.com/a/1190000013739000)