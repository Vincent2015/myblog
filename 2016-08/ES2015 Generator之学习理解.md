generator 就像一个取号机，你可以通过取一张票来向机器请求一个号码。你接收了你的号码，但是机器不会自动为你提供下一个。换句话说，取票机“暂停”直到有人请求另一个号码(next())，此时它才会向后运行。下面我们看一个简单的示例：

	function* idMaker(){
	  var index = 0
	  while(index < 3)
	    yield index++
	}
	
	var gen = idMaker()
	
	gen.next() // {value: 0, done: false}
	gen.next() // {value: 1, done: false}
	gen.next() // {value: 2, done: false}
	gen.next() // {value: undefined, done: true}
	// ...


从上面的代码的输出可以看出:

generator 函数的定义，是通过 function *(){} 实现的
对 generator 函数的调用返回的实际是一个遍历器，随后代码通过使用遍历器的 next() 方法来获得函数的输出
通过使用yield语句来中断 generator 函数的运行，并且可以返回一个中间结果
每次调用next()方法，generator 函数将执行到下一个yield语句或者是return语句。
下面我们就对上面代码的每次next调用进行一个详细的解释：

第1次调用next()方法的时候，函数执行到第一次循环的yield index++语句停了下来，并且返回了0这个value，随同value返回的done属性表明 generator 函数的运行还没有结束
第2次调用next()方法的时候，函数执行到第二循环的yield index++语句停了下来，并且返回了1这个value，随同value返回的done属性表明 generator 函数的运行还没有结束
... ...
第4次调用next()方法的时候，由于循环已经结束了，所以函数调用立即返回，done属性表明 generator 函数已经结束运行，value是undefined的，因为这次调用并没有执行任何语句
PS：如果在 generator 函数内部需要调用另外一个 generator 函数，那么对目标函数的调用就需要使用yield*。
![](http://i.imgur.com/CwscdhS.jpg)

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

> 摘自https://segmentfault.com/a/1190000006166385?utm_source=weekly&utm_medium=email&utm_campaign=email_weekly#articleHeader7