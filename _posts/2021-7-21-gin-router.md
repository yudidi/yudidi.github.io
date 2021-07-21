---
layout: post
title: gin-router代码阅读
---

# 背景
今天遇到一个需求

我: gin怎么匹配这种路由: 域名/xxx-notify 和 域名/yyy-notify, 可以提取里面的xxx,yyy

群友A: 把xxx，yyy作为参数

我: url已经约定了，不能改

群友B: 可以获取到xxx-notify,然后自己按-分割出xxx
```go
package main

import (
    "net/http"
    "strings"

    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    r.GET("/user/:name/*action", func(c *gin.Context) {
        name := c.Param("name")
        action := c.Param("action")
        //截取/
        action = strings.Trim(action, "/")
        c.String(http.StatusOK, name+" is "+action)
    })
    //默认为监听8080端口
    r.Run(":8000")
}
```

我: 试了，确实可以，牛皮

```go
package main

import (
	"fmt"
	"net/http"
	"strings"

	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()
	r.GET("/adds/*-notify", func(c *gin.Context) {
		action := c.Param("-notify")
		action = strings.Trim(action, "/")
		ss := strings.Split(action, "-")
		fmt.Println("渠道信息",ss[0])
		c.String(http.StatusOK, "%+v", ss)
	})
	//默认为监听8080端口
	r.Run(":8000")
}
```

我: c.Param这里是咋匹配出-notify的结果的呢[捂脸]

群友B: 啥意思

我: 没太明白gin的前缀树是咋匹配到/weibo-notify这部分的。

# TODO 具体解析
前缀树代码有的难，一时不好看,尤其是通配符`:`和`*`的处理

# 参考
1.[gin的路由就是基于这个写的](github.com/julienschmidt/httprouter)