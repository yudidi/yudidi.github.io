---
layout: post
title: Code Review - 如何对slice赋值
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

# slice作为参数传递,在函数中进行修改,函数返回后,修改能否保留?
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

# 参考
1.[Golang 新手可能会踩的 50 个坑--12. Array|slice的值作为函数参数](https://segmentfault.com/a/1190000013739000)