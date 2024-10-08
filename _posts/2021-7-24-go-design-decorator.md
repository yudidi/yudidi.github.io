---
layout: post
title: 结合工作实践理解-编程模式:修饰器(Golang)
---
# 背景

## 笔者公司和个人背景
我司成立于2016年,前期核心业务主要采用php实现,因为是初创公司且技术人员流动不小,代码质量比较一般。

我司在2020年初抓住了一个互联网风口,业务迎来暴涨,现有系统维护难度越来越大,所以从CEO和CTO层面开始推动代码重构|重写来提升系统可维护性。

我就是这个背景下于2020年4月入职我司。笔者所在技术团队这2年的主要任务是服务化。

入职之后,大致经历了以下阶段:

1.磨合期:现在回想起来,就是和同事互相熟悉性格和工作习惯,便于各自找准角色定位。

2.微框架搭建:调研微服务框架,搭建基础设施(微服务模版,监控,CI/CD,注册中心Consul,Canal,MQ),明确团队的主要职责:基础架构和业务开发,五五开。

3.服务迁移(2020下半年):主要是对周边业务和部分涉及面较小的核心业务进行迁移。用Go对php进行重写。

4.服务重构(2021下半年):对剩余的老大难系统进行重新一定程度的设计和重构。用Go对php进行重写。

围绕这个核心任务，我们团队做了如下事情:

1.搭建微服务开发基本框架. 主要是基于go-micro做一些一些封装,然后在此基础上逐个迁移服务。

2.梳理分析老系统的业务流程和特点,给出相对更好的设计,然后用Go实现,配合业务部门逐渐实现迁移。

在以上工作过程中,我深刻的意思到软件设计的必要性,同时也意识到自己对软件原则和设计模式的应用还比较初级,未能达到得心应手的程度。

为了可以提升自己在应对复杂业务需求时的设计能力,最近在阅读<编程的逻辑-如何用面向对象方法实现复杂业务需求>,同时也在重构部分核心业务,
希望可以结合自身重构经历,再次理解一些常见设计模式的原理,熟悉其应用场景。

# 装饰器模式原理和解决的问题

# 参考
1.[GO编程模式：修饰器](https://coolshell.cn/articles/17929.html)

2.[Go 设计模式之装饰器模式](https://learnku.com/articles/26820)

3.<编程的逻辑-如何用面向对象方法实现复杂业务需求>

4.<Head First设计模式-第3章 装饰对象装饰者模式>
本章可以称为`"给爱用继承的人一个全新的设计眼界"。` 
我们即将再度探讨典型的`继承滥用`问题。你将在本章学到如何使用对象组合的方式，做到在运行时装饰类。
为什么呢? 一旦，你熟悉了装饰的技巧，你将能够在不修改任何底层代码的情况下，给你的（或别人的）对象赋予新的职责。

5.[PYTHON修饰器的函数式编程](https://coolshell.cn/articles/11265.html)
