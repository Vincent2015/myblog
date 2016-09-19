### 从ajax,deferred到promise
####1 ajax

(jquery 1.5 blow)
  
	$.ajax({
        url: "/echo/html/",
       success: function(){
			alert("哈哈，成功了！");
		},error:function(){
			alert("出错啦！");
		}});

(after 1.5)

######1.1 deferred
	简单说，deferred对象就是jQuery的回调函数解决方案。在英语中，defer的意思是"延迟"，所以deferred对象的含义就是"延迟"到未来某个点再执行。
	$.ajax("test.html")
	.done(function(){ alert("哈哈，成功了！"); })
	.fail(function(){ alert("出错啦！"); });

	case
	
	$.ajax("test.html")
	.done(function(){ alert("哈哈，成功了！");} )
	.fail(function(){ alert("出错啦！"); } )
	.done(function(){ alert("第二个回调函数！");} );

	$.when($.ajax("test1.html"), $.ajax("test2.html"))
	.done(function(){ alert("哈哈，成功了！"); })
	.fail(function(){ alert("出错啦！"); });


	var wait = function(dtd){
	   var dtd = $.Deferred(); //新建一个Deferred对象
	   var tasks = function(){
			alert("执行完毕！");
			dtd.resolve(); // 改变Deferred对象的执行状态
		};
		
		setTimeout(tasks,5000);
		return dtd.promise(); // 返回promise对象
	};
	
	$.when(wait())
	.done(function(){ alert("哈哈，成功了！"); })
	.fail(function(){ alert("出错啦！"); });

	Query.Deferred()基于Promises/A规范实现，因为jQuery本身的设计风格，jQuery.Deferred()并没有完全遵循Promises/A规范。

**jQuery.Deferred对象**
	
	jQuery.Deferred()创建一个新的Deferred对象的构造函数，可以带一个可选的函数参数，它会在构造完成后被调用。

	jQuery.when()	
	通过该方式来执行基于一个或多个表示异步任务的对象上的回调函数

	jQuery.ajax()	执行异步Ajax请求，返回实现了promise接口的jqXHR对象

	deferred.then( doneFilter [, failFilter ] [, progressFilter ] )	当Deferred（延迟）对象解决，拒绝或仍在进行中时，调用添加处理程序。

	deferred.done()	当延迟成功时调用一个函数或者数组函数.

	deferred.fail()	当延迟失败时调用一个函数或者数组函数.。

	deferred.always()	当Deferred(延迟)对象解决或拒绝时，调用添加处理程序。

	deferred.resolve(ARG1，ARG2，…)	调用Deferred对象注册的‘done’回调函数并传递参数

	deferred.resolveWith(context，args)	调用Deferred对象注册的‘done’回调函数并传递参数和设置回调上下文

	deferred.isResolved	确定一个Deferred对象是否已经解决。

	deferred.reject(arg1，arg2，…)	调用Deferred对象注册
	的‘fail’回调函数并传递参数

	deferred.rejectWith(context，args)	调用Deferred对象注册的‘fail’回调函数并传递参数和设置回调上下文

	deferred.promise()	返回promise对象，这是一个伪造的deferred对象：它基于deferred并且不能改变状态所以可以被安全的传递
### promise

	ES6 原生提供了 Promise 对象。
	所谓 Promise，就是一个对象，用来传递异步操作的消息。它代表了某个未来才会知道结果的事件（通常是一个异步操作），并且这个事件提供统一的 API，可供进一步处理。

	var promise = new Promise(function(resolve, reject) {
	 if (/* 异步操作成功 */){
	resolve(value);
	} else {
	reject(error);
	}});

	promise.then(function(value) {
	 // success
	}, function(value) {
	 // failure
	});

	Promise 构造函数接受一个函数作为参数，该函数的两个参数分别是 resolve 方法和 reject 方法。

	如果异步操作成功，则用 resolve 方法将 Promise 对象的状态，从「未完成」变为「成功」（即从 pending 变为 resolved）；

	如果异步操作失败，则用 reject 方法将 Promise 对象的状态，从「未完成」变为「失败」（即从 pending 变为 rejected）。

	基本的 api
	Promise.resolve()
	Promise.reject()
	Promise.prototype.then()
	Promise.prototype.catch()
	Promise.all() // 所有的完成
	Promise.race() // 竞速，完成一个即可

	Promise 的实现
		q
		bluebird
		co
		when