---
layout: post
title: "Markdown语法测试"
date: 2013-02-10 22:26
comments: true
categories: markdown
---
此文主要是用于熟悉markdown的一些常用用法，分别显示了原文格式和html化之后的格式。以供参考。
<!-- more -->
	Markdown语法测试__这里是一级标题
	========
	作者：泰迪__这里是二级标题
	-------

	### 这里是三级标题 ###

	#### 四级标题

	******************
	正文来了：
	这里是第一段落。

	空行表示另外一行。

	******************
	我是*真的*、_真的_、**真的**、 __真的__ 很爱你。
	\*\*和\_\_表示强调。

	******************
	我祝大家：

	* 身体健康
	* 工作愉快
	* 合家平安
	******************

	以下我想对大家说：

	1. 我是teddy
	2. 我是男的
	3. 这个文档主要是记录markdown的语法

	******************
	这是我的[博客](http://teddywu.info "吴炜恒的博客")

	这是我的[博客地址][吴炜恒的博客地址]另外的一种写法

	这里是我的照片：

	![泰迪](/images/teddy/teddy1.jpg "泰迪")

	*****************
	上面是分割线，下面讲讲代码的写法

	其实只要tab缩进了，或者4个空格缩进了就是代码区域了，比如

		var cnt = 0;
		function add(a, b){
			 return a+b;
		}
		var six = add(cnt,5);
	
	用空格缩进的代码：
		@mixin shadow-box($border: #fff .5em solid, $shadow: rgba(#000, .15) 0 1px 4px, $border-radius: .3em) {
		  @include border-radius($border-radius);
		  @include box-shadow($shadow);
		  @include box-sizing(border-box);
		  border: $border;
		}

	如果想行内使用的话可以用这个符号\`把代码包住，比如： `printfUI()`也可以

	******************
	子曾经说过：
	> ###《论语》
	> 学而时习之，不亦乐乎。
	>
	> 温故而知新，可以为师矣。

	[吴炜恒的博客地址]: http://teddywu.info



*****************************
下面就是转码后的效果

Markdown语法测试__这里是一级标题
========
作者：泰迪__这里是二级标题
-------

### 这里是三级标题 ###

#### 四级标题

******************
正文来了：
这里是第一段落。

空行表示另外一行。

******************
我是*真的*、_真的_、**真的**、 __真的__ 很爱你。
\*\*和\_\_表示强调。

******************
我祝大家：

* 身体健康
* 工作愉快
* 合家平安
******************

以下我想对大家说：

1. 我是teddy
2. 我是男的
3. 这个文档主要是记录markdown的语法

******************
这是我的[博客](http://teddywu.info "吴炜恒的博客")

这是我的[博客地址][吴炜恒的博客地址]另外的一种写法

这里是我的照片：

![泰迪](/images/teddy/teddy1.jpg "泰迪")

*****************
上面是分割线，下面讲讲代码的写法

其实只要tab缩进了，或者4个空格缩进了就是代码区域了，比如

	var cnt = 0;
    function add(a, b){
         return a+b;
    }
	var six = add(cnt,5);

用空格缩进的代码：
    @mixin shadow-box($border: #fff .5em solid, $shadow: rgba(#000, .15) 0 1px 4px, $border-radius: .3em) {
      @include border-radius($border-radius);
      @include box-shadow($shadow);
      @include box-sizing(border-box);
      border: $border;
    }

如果想行内使用的话可以用这个符号\`把代码包住，比如： `printfUI()`也可以

******************
子曾经说过：
> ###《论语》
> 学而时习之，不亦乐乎。
>
> 温故而知新，可以为师矣。

[吴炜恒的博客地址]: http://teddywu.info