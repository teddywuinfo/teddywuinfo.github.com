---
layout: post
title: "[翻译]并发模型和Event Loop"
date: 2013-12-15 19:00
comments: true
categories: [javascript, event loop, concurrency model]
---
原文来自《[Concurrency model and Event Loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/EventLoop)》

JavaScript具有并发性，这个并发性是基于“event loop”的。它的这种并发性的实质和其他语言（比如C、Java）都不太一样。

<!-- more -->
运行时的一些概念
--------

下面只解释原理性的东西，由于现代浏览器对这块做了许多优化，有些细节可能不太一样。

###看图###
![堆、栈、和队列](https://developer.mozilla.org/files/4617/default.svg)

###堆栈（Stack）###

函数间的调用形成了由函数栈帧组成的栈。如下面这个例子

``` javascript
	function f(b){
	  var a = 12;
	  return a+b+35;
	}

	function g(x){
	  var m = 4;
	  return f(m*x);
	}

	g(21);
```

当调用 `g` 函数的时候，创建了一个函数栈帧。它就是`g`函数和它的局部变量。当`g` 调用 `f`的时候，第二个函数栈帧被创建，它放在第一个函数栈帧的上面。它就是`f`函数和它的局部变量。当`f`函数返回的时候，最上层的函数栈帧被弹出栈（只剩下`g`这个函数栈帧），当`g`返回的时候,`g`的函数栈帧被弹出栈，因此栈就为空了。

###堆（Heap）###

对象是放在堆中的。堆就是指一块没有特定结构的内存区域。对象通过对象名作为索引，通过索引可以与它所在的一小块内存区域关联起来。

###队列（Queue）###

JavaScript运行时有一个消息队列，这些消息将要被按序执行。每一个消息与一个函数相关联。当栈为空，一个消息就会从消息队列中取出并执行。执行过程就是调用相关的函数（因此创建一个新的函数调用栈）。当栈为空时，消息继续往下执行。

Event Loop
==========

event loop实质上就像它的名字所指那样，它看起来像是这样的：

``` javascript
	while(queue.waitForMessage()){
	  queue.processNextMessage();
	}
```

如果当前没有消息的话，`queue.waitForMessage`会同步地等待消息的到来。

###Run-to-completion###

每一个消息都要执行完才能执行下一个消息。这个让程序逻辑变得清晰，无论什么时候函数被执行，它都不会pre-empted，它会全部执行完毕才让其他代码执行（可以操作函数可以控制的数据）。在C语言里面就不一样了，可能一个函数在一个线程中运行，在某个时刻被停止了，然后在另一个线程中执行其他的代码。

这个模型的缺点就是一个消息需要很长时间去执行完毕，网页程序不能对用户的操作（比如点击、滚动）做出处理。浏览器弹出“正在运行一个需要长时间运行的脚本”提示框，来减轻用户的反感。一个好的实践经验就是让消息处理过程尽可能的短，如果可能的话，尽可能把一个消息分割成多个消息。

###添加消息###
在浏览器中，有各种各样事件监听器在监听事件，如果事件被触发，那么消息就会被添加到消息队列中。如果没有事件监听器，事件就会丢失。因此，对一个绑定了点击事件监听器的按钮进行点击的时候，就会添加消息——其他事件也都类似。

调用[setTimeout](https://developer.mozilla.org/en-US/docs/window.setTimeout)的时候会经过第二个参数给的时间之后再把消息添加到队列中。如果队列没有其他消息，那么这个消息就会马上执行。如果有消息在里面，这个消息不得不等其他消息都执行完才能被执行。所以第二个参数指定的时最短的执行时间时间，这个时间无法保证。

###运行时环境之间的交流###

一个web worker 或者跨源 iframe 有他们自己的栈、堆和消息队列。两个不同的运行时环境只能通过[postMessage](https://developer.mozilla.org/en-US/docs/DOM/window.postMessage)方法来发送消息，从而交流。只要一个运行时环境监听了message事件，另一个运行时环境可以通过postMessage这个方法把消息添加到那个运行时环境。

永不阻塞
=======
JavaScript的event loop模型一个非常有趣的特性。不像其他语言，它是永远不会阻塞的。I/O处理经常会通过事件和回调来实现。因此当应用程序在等待[IndexedDB](https://developer.mozilla.org/en-US/docs/IndexedDB)查询结果返回，或者一个[XHR](https://developer.mozilla.org/en-US/docs/DOM/XMLHttpRequest)请求返回的时候，它仍然可以执行其他事情。

有一些例外的情况，比如`alert`或者同步的XHR请求，但我们一般都会避免使用他们。然后，[例外还是会存在](http://stackoverflow.com/questions/2734025/is-javascript-guaranteed-to-be-single-threaded/2734311#2734311)（但通常被当做是个bug来对待）。

更多资料：
=======
一篇通俗易懂的文章《[什么是Event Loop](http://www.ruanyifeng.com/blog/2013/10/event_loop.html)》。

