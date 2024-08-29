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
Q1:文本变成解析树，需要哪些知识点

A:将文本解析成解析树涉及以下几个知识点：

1. 词法分析（Lexical Analysis）：词法分析是将输入的文本字符串分解成一系列标记（tokens）的过程。在模板解析中，词法分析器会扫描文本并识别出模板语言中的各种标记，如变量、函数、控制结构等。

2. 语法分析（Syntax Analysis）：语法分析是将词法分析得到的标记序列转换为抽象语法树（Abstract Syntax Tree，AST）的过程。在模板解析中，语法分析器会根据模板语言的语法规则，构建出表示模板结构的语法树。

3. 语法规则（Syntax Rules）：模板语言具有特定的语法规则，定义了如何组织模板中的标记和表达式，以及它们之间的关系。了解模板语言的语法规则是理解和实现模板解析的关键。

4. 解析器（Parser）：解析器是实现语法分析的程序或算法。它接受词法分析器生成的标记序列，并按照语法规则构建语法树。解析器可以使用递归下降、LL(k)、LR(k) 等不同的算法来进行解析。

5. 解析树（Parse Tree）：解析树是由语法分析器构建的树形结构，它反映了模板文本中标记和表达式之间的层次关系。解析树可以用于后续的模板渲染和执行过程。

理解以上知识点可以帮助你理解模板解析的工作原理，并在需要时实现自己的模板解析器。在 Go 语言中，`text/template` 包已经提供了完整的模板解析和执行功能，可以直接使用其中的解析器来将文本解析成解析树。


Q2:上面的parse就经过了这几个步骤，是吗

A:
是的，上述的 `parse` 是 `text/template` 包中的一个类型，它代表了模板的解析树。在 Go 语言中，使用 `text/template` 包来进行模板解析时，以下步骤会被自动完成：

1. 词法分析：`text/template` 包内部包含了词法分析器，它会将输入的模板文本进行词法分析，将文本分解成一系列的标记(token)。

2. 语法分析：`text/template` 包内部的解析器会根据模板语言的语法规则，接收词法分析器生成的标记序列，并构建出表示模板结构的解析树。

3. 构建解析树：解析器会按照语法规则逐步解析标记序列，并构建出解析树的节点和层次结构。解析树的节点表示模板中的标记、表达式和控制结构等。

4. 解析错误处理：如果在解析过程中遇到语法错误，解析器会报告错误并提供相应的错误信息，以帮助开发者修复模板中的问题。

最终，`parse` 对象会保存整个模板的解析结果，包括解析树和相关的元数据。通过解析树，可以在后续的模板渲染过程中进行变量替换、执行控制结构等操作，从而生成最终的输出文本。


Q3: 那接下来的execute，又是怎么把数据渲染到解析树中的呢


A:在 `text/template` 包中，`Execute` 函数用于将数据渲染到解析树中生成最终的输出文本。执行过程如下：

1. 创建模板对象：首先，你需要创建一个模板对象。可以使用 `template.New` 或 `template.Must` 函数来创建一个模板对象。例如：`tmpl := template.Must(template.New("myTemplate").Parse(templateText))`。

2. 准备数据：将要渲染到模板中的数据准备好，并将其传递给 `Execute` 函数。数据可以是任意类型的值，可以是单个值，也可以是结构体、映射等复杂类型。

3. 执行渲染：调用模板对象的 `Execute` 方法，将数据作为参数传递进去。例如：`err := tmpl.Execute(writer, data)`。其中，`writer` 是一个实现了 `io.Writer` 接口的对象，用于接收渲染后的输出结果。

4. 渲染过程：`Execute` 函数会按照解析树中定义的规则，遍历解析树的节点，并根据节点的类型执行相应的操作。对于变量节点，会从数据中检索对应的值；对于控制结构节点（如循环、条件语句等），会根据条件判断是否执行相应的代码块。

5. 输出结果：渲染过程会将最终的输出结果写入到 `writer` 中，可以通过该对象获取渲染后的输出文本。

通过以上步骤，模板中的标记和表达式会被替换为数据中的对应值，控制结构会根据条件进行相应的执行，最终生成渲染后的输出文本。

需要注意的是，在实际使用过程中，还可以使用管道操作符 `|` 和自定义函数来对数据进行加工处理，以满足更复杂的渲染需求。同时，还可以使用模板语言中的控制结构、循环语句等功能来实现动态的输出文本。

```

1.2 execute

就是把数据填充到模版树的节点中去

2.组合嵌套模版

# 参考

1.[golang的一些ssti模板注入问题](https://zhuanlan.zhihu.com/p/628060790)