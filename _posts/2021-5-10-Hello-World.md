---
layout: post
title: Github+Jekyll搭建个人博客(不需要服务器和域名,感恩Github)
---

1.[按照这个博客说的做](https://www.jianshu.com/p/95646037acdc/)

2.[进一步使用看官方文档](https://jekyllrb.com/docs/posts/)

# 遇到的问题

## Q:配置图片
1.相对路径访问仓库的图片

```c

![_config.yml]({{ site.baseurl }}/images/jekyll-logo2.png)

```
效果:
![_config.yml]({{ site.baseurl }}/images/jekyll-logo2.png)

2.绝对路径访问外站图片

```c
<iframe id="embed_dom" name="embed_dom" frameborder="0" style="display:block;width:1000px; height:600px;" src="https://www.processon.com/embed/606685cbe0b34d28298f2b03"></iframe>
```

<iframe id="embed_dom" name="embed_dom" frameborder="0" style="display:block;width:1000px; height:600px;" src="https://www.processon.com/embed/606685cbe0b34d28298f2b03"></iframe>


