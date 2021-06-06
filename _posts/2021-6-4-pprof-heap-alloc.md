---
layout: post
title: --inuse/alloc_space --inuse/alloc_objects区别
---

通常情况下
用--inuse_space来分析程序常驻内存的占用情况;
用--alloc_objects来分析内存的临时分配情况，可以提高程序的运行速度。

# 命令
弹出url
http://localhost:8082/ui/?si=inuse_space
http://localhost:8082/ui/?si=alloc_objects

# 定位过程
go tool pprof --alloc_objects pprof.product_server.alloc_objects.alloc_space.inuse_objects.inuse_space.001.pb.gz 
top
list 比较大的函数名
查看该函数哪一行分配内存比较多。

# 需要频繁分配内存的情况
1.log日志打印。
```
aplum_product/src/product_server/models/db.(*TProduct).SetActivityRule
/Users/didiyu/Downloads/go/src/aplum_product_clear_micro/src/product_server/models/db/set_discount_product.go
  Total:     8490569   22018638 (flat, cum)  5.25%
    179            .          .           func (p *TProduct) SetActivityRule(curDiscount float64, whitelist map[string]bool) { 
    180            .          .           ConfigLoop: 
    181            .          .           	for i, config := range p.ActivityConfigs { 
    182      5996623    5996623                 log.Debug("%v conf %v,%v", i, config.ID, config.Name) 
```
2.split方法

# 参考
1.[--inuse/alloc_space --inuse/alloc_objects区别](https://lrita.github.io/2017/05/26/golang-memory-pprof/#--inusealloc_space---inusealloc_objects%E5%8C%BA%E5%88%AB)