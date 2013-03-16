---
layout: post
title: "[bug 分享]octopress rake generate bug fix : Liquid Exception: invalid byte sequence in GBK in post"
date: 2013-03-16 18:58
comments: true
categories: [octopress,jekyll,rake generate,GBK,invalid byte sequence in GBK in post]
---

win 8 下 octopress 博客搭建的时候，在  rake generate 时发现如下错误：

	Liquid Exception: invalid byte sequence in GBK in post

<!--more -->

如下图：

![bug图](/images/git/octopress rake generate.png "bug图")

分析： 这里的bug 出现时因为Ruby 里面 File.read 不支持 utf-8 导致。

解决方案：

到Ruby的安装目次`\lib\ruby\gems\1.9.1\gems\jekyll-0.11.2\lib\jekyll\`找到`convertible.rb`这个文件，批改

	self.content = File.read(File.join(base, name))

为

	self.content = File.read(File.join(base, name), :encoding => "utf-8")

如果在jekyll-0.12.0版本，另外在此文件也有同样问题：

`\lib\ruby\gems\1.9.1\gems\jekyll-0.12.0\lib\jekyll\tags\include.rb`

(完)
