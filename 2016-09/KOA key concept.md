koa 简介

由 Express 原班人马打造的 koa，致力于成为一个更小、更健壮、更富有表现力的 Web 框架。使用 koa 编写 web 应用，通过组合不同的 generator，可以免除重复繁琐的回调函数嵌套，并极大地提升常用错误处理效率。Koa 不在内核方法中绑定任何中间件，它仅仅提供了一个轻量优雅的函数库，使得编写 Web 应用变得得心应手。

安装 koa

koa 依赖支持 generator 的 Node 环境，准确来说，是 node &gt;= 0.11.9 的环境。如果你正在使用一个更早的 Node 版本，可以使用模块 n 来管理多版本环境，并且快速安装 0.11.x:

$ npm install -g n $ n 0.11 $ node --harmony my-koa-app.js

安装完成后，应确保使用 $ node --harmony app.js 即，harmony 模式运行程序。

为了方便，可以将 node 设置为默认启动 harmony 模式的别名：

alias node='node --harmony'

应用（Application）

一个 Koa Application（以下简称 app）由一系列 generator 中间件组成。按照编码顺序在栈内依次执行，从这个角度来看，Koa app 和其他中间件系统（比如 Ruby Rack 或者 Connect\/Express ）没有什么太大差别，不过，从另一个层面来看，Koa 提供了一种基于底层中间件编写「语法糖」的设计思路，这让设计中间件变得更简单有趣。

在这些中间件中，有负责内容协商（content-negotation）、缓存控制（cache freshness）、反向代理（proxy support）与重定向等等功能的常用中间件（详见 中间件 章节），但如前所述， Koa 内核并不会打包这些中间件，让我们先来看看 Koa 极其简单的 Hello World 应用程序：

var koa = require\('koa'\); var app = koa\(\); app.use\(function \*\(\){ this.body = 'Hello World'; }\); app.listen\(3000\);

如果使用Koa 2的话：

var Koa = require\('koa'\); var app = new Koa\(\); app.use\(ctx =&gt; { ctx.body = 'Hello World'; }\); app.listen\(3000\);

译者注： 与普通的 function 不同，generator functions 以 function\* 声明，以这种关键词声明的函数支持 yield。generator function是ECMAScript 6定义的新的语法，想了解其基本用法，以及Koa如何利用generator function达到在保持js代码异步特性的同时无需编写大量回调函数，可以参考这篇文章。

级联代码（Cascading）

Koa 中间件以一种非常传统的方式级联起来，你可能会非常熟悉这种写法。

在以往的 Node 开发中，频繁使用回调不太便于展示复杂的代码逻辑，在 Koa 中，我们可以写出真正具有表现力的中间件。与 Connect 实现中间件的方法相对比，Koa 的做法不是简单的将控制权依次移交给一个又一个的中间件直到程序结束，Koa 执行代码的方式有点像回形针，用户请求通过中间件，遇到 yield next 关键字时，会被传递到下一个符合请求的路由（downstream），在 yield next 捕获不到下一个中间件时，逆序返回继续执行代码（upstream）。

下边这个例子展现了使用这一特殊方法书写的 Hello World 范例：一开始，用户的请求通过 x-response-time 中间件和 logging 中间件，这两个中间件记录了一些请求细节，然后「穿过」 response 中间件一次，最终结束请求，返回 「Hello World」。

当程序运行到 yield next 时，代码流会暂停执行这个中间件的剩余代码，转而切换到下一个被定义的中间件执行代码，这样切换控制权的方式，被称为 downstream，当没有下一个中间件执行 downstream 的时候，代码将会逆序执行。

var koa = require\('koa'\); var app = koa\(\); \/\/ x-response-time app.use\(function _\(next\){ \/\/ \(1\) 进入路由 var start = new Date; yield next; \/\/ \(5\) 再次进入 x-response-time 中间件，记录2次通过此中间件「穿越」的时间 var ms = new Date - start; this.set\('X-Response-Time', ms + 'ms'\); \/\/ \(6\) 返回 this.body }\); \/\/ logger app.use\(function _\(next\){ \/\/ \(2\) 进入 logger 中间件 var start = new Date; yield next; \/\/ \(4\) 再次进入 logger 中间件，记录2次通过此中间件「穿越」的时间 var ms = new Date - start; console.log\('%s %s - %s', this.method, this.url, ms\); }\); \/\/ response app.use\(function \*\(\){ \/\/ \(3\) 进入 response 中间件，没有捕获到下一个符合条件的中间件，传递到 upstream this.body = 'Hello World'; }\); app.listen\(3000\);

在上方的范例代码中，中间件依次被执行的顺序已经在注释中标记出来。你也可以自己尝试运行一下这个范例，并打印记录下各个环节的输出与耗时。

译者注： 「级联」这个词许多人也许在 CSS 中听说过，如果你不能理解为什么在这里使用这个词，可以将这种路由结构想象成 LESS 的继承嵌套书写方式：

.middleware1 { \/\/ \(1\) do some stuff .middleware2 { \/\/ \(2\) do some other stuff .middleware3 { \/\/ \(3\) NO next yield ! \/\/ this.body = 'hello world' } \/\/ \(4\) do some other stuff later } \/\/ \(5\) do some stuff lastest and return }

上方的伪代码中标注了中间件的执行顺序，看起来是不是有点像 ruby 执行代码块（block）时 yield 的表现了？也许这能帮助你更好的理解 koa 运作的方式。

![](https://camo.githubusercontent.com/d80cf3b511ef4898bcde9a464de491fa15a50d06/68747470733a2f2f7261772e6769746875622e636f6d2f66656e676d6b322f6b6f612d67756964652f6d61737465722f6f6e696f6e2e706e67)

译者注： 更加形象的图可以参考 Django Middleware

