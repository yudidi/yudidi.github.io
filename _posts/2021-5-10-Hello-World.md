---
layout: post
title: Github+Jekyll+gitment 搭建个人博客(带评论系统)
---
# 感恩Github,这套系统不需要服务器和域名

# 2021-5-10 首次搭建,没有评论系统
按照附录1和2的博客做,因为这两个博客已经写得很完善了，我就不在赘述了。

# 遇到的问题
## Q:配置图片
1.相对路径访问仓库的图片
```
![_config.yml]({{ site.baseurl }}/images/jekyll-logo2.png)
```
效果:
![_config.yml]({{ site.baseurl }}/images/jekyll-logo2.png)

2.绝对路径访问外站图片

```
<iframe id="embed_dom" name="embed_dom" frameborder="0" style="display:block;width:1000px; height:600px;" src="https://www.processon.com/embed/606685cbe0b34d28298f2b03"></iframe>
```

<iframe id="embed_dom" name="embed_dom" frameborder="0" style="display:block;width:1000px; height:600px;" src="https://www.processon.com/embed/606685cbe0b34d28298f2b03"></iframe>

# 2021-06-06 加入评论系统
具体步骤完全参考附录3,直接生效.

两点注意:

1.需要新注册一个github小号,在小号里注册oauth application,使用小号app的client secret.

2.生成github oauth的"Authorization callback URL",填自己博客路径即可,比如我填写的https://yudidi.github.io/

![_config.yml]({{ site.baseurl }}/images/content/blog_issues.png)

第一个成功的评论:
https://yudidi.github.io/pprof-heap-alloc/

# 2021-08-24 增加导航和分类
// 附录5,6

# 附录
1.[按照这个博客说的做](https://www.jianshu.com/p/95646037acdc/)

2.[进一步使用看官方文档](https://jekyllrb.com/docs/posts/)

3.[在Jekyll博客添加评论系统：gitment篇](https://jacobpan3g.github.io/cn/2017/07/17/gitment-in-jekyll/#2-%E5%9C%A8jekyll%E5%8D%9A%E5%AE%A2%E8%B0%83%E7%94%A8gitment)

4.[本篇博客的源文件在这里,遇到问题可以参考我的源文件和历史提交记录,都是透明开源的](https://github.com/yudidi/yudidi.github.io/blob/master/_posts/2021-5-10-Hello-World.md)

5.[手把手教你使用jekyll正确设置文章分类导航栏](http://yaolingxin.gitee.io/lingxin_jekyll/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/2019/07/02/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0-jekyll.html)

6.[jekyll如何写一个导航栏？](https://www.zhihu.com/question/304995960)
搭建导航遇到一些问题,需要更多的理解Jekyll
[搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)