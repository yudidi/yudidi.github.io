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

# 2021-06-06更新
博客加入评论系统,具体步骤完全参考这个博客[在Jekyll博客添加评论系统：gitment篇](https://jacobpan3g.github.io/cn/2017/07/17/gitment-in-jekyll/#2-%E5%9C%A8jekyll%E5%8D%9A%E5%AE%A2%E8%B0%83%E7%94%A8gitment),直接生效.
两点注意:
1.新注册一个github小号.
2.生成github oauth的"Authorization callback URL",填自己博客路径即可,比如我填写的https://yudidi.github.io/

![_config.yml]({{ site.baseurl }}/images/content/blog_issues/.png)

第一个成功的评论:
https://yudidi.github.io/pprof-heap-alloc/