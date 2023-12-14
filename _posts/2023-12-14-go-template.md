---
layout: post
title: golang模版引擎
categories: [go]
tags: [template]
---

# 使用场景

1. cms系统的自定义标签: 自定义网站标签,渲染网站模版

# 例子

```go
package main

import (
	"net/http"
	"text/template"
)

type DataForTpl struct {
	Id   int
	Name string
}

// 模板自定义函数及管道  https://www.lesscode.work/sections/62ac21319741b.html
func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		// 定义函数
		// say := template.FuncMap{"say": func(message string) string {
		// 	return "say x : " + message
		// }}
		// say2 := template.FuncMap{"say2": func(data DataForTpl) string {
		// 	return "say2 y: " + data.Name
		// }}
		say := template.FuncMap{
			"say": func(message string) string {
				return "say x : " + message
			},
			"say2": func(data DataForTpl) string {
				return "say2 y: " + data.Name
			}}
		t := template.New("index.html")
		t.Funcs(say)
		// t.Funcs(say2)
		// t.ParseFiles()
		_, _ = t.Parse(`<!DOCTYPE html>
<html lang="en">
<head>
</head>
<body>
<!--理解函数的对象需要和传入的参数匹配-->
<div>hi,我是文本模版...{{say .Name}}</div>
<div>hi...{{say2 .}}</div>
</body>
</html>`)
		// 传入数据
		err := t.Execute(w, DataForTpl{
			Id:   431,
			Name: "test..",
		})
		if err != nil {
			println(err.Error())
		}
	})
	http.ListenAndServe(":8088", nil)
}

```

# 技术点

1.模版渲染过程 parse和execute

1.1 parse

就是把待解析的文本,解析为树的形式,并且拆分为几个节点.
这里的例子,把文本拆分为了3个文本节点(textNode)和2个执行节点(actionNode).

![_config.yml]({{ site.baseurl }}/images/content/20231214-142816-go-tpl.png)


1.2 execute

就是把数据填充到模版树的节点中去

2.组合嵌套模版

# 参考

1.[golang的一些ssti模板注入问题](https://zhuanlan.zhihu.com/p/628060790)