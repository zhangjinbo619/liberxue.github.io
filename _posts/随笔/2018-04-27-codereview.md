---
layout: blog
book: true
title: "code review 随笔"
background-image: "http://zhangjinbo619-github.oss-cn-hangzhou.aliyuncs.com/zhangjinbo619.github.io/codereview.jpg"
date: "2018-04-27 22:35:30"
category: book
tags:
- code review
---


![code review](http://zhangjinbo619-github.oss-cn-hangzhou.aliyuncs.com/zhangjinbo619.github.io/codereview.jpg)

# 背景
   最近app项目ios&&android项目上线，我的初衷是为了让大家一起提高编码质量，当然从无到有的过程是痛苦的。但是做任何一件事情都要明确目标，客观分析。


# 为什么要review？
  - 公司角度：提高产品质量，规避潜在风险。

  - 员工角度：提高自己编码水平，了解别人编码风格，取长补短。毕竟程序员不善言辞，Talk is cheap,show me the code.

  - 个人角度：我真的不想封装好的代码被我们一帮可爱的程序员兄弟姐妹非恶意的改乱了，就像app，它还是个孩子有很多不足，但我们会让它慢慢长大，逐渐变好，至少要比昨天好。


# 什么时候要review？
实践下来，定义送测版本后，在送测版本和上线的间隔期，review代码。

如是bug，必须当前版本修复并送测。

如果是功能优化，根据实际情况决定是否当前版本或者下个版本优化。


# 如何review？

app项目组时间下来有如下几点：
 1. 每次指定一个负责人（让大家都是参与感），负责记录和追溯当前版本review发生的问题，并录入知识库。
 2. 每次开始前根据知识库的记录，追溯历史review的完成情况。
 3. 每次记录的格式需要将解决问题提交代码的reversion号填入，以便以后查看。
 4. 每次review后，去追溯是否有生产上线后bug，分析原因，多问几次为什么，总能找到答案。


总而言之，程序员很可爱，我们也想做的更好，请善待你身边的程序员。😂😂😂😂