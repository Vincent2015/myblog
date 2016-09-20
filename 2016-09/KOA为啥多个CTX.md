为啥Koa 2.x要多出个ctx？

@jonathanong



这样变化的最主要的原因是，在你写koa apps 时使用async箭头函数的时候：



app.use(async (ctx, next) => { await next()})



这种情况下，使用this是万万不可能的。



因为 Arrow Function是 Lexical scoping（定义时绑定）, this指向定义Arrow Function时外围, 而不是运行时的对象。





引用koa 1.x和2.x 的差异根源

1.x



var koa = require('koa');var app = koa();



2.x



const Koa = require('koa');const app = new Koa();



源码



1.x



/** * Application prototype. */var app = Application.prototype;/** * Expose `Application`. */module.exports = Application;/** * Initialize a new `Application`. * * @api public */function Application() { if (!(this instanceof Application)) return new Application; this.env = process.env.NODE_ENV || 'development'; this.subdomainOffset = 2; this.middleware = []; this.proxy = false; this.context = Object.create(context); this.request = Object.create(request); this.response = Object.create(response);}



2.x



/** * Expose `Application` class. * Inherits from `Emitter.prototype`. */module.exports = class Application extends Emitter { /** * Initialize a new `Application`. * * @api public */ constructor() { super(); this.proxy = false; this.middleware = []; this.subdomainOffset = 2; this.env = process.env.NODE_ENV || 'development'; this.context = Object.create(context); this.request = Object.create(request); this.response = Object.create(response); }}



很明显，1.x是函数，而2.x是类，需要new来实例化。



整个Koa2.x里只有application做了类化，其他的还是保持之前的风格，大概还没有到必须修改的时候吧。
