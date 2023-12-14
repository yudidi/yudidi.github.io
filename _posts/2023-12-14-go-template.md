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


* GPT问答

```text
Q:文本变成解析树，需要哪些知识点

A:将文本解析成解析树涉及以下几个知识点：

1. 词法分析（Lexical Analysis）：词法分析是将输入的文本字符串分解成一系列标记（tokens）的过程。在模板解析中，词法分析器会扫描文本并识别出模板语言中的各种标记，如变量、函数、控制结构等。

2. 语法分析（Syntax Analysis）：语法分析是将词法分析得到的标记序列转换为抽象语法树（Abstract Syntax Tree，AST）的过程。在模板解析中，语法分析器会根据模板语言的语法规则，构建出表示模板结构的语法树。

3. 语法规则（Syntax Rules）：模板语言具有特定的语法规则，定义了如何组织模板中的标记和表达式，以及它们之间的关系。了解模板语言的语法规则是理解和实现模板解析的关键。

4. 解析器（Parser）：解析器是实现语法分析的程序或算法。它接受词法分析器生成的标记序列，并按照语法规则构建语法树。解析器可以使用递归下降、LL(k)、LR(k) 等不同的算法来进行解析。

5. 解析树（Parse Tree）：解析树是由语法分析器构建的树形结构，它反映了模板文本中标记和表达式之间的层次关系。解析树可以用于后续的模板渲染和执行过程。

理解以上知识点可以帮助你理解模板解析的工作原理，并在需要时实现自己的模板解析器。在 Go 语言中，`text/template` 包已经提供了完整的模板解析和执行功能，可以直接使用其中的解析器来将文本解析成解析树。
```

1.2 execute

就是把数据填充到模版树的节点中去

2.组合嵌套模版

# 参考

1.[golang的一些ssti模板注入问题](https://zhuanlan.zhihu.com/p/628060790)