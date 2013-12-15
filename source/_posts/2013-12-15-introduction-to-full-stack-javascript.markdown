---
layout: post
title: "[翻译]JavaScript一波流"
date: 2013-12-15 10:54
comments: true
categories: 
---
原文来自[An Introduction To Full-Stack JavaScript](http://coding.smashingmagazine.com/2013/11/21/introduction-to-full-stack-javascript/#more-129418)


现在，要开发web应用，你有非常多技术方案可以选择，而你想要选择最合适的一种，这种方案必须敏捷快速，持续迭代，高效可靠等等。你想要这种技术精简和灵活，而且无论是短期还是长期都帮助你成功。但这些技术方案不总是那么容易找到。

据我所知，[JavaScript一波流（full-stack JavaScript）](http://theothersideofcode.com/building-full-javascript-application-stack)满足所有要求。你可能之前也听说过，觉得它没用，甚至和朋友辩论。但你自己有真的试过吗？在本文，我会给你详细介绍一波流JavaScript，以及它到底是怎么运作的。
<!-- more -->
给一个概要图：


![概要图](/images/full-stack-javascript/toptal-blog-500-opt.png)

我会逐部分介绍这些组件。在此之前，我简单说下我们是怎么发展到现在这样的。

为什么我选择JavaScript
--------------------

从1998年起，我就是一个web开发者。那时候，我们在用[Perl](http://www.theperlreview.com/articles/php.html)进行后台开发。而在那时候，我们已经在用JavaScript进行前端开发。Web后台开发技术从那时起开始发生巨大变化：我们经历过一波又一波技术和语言的发展浪潮，比如PHP、ASP、JSP、.NET、Ruby、Python还有很多。

在PHP和ASP年代的早期，那时候模版引擎还是一个概念，开发者把应用程序代码内嵌到HTML里面。像这样的代码并非罕见。

	<script>
	    <?php
	        if ($login == true){
	    ?>
	    alert("Welcome");
	    <?php
	        }
	    ?>
	</script> 

甚至有：

	<script>
	    var users_deleted = [];
	    <?php
	        $arr_ids = array(1,2,3,4);
	        foreach($arr_ids as $value){
	    ?>
	    users_deleted.push("<php>");
	    <?php
	        }
	    ?>
	</script>

对于初学者来说，在不同语言之间存在典型的错误和令人困惑的语句，比如`for`和`foreach`。而且，甚至在现在，在前台和后台写这样的代码来处理相同的数据结构，是让人很不爽的(除非，你有一个开发团队，有人专门负责前台，有专门负责后台。如果这样的话，就算他们知道对方在做什么，也很难读懂对方的代码。)：

    <?php
        $arr = array("apples", "bananas", "oranges", "strawberries"),
        $obj = array();
        $i = 10;
        foreach($arr as $fruit){
            $obj[$fruit] = $i;
            $i += 10;
        }
        echo json_encode(obj);
    ?>
    <script>
        $.ajax({
            url:"/json.php",
            success: function(data){
                var x;
                for(x in data){
                    alert("fruit:" + x + " points:" + data[x]);
                }
            }
        });
    </script>

最初尝试统一前后台语言的方案，是在服务器端创建前端组件，然后把组件编译成JavaScrpt。但实际不如预期，大部分那样的项目失败了（比如 ASP MVC 替代 [ASP.NET Web form](http://stackoverflow.com/questions/102558/biggest-advantage-to-using-asp-net-mvc-vs-web-forms)，[GWT](http://www.gwtproject.org/overview.html)逐渐被[Polymer](http://www.2ality.com/2013/05/google-polymer.html)替代）。但这个想法其实不赖，因为从本质上说，前后台统一语言，然我们可以重用组件和*资源*（这个是关键字）。

答案很简单：把JavaScript 放到后台。

JavaScript实际上[一发明出来的时候是用于网景企业服务器端的](http://www.infoworld.com/d/application-development/javascript-conquers-the-server-969)，但这个语言在那时候还没做好。经过多年反复试验，[Node.js](http://www.toptal.com/nodejs/why-the-hell-would-i-use-node-js)最终出现了。它不仅把JavaScript放到服务器端，也促进了[非阻塞性编程](http://blog.mixu.net/2011/02/01/understanding-the-node-js-event-loop/),这个从nginx世界而来的概念。这多亏Node发明人的nginx开发背景，(明智地)保持简单，多亏了JavaScript的event-loop特性。

（一句话概括，非阻塞性编程目的在于把耗时任务放一边，指明当这些任务完成之后要做些啥之后，腾出处理器来处理其他请求。）

** Node.js 从此改变了我们处理I/O请求的方式。** 作为Web开发者。我们很熟悉这样访问数据库（I/O）：

	var resultset = db.query("SELECT * FROM 'table'");
	drawTable(resultset);

这一行实际上会阻塞你的代码，因为你的程序会停止运行直到你的数据库返回数据 *resultset*。在此期间，你的架构会提供线程来提高并发性。

有了Node.js和非阻塞性编程，我们对程序流程有了更多的控制。现在（你仍然可以使用线程提高并发），你可以告诉程序在请求数据的同时，你可以做什么，还有数据到达之后，要怎么处理。

	db.query("SELECT * FROM 'table'", function	resultset){
	   drawTable(resultset);
	});
	doSomeThingElse();

通过这个代码片段，我们可以看到两段程序流程：第一个在发送数据库查询请求之后，立刻执行；第二个在我们受到返回*resultSet*之后执行。这是一个优雅又强大的管理并发性的方案。就像他们说的：“除了你的代码，所有都是并发执行。”因此，你的代码很容易编写、阅读、理解和维护，不用担心你的程序流程失去控制。

这些想法并不新鲜，但为什么因为Node.js的出现，这些东西又突然流行起来呢？很简单。非阻塞性变成可以通过不同方法实现，最简单的可能就是使用回调(callback)和事件轮询([event loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/EventLoop))。对于大部分语言而言，这个并不容易实现。有些语言使用回调很常见，但缺很少使用事件轮询。又或者你需要折腾一些外部库（比如，Python 和 [Tornado](http://www.tornadoweb.org/en/stable/)）。

但是在JavaScript里面，[回调](http://www.impressivewebs.com/callback-functions-javascript/)是一种语言特性，事件轮询也是。几乎所有玩过JavaScript的程序员都熟悉这些东西，或者至少用过，[即使他们不太懂啥是事件轮询](http://developer.yahoo.com/blogs/ydn/part-1-understanding-event-loops-writing-great-code-11401.html)。突然所有创业公司只需要找JavaScript程序员就可以同时搞定前台和后台，减轻了不少招聘难题。

我们现有了一个很牛X的平台（感谢非阻塞性编程），和一个很简单的编程语言（感谢JavaScript）。这就足够了吗？它会持续多久？我很肯定，JavaScript在将来会越来越受到重视。我告诉你原因.

### 函数式编程 ###

JavaScript是第一个[把函数范式普及给大众](http://www.crockford.com/javascript/javascript.html)的程序语言。当然，Lisp是第一个，但大部分程序员不会用它来写商业应用。Lisp 和 Selp，这两个[对JavaScript影响最大](http://exploringdata.github.io/vis/programming-languages-influence-network/#JavaScript)的语言，充满了革新的概念，可以改变我们的思维，让我们去探索新的技术、模式。这些全都带到了JavaScript里面来。[moands](http://blog.jcoglan.com/2011/03/05/translation-from-haskell-to-javascript-of-selected-portions-of-the-best-introduction-to-monads-ive-ever-read/),[Church numbers](http://uxul.wordpress.com/2009/03/02/generating-church-numbers-with-javascript/)，甚至 [Underscore](http://underscorejs.org/) 的 [collection functions](http://underscorejs.org/#collections)，都可以节省不少代码。

### 动态对象和原型继承 ###

没有类的面向对象编程，脱离了无穷无尽的类的继承，提高了开发效率——只需要创建对象和方法，然后使用他们。 最重要的，它减少了代码重构的代价，因为程序员只需要修高对象的实例，而不用修改类。这种高效和灵活为敏捷开发铺平了道路。

### JavaScript是互联网的  ###

JavaScript是为[互联网而设计的](http://www.computer.org/csdl/mags/co/2012/02/mco2012020007.pdf)。它从一开始就存在，并且[不会消失](http://www.netmagazine.com/news/firefox-23-hide-disable-javascript-option-132851).所有破坏它的尝试都失败了。回想一下，比如，[Java Applet](http://jaxenter.com/douglas-crockford-java-was-a-colossal-failure-javascript-is-succeeding-because-it-works-45928.html)的衰落，VBScript被微软的[TypeScript](http://www.typescriptlang.org/)（可编译成JavaScript）替代，以及Flash在[移动市场和HTML5](http://www.buuteeq.com/blog/html5-and-the-death-of-flash/)方向的失败。替代JavaScript并不影响成千上万的网页，是不可能的啦。所以我们的目标就是改善它。而没有比[ECMA 39技术委员会](http://www.ecma-international.org/memento/TC39.htm)更胜任这项工作。

当然，每天都有想替代JavaScript的新事物出现，比如[CoffeeScript](http://coffeescript.org/)，[TypeScript](http://www.typescriptlang.org/)，和一大堆[可以编译成JavaScript的语言](https://github.com/jashkenas/coffee-script/wiki/List-of-languages-that-compile-to-JS)。这些替代品可能在开发过程比较有用（通过[source maps](http://www.html5rocks.com/en/tutorials/developertools/sourcemaps/)）,但最终他们还是替代不了JavaScript。有两个原因：他们的组织委员会不会更强大，而且他们最好的特性会被ECMAScript采用（比如JavaScript）。JavaScript 不是汇编语言，它是高级程序语言，它的源代码你可以理解，因此，你应该理解它。

 端对端JavaScript：Node.js 和 MongoDB
---------------------------------------

我们已经谈到使用JavaScript的原因，接下来，我们谈谈为什么要使用Node.js和MongodDB。

### Node.js ###

[Node.js](http://www.toptal.com/nodejs/why-the-hell-would-i-use-node-js)是一个构建快速和可扩展网络应用的平台。就像Node.js网站说的那样。但Node.js不仅是那样，它是目前最热的JavaScript运行时环境，被大量应用和库使用着，——**甚至浏览器库也运行在Node.js之上**。最重要的是，这个高效的后台执行让开发者专注于更复杂的问题，比如[Natural](https://github.com/NaturalNode/natural)用于[自然语言处理](http://en.wikipedia.org/wiki/Natural_language_processing)。就算你不打算用Node.js写你的主要服务器端程序，你也可以使用基于Node.js构建的工具提高开发效率。比如，[Bower](http://bower.io/)，是一个前端工具包管理器，[Mocha](http://visionmedia.github.io/mocha/)用于单元测试，[Grunt](http://gruntjs.com/)用于自动化任务构建，甚至[Brackets](http://brackets.io/)用于全文代码编辑。

因此，如果你打算写JavaScript应用，你应该掌握Node.js。因为你经常要跟它打交道。或许有一些有趣的[替代品](http://vertx.io/)出现，但没有一个有Node.js社区的10%那么强大。

### MongoDB ###

[MongoDB](http://www.mongodb.org/) 是一个[NoSQL](https://en.wikipedia.org/wiki/NoSQL)文档型数据库，并使用JavaScript作为查询语言（但不是用JavaScript来实现），从而实现了让JavaScript成为从前端到后端的统一语言的可能。但这不是选择这个数据库最主要的目的。

[MongoDB](http://www.mongodb.org/) 是[无模式的（schema-less）](http://blog.mongodb.org/post/119945109/why-schemaless)，使得你可以更灵活地持有对象。因此可以快速因需而变。再加上，它是高度[可扩展的](http://siliconangle.com/blog/2013/06/21/mongodb-brings-speed-scalability-and-performance-to-handling-unstructured-data-mongodbdays/)和[基于map-reduce的](http://docs.mongodb.org/manual/core/map-reduce/)，使得它适合大数据应用。MongoDB很灵活，它可以用作无模式的文档型数据库，也可以用作关系型数据库（虽然它[缺乏]()了[事务(transaction)](http://docs.mongodb.org/manual/faq/fundamentals/)，只能通过[模拟](http://docs.mongodb.org/manual/tutorial/perform-two-phase-commits/)来实现），甚至也可以用于key-value存储缓存，像[Memcached](http://memcached.org/) 和 [Redis](http://redis.io/)。

用Express实现服务器端组件化 
----------------------

服务器端组建化从来都不简单。但有了[Express](http://expressjs.com/)(和 [Connect](http://www.senchalabs.org/connect/))之后，产生了“中间件”的概念。在我看来，中间件是对与服务器端组件的最好定义。

这里的基础概念就是你的组件是管道的一部分。管道处理一个请求（比如一个输入），然后生成响应（比如输出），但你的组件不用对整个响应输出负责。它只处理它需要处理的地方，然后交接给管道的下一个模块。当管道最后一个模块完成处理的时候，就把最终响应发送回给客户端。

我们把这些管道的中间模块成为中间件。很自然地，我们可以创造两种中间件：

* **直接件**

直接件处理请求和响应，但不对最终响应结果负责，它只需要交接给下一个中间件。

* **最后件**

最后件最最终响应负责。它处理请求和响应，但不需要交接给下一个中间件。实际上，无论怎样都交接给下一个中间件，会使得架构更加灵活（比如以后要增加中间件的话），就算那个中间件并不存在（如果这样的话，响应会自动返回给客户端）

![用户管理器](/images/full-stack-javascript/user-manager-large-opt.png)

举个具体的例子	，比如说一个“用户管理器”服务器组件。从中间件的角度来说，我们直接件和最后件都有。作为最后件，我们有像创建用户和展示用户列表这类的功能。但在做那些操作之前，我们需要直接件来做登录验证（因为我们不想未经过登录校验的请求过来并创建新的用户）。一旦我们创建好了这些登录验证中间件，我们只需要在我们想要的地方添加一下它们，就可以增加登录校验的特性。

单页应用
-------

当使用JavaScript一波流的使用，你经常遇到创建[单页应用](http://en.wikipedia.org/wiki/Single-page_application)(SPA)。大部分WEB开发者都亲自鼓捣过。我也搞过几次（大部分自己搞的）。而我相信它们就是[未来的web应用](https://medium.com/tech-talk/fb44da86dc1f)。你是否对比过SPA和常规web应用在移动设备上的表现。响应速度根本不在一个数量级。

（注：其他人可能不同意我的观点，比如，twitter，[回滚了它的SPA版本](https://blog.twitter.com/2012/improving-performance-twittercom)。与此同时，大量网站，比如[Zendesk](http://techcrunch.com/2012/11/30/why-enterprise-apps-are-moving-to-single-page-design/)正在朝这方向发展。SPA有足够多的优点让我相信它，但实际发展有可能千差万别。）

如果SPA真的那么牛X，为毛线不用呢？一个常见的反对声音就是他们担心SEO（搜索引擎优化）。但如果你处理得当，这个不是什么问题：你可以选择不同的方法，比如[使用一个无界面的浏览器引擎](https://vickev.com/#!/article/easily-index-your-single-page-application-thanks-to-phantomjs)，（[PhantomJS](http://phantomjs.org/)）在框架的帮助下，当网页爬虫监测到的时候，去渲染HTML。

前端 MV* 框架，使用Backbone.js,Marionette和Twitter Bootstrap
---------------------------------------------------------

用于SPA的[MV*框架](http://blog.stevensanderson.com/2012/08/01/rich-javascript-applications-the-seven-frameworks-throne-of-js-2012/)已经讨论了很多，很难选，我觉得最优秀三个是[Backbone.js](http://backbonejs.org/)、[Ember](http://emberjs.com/)和[AngularJS](http://angularjs.org/)。

这三个都非常值得推荐，[哪个最适合你呢](http://todomvc.com/)？

遗憾的是，我觉得对AngularJS还不够熟悉，所以这里我不讨论它。Ember 和 Backbone.js 代表了两个不同的解决思路。

[Backbone.js](http://www.toptal.com/backbone-js/)只提供刚刚够用的东西，让你创建一个简单的SPA.Ember，是一个完整、专业的框架让你创建SPA。它有很多额外的功能，但这需要花更多时间学习。（你可以看更多[关于Ember.js的资料](http://coding.smashingmagazine.com/2013/11/07/an-in-depth-introduction-to-ember-js/)。）

根据你的应用的复杂度来决定。简单地说，你要在“features used” 和 "features available" 两者之间取得合适的点。这是个重要的提示。 

写样式也是一项挑战，当然，我们也可以用框架来解决。对于CSS，[Twitter Bootstrap](http://twitter.github.io/bootstrap/) 是一个不错的选择。因为它提供了一套完整的样式，不仅可以立刻使用，也[支持自定义](http://coding.smashingmagazine.com/2013/03/12/customizing-bootstrap/)。

Bootstrap 使用[LESS](http://lesscss.org/)写的，开源。所以你可以随意修改。它有一堆UX控件，有非常好的[文档](http://twitter.github.io/bootstrap/components.html)。再加上，[自定义模型](http://twitter.github.io/bootstrap/customize.html)让你可以轻松创建自己的样式，非常实用。

最佳实践：Grunt,Mocha,Chai,RequireJS和CoverJS
-------------------------------------------

最后，我们说一下一些最佳实践技巧，以及怎么实现和维护他们。我的解决方案集中在服务器端的工具，它们都是基于Node.js的。

### MOCHA 和 CHAI ###

这些工具让你可以在开发过程中使用[测试驱动开发（TDD）](http://www.agiledata.org/essays/tdd.html)和[行为驱动开发(BDD)](http://dannorth.net/introducing-bdd/)，来组织你的单元测试，并且让测试自动运行。

JavaScript单元测试框架有[一大堆](http://en.wikipedia.org/wiki/List_of_unit_testing_frameworks#JavaScript)，为什么要用Mocha？最短的答案，因为它灵活而且完整。

详细的讲，因为它有两个重要的特性（好的接口，好的记录器）还有一个重要的空缺（断言）。让我解释一下：

* **接口**

也许你习惯于TDD的理念和它的单元测试，或许你更倾向于BDD的概念，比如 **describe** 和 **should**。这两者，Mocha的接口都可以满足。

* **记录器**

测试跑完之后会产生结果文档，你可以选择不同的记录器去格式化这些结果。例如，如果你需要维护一个持续集成的服务器，你可以用一个记录器专门负责它。

* **没有断言的功能**

与其做一个有问题的断言功能，还不如自己选择第三方的断言库，让你有更高的灵活性。你有许多[选择](http://stackoverflow.com/questions/10472152/standalone-assertion-libraries#answer-10480546)，这就是Chai要出场的时候了。

Chai 是一个灵活的断言库，让你拥有三种主要的断言风格：

* `assert`

这是经典TDD的断言风格：

	assert.equal(variable, "value");

* `expect`

这种链式风格最常见于BDD：

	expect(variable).to.equal("value");

* `should`

这也用于BDD，但我更喜欢`expect`因为`should`经常重复（比如行为说明文档“it(should do something...)”），例如：

    variable.should.equal("value");

Chai和Mocha结合很完美。用这两个库你可以用TDD、BDD或者你想用的方式去写你的测试用例。

###GRUNT###

Grunt实现了自动化任务构建,包括简单的复制粘贴、合并文件、模板预编译、样式语言（比如SASS 和 LESS）编译，单元测试（比如Mocha），语法检测和代码压缩（比如，[UglifyJS](https://github.com/mishoo/UglifyJS)和[Closure Compiler](https://developers.google.com/closure/compiler/)）。你可以添加你自己的任务到Grunt或者[搜索已有的插件](http://gruntjs.com/plugins)。一大堆的插件供你使用（再强调下，背后拥有强大社区的工具是很值得一用的）。Grunt可能够[监控你的文件](https://github.com/gruntjs/grunt-contrib-watch)然后当你改动他们的时候触发某些行为。

###REQUIREJS###

RequireJS 听起来就像基于[AMD](https://github.com/amdjs/amdjs-api/wiki/AMD)API的模块加载器，但我向你保证它远不止那样。有了RequireJS,你可以在你的模块上定义依赖和继承，并让RequireJS帮你加载。它也**通过简单的方式避免全局变量命名空间污染**，这通过把你的模块在函数内部定义来实现。这使得模块可复用，而不像[命名空间化的模块](http://www.kenneth-truyers.net/2013/04/27/javascript-namespaces-and-modules/)。思考一下这样的场景：如果你定义了一个模块像这样子——`Demoapp.helloWorldModule` 然后你想把它移植到`Firstapp.helloWorldModule`，那么你需要改变所有`Demoapp`的引用，来让它可移植。

RequireJS 让你拥抱[dependency injection](http://merrickchristensen.com/articles/javascript-dependency-injection.html)模式。假设你有一个模块需要主程序对象（一个单例）的实例。使用RequireJS,你意识到你不能使用全局变量去存储它，而且你不能通过依赖获得实例。因此，你需要在模块构造函数引入这个依赖。看个例子。

在`main.js`:


    define(
      ["App","module"],
      function(App, Module){
          var app = new App();

          var module = new Module({
              app: app
          })

          return app;
      }
    );

在`module.js`：

    define([],
      function(){
          var module = function(options){
              this.app = options.app;
          };
          module.prototype.useApp = function(){
              this.app.performAction();
          };
          return module
      }
    );

注意到，我们要让模块依赖`main.js`的话，比如创建循环引用。

###COVERJS###

[代码覆盖](https://en.wikipedia.org/wiki/Code_coverage)是衡量你的测试用例的尺度标准。就像名字那样，它告诉你有多少代码被你当前的测试用例覆盖到。CoverJS的测试是基于语句的（而不是基于行，像[JSCoverage](http://siliconforks.com/jscoverage/)）。它生成代码覆盖质量报告。它也可以堆你的持续集成的服务器发送报告。

结论
----

JavaScript一波流不能解决所有问题。但它的社区和技术可以肯定对你有所帮助。通过JavaScript，你可以创建扩展性强，可维护的应用，并统一用一种语言。这绝B是未来的趋势啊。







