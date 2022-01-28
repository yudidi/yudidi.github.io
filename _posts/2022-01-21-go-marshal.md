---
layout: post
title: go版本对unmarshal的支持不同
categories: [go]
tags: [go]
---

# 背景


# 参考
大家使用工具生成 gorm结构体的时候，需要注意一下，生成的tag，多了一个primary_key字段；

比如：【ID          int32  `gorm:"column:id;primary_key" json:"id;primary_key"`】

go1.13 使用json marshal 的结果 与 go1.16 使用marshal 的结果 不一致，

开发 & 预发布是1.16， 一些旧的服务是编译用的 1.13（做需求时，需要尽快升级），混用的时候会出问题
升级两部
a 变更容器ID
b build.sh 里 go build之前添加 go mod tidy

比如：预发布用1.16，通过json生成的缓存，在线上用1.13编译的服务读取这个缓存时是有问题的