下面我们就以这三个条件为原则，具体介绍下 Express、Koa 中的异常处理：

2.1 Express 异常处理
在 Express 中有一个内置的错误处理中间件，这个中间件会处理任何遇到的错误。如果你在 Express 中传递了一个错误给next()，而没有自己定义的错误处理函数处理这个错误，这个错误就会被 Express 默认的错误处理函数捕获并处理，而且会把错误的堆栈信息返回到客户端，这样的错误处理是非常不友好的，还好我们可以通过设置NODE_ENV环境变量为production，这样 Express 就会在生产环境模式下运行应用，生产环境模式下 Express 不会把错误的堆栈信息返回到客户端。

在 Express 项目中可以定义一个错误处理的中间件用来替换 Express 默认的错误处理函数：

	app.use(errorHandler)
	function errorHandler(err, req, res, next) {
	  if (res.headersSent) {
	    return next(err)
	  }
	  res.status(500)
	  switch(req.accepts(['html', 'json'])) {
	    case 'html':
	      res.render('error', { error: err })
	      break
	    default:
	      res.send('500 Internal Server Error')
	  }
	}
在所有其他app.use()以及路由之后引入以上代码，可以满足以上三个友好错误处理条件，是一种非常友好的错误处理机制。

2.2 Koa 异常处理
我们以Koa 1.x为例，看代码：

	app.use(function *(next) {
	  try {
	    yield next
	  } catch (err) {
	    this.status = err.status || 500
	    this.body = err
	    this.app.emit('error', err, this)
	  }
	})
把上面的代码放在所有app.use()函数前面，这样基本上所有的同步错误均会被 try{} catch(err){} 捕获到了，具体原理大家可以了解下 Koa 中间件的机制。

2.3 未捕获的异常 uncaughtException
上面的两种异常处理方法，只能捕获同步错误，而异步代码产生的错误才是致命的，uncaughtException错误会导致当前的所有用户连接都被中断，甚至不能返回一个正常的HTTP 错误码，用户只能等到浏览器超时才能看到一个no data received错误。

这是一种非常野蛮粗暴的异常处理机制，任何线上服务都不应该因为uncaughtException 导致服务器崩溃。在Node.js 我们可以通过以下代码捕获 uncaughtException错误：

	process.on('uncaughtException', function (err) {
	  console.error('Unexpected exception: ' + err)
	  console.error('Unexpected exception stack: ' + err.stack)
	  // Do something here: 
	  // Such as send a email to admin
	  // process.exit(1)
	})
捕获uncaughtException后，Node.js 的进程就不会退出，但是当 Node.js 抛出 uncaughtException 异常时就会丢失当前环境的堆栈，导致 Node.js 不能正常进行内存回收。也就是说，每一次、uncaughtException 都有可能导致内存泄露。既然如此，退而求其次，我们可以在满足前两个条件的情况下退出进程以便重启服务。当然还可以利用domain模块做更细致的异常处理，这里就不做介绍了。
> 摘自https://segmentfault.com/a/1190000006166385?utm_source=weekly&utm_medium=email&utm_campaign=email_weekly#articleHeader7