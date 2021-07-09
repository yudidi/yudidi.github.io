---
layout: post
title: 深入理解panic and recover
---

1.如果协程A发生了panic，协程B是否会因为协程A的panic而挂掉？
2.如果协程A发生了panic，协程B是否能用recover捕获到协程A的panic？

https://segmentfault.com/a/1190000023691221

# 了解了以上事实，容易误判下列例子
```
func main() {
 runtime.GOMAXPROCS(1)
 go func() {
  fmt.Println("2")
  panic("hello goroutine")
 }()
 fmt.Println("1")
 sum := 0
 for i := 0; i < 666666666; i++ {
  sum += i
 }
 fmt.Printf("sum: %d\n", sum)
}
```

执行结果
![_config.yml]({{ site.baseurl }}/images/content/panic.png)

原因
卧槽，说明main携程执行太快了。。。
因为就1个cpu啊，只能执行一个内核线程。
一个内核线程(GPM的M)某个时刻只能执行一个携程。
子携程还没来得及启动，main就完事退出了。

# 参考
2.[Golang: 深入理解panic and recover](https://ieevee.com/tech/2017/11/23/go-panic.html)