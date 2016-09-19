## Node.js模块的实现

之前在网上查阅了许多介绍Node.js的文章，可惜对于Node.js的模块机制大都着墨不多。在后续介绍模块的使用之前，我认为有必要深入一下Node.js的模块机制。

### CommonJS规范

早在Netscape诞生不久后，JavaScript就一直在探索本地编程的路，Rhino是其代表产物。无奈那时服务端JavaScript走的路均是参考众多服务器端语言来实现的，在这样的背景之下，一没有特色，二没有实用价值。但是随着JavaScript在前端的应用越来越广泛，以及服务端JavaScript的推动，JavaScript现有的规范十分薄弱，不利于JavaScript大规模的应用。那些以JavaScript为宿主语言的环境中，只有本身的基础原生对象和类型，更多的对象和API都取决于宿主的提供，所以，我们可以看到JavaScript缺少这些功能：

* JavaScript没有模块系统。没有原生的支持密闭作用域或依赖管理。
* JavaScript没有标准库。除了一些核心库外，没有文件系统的API，没有IO流API等。
* JavaScript没有标准接口。没有如Web Server或者数据库的统一接口。
* JavaScript没有包管理系统。不能自动加载和安装依赖。

于是便有了CommonJS（[http:\/\/www.commonjs.org](http://www.commonjs.org/)）规范的出现，其目标是为了构建JavaScript在包括Web服务器，桌面，命令行工具，及浏览器方面的生态系统。

CommonJS制定了解决这些问题的一些规范，而Node.js就是这些规范的一种实现。Node.js自身实现了require方法作为其引入模块的方法，同时NPM也基于CommonJS定义的包规范，实现了依赖管理和模块自动安装等功能。这里我们将深入一下Node.js的require机制和NPM基于包规范的应用。

### 简单模块定义和使用

在Node.js中，定义一个模块十分方便。我们以计算圆形的面积和周长两个方法为例，来表现Node.js中模块的定义方式。

```
var PI = Math.PI;
exports.area = function (r) {
    return PI * r * r;
};
exports.circumference = function (r) {
    return 2 * PI * r;
};
```

将这个文件存为circle.js，并新建一个app.js文件，并写入以下代码：

```
var circle = require('./circle.js');
console.log( 'The area of a circle of radius 4 is ' + circle.area(4));

```

可以看到模块调用也十分方便，只需要require需要调用的文件即可。

在require了这个文件之后，定义在exports对象上的方法便可以随意调用。Node.js将模块的定义和调用都封装得极其简单方便，从API对用户友好这一个角度来说，Node.js的模块机制是非常优秀的。

### 模块载入策略

**Node.js的模块分为两类，一类为原生（核心）模块，一类为文件模块。原生模块在Node.js源代码编译的时候编译进了二进制执行文件，加载的速度最快。另一类文件模块是动态加载的，加载速度比原生模块慢。但是Node.js对原生模块和文件模块都进行了缓存，于是在第二次require时，是不会有重复开销的。其中原生模块都被定义在lib这个目录下面，文件模块则不定性。**

```
node app.js
```

**由于通过命令行加载启动的文件几乎都为文件模块。我们从Node.js如何加载文件模块开始谈起。加载文件模块的工作，主要由原生模块module来实现和完成，该原生模块在启动时已经被加载，进程直接调用到runMain静态方法。**

```
// bootstrap main module.
Module.runMain = function () {
    // Load the main module--the command line argument.
    Module._load(process.argv[1], null, true);
};
```

**\_load静态方法在分析文件名之后执行**

```
var module = new Module(id, parent);
```

**并根据文件路径缓存当前模块对象，该模块实例对象则根据文件名加载。**

```
module.load(filename);
```

**实际上在文件模块中，又分为3类模块。这三类文件模块以后缀来区分，Node.js会根据后缀名来决定加载方法。**

* **.js。通过fs模块同步读取js文件并编译执行。**
* **.node。通过C\/C++进行编写的Addon。通过dlopen方法进行加载。**
* **.json。读取文件，调用JSON.parse解析加载。**

**这里我们将详细描述js后缀的编译过程。Node.js在编译js文件的过程中实际完成的步骤有对js文件内容进行头尾包装。以app.js为例，包装之后的app.js将会变成以下形式：**

```
(function (exports, require, module, __filename, __dirname) {
    var circle = require('./circle.js');
    console.log('The area of a circle of radius 4 is ' + circle.area(4));
});
```

**这段代码会通过vm原生模块的runInThisContext方法执行（类似eval，只是具有明确上下文，不污染全局），返回为一个具体的function对象。最后传入module对象的exports，require方法，module，文件名，目录名作为实参并执行。**

**这就是为什么require并没有定义在app.js 文件中，但是这个方法却存在的原因。从Node.js的API文档中可以看到还有\_\_filename、\_\_dirname、module、exports几个没有定义但是却存在的变量。其中\_\_filename和\_\_dirname在查找文件路径的过程中分析得到后传入的。module变量是这个模块对象自身，exports是在module的构造函数中初始化的一个空对象（{}，而不是null）。**

**在这个主文件中，可以通过require方法去引入其余的模块。而其实这个require方法实际调用的就是load方法。**

**load方法在载入、编译、缓存了module后，返回module的exports对象。这就是circle.js文件中只有定义在exports对象上的方法才能被外部调用的原因。**

**以上所描述的模块载入机制均定义在lib\/module.js中。**

### require方法中的文件查找策略

由于Node.js中存在4类模块（原生模块和3种文件模块），尽管require方法极其简单，但是内部的加载却是十分复杂的，其加载优先级也各自不同。



#### 从文件模块缓存中加载

尽管原生模块与文件模块的优先级不同，但是都不会优先于从文件模块的缓存中加载已经存在的模块。

#### 从原生模块加载

原生模块的优先级仅次于文件模块缓存的优先级。require方法在解析文件名之后，优先检查模块是否在原生模块列表中。以http模块为例，尽管在目录下存在一个http\/http.js\/http.node\/http.json文件，require\(“http”\)都不会从这些文件中加载，而是从原生模块中加载。

原生模块也有一个缓存区，同样也是优先从缓存区加载。如果缓存区没有被加载过，则调用原生模块的加载方式进行加载和执行。

#### 从文件加载

当文件模块缓存中不存在，而且不是原生模块的时候，Node.js会解析require方法传入的参数，并从文件系统中加载实际的文件，加载过程中的包装和编译细节在前一节中已经介绍过，这里我们将详细描述查找文件模块的过程，其中，也有一些细节值得知晓。

**require方法接受以下几种参数的传递：**

* **http、fs、path等，原生模块。**
* **.\/mod或..\/mod，相对路径的文件模块。**
* **\/pathtomodule\/mod，绝对路径的文件模块。**
* **mod，非原生模块的文件模块。**

**在进入路径查找之前有必要描述一下module path这个Node.js中的概念。对于每一个被加载的文件模块，创建这个模块对象的时候，这个模块便会有一个paths属性，其值根据当前文件的路径计算得到。我们创建modulepath.js这样一个文件，其内容为**：

```
console.log(module.paths);
```

我们将其放到任意一个目录中执行node modulepath.js命令，将得到以下的输出结果。

```
[ '/home/jackson/research/node_modules',
'/home/jackson/node_modules',
'/home/node_modules',
'/node_modules' ]
```

Windows下：

```
[ 'c:\\nodejs\\node_modules', 'c:\\node_modules' ]
```

可以看出module path的生成规则为：从当前文件目录开始查找node\_modules目录；然后依次进入父目录，查找父目录下的node\_modules目录；依次迭代，直到根目录下的node\_modules目录。

除此之外还有一个全局module path，是当前node执行文件的相对目录（..\/..\/lib\/node）。如果在环境变量中设置了HOME目录和NODE\_PATH目录的话，整个路径还包含NODE\_PATH和HOME目录下的.node\_libraries与.node\_modules。其最终值大致如下：

```
[NODE_PATH，HOME/.node_modules，HOME/.node_libraries，execPath/../../lib/node]
```

下图是笔者从源代码中整理出来的整个文件查找流程：



简而言之，如果require绝对路径的文件，查找时不会去遍历每一个node\_modules目录，其速度最快。其余流程如下：

1. 从module path数组中取出第一个目录作为查找基准。
2. 直接从目录中查找该文件，如果存在，则结束查找。如果不存在，则进行下一条查找。
3. 尝试添加.js、.json、.node后缀后查找，如果存在文件，则结束查找。如果不存在，则进行下一条。
4. 尝试将require的参数作为一个包来进行查找，读取目录下的package.json文件，取得main参数指定的文件。
5. 尝试查找该文件，如果存在，则结束查找。如果不存在，则进行第3条查找。
6. 如果继续失败，则取出module path数组中的下一个目录作为基准查找，循环第1至5个步骤。
7. 如果继续失败，循环第1至6个步骤，直到module path中的最后一个值。
8. 如果仍然失败，则抛出异常。

整个查找过程十分类似原型链的查找和作用域的查找。所幸Node.js对路径查找实现了缓存机制，否则由于每次判断路径都是同步阻塞式进行，会导致严重的性能消耗。

## 包结构

前面提到，JavaScript缺少包结构。CommonJS致力于改变这种现状，于是定义了包的结构规范（[http:\/\/wiki.commonjs.org\/wiki\/Packages\/1.0](http://wiki.commonjs.org/wiki/Packages/1.0) ）。而NPM的出现则是为了在CommonJS规范的基础上，实现解决包的安装卸载，依赖管理，版本管理等问题。require的查找机制明了之后，我们来看一下包的细节。

一个符合CommonJS规范的包应该是如下这种结构：

* 一个package.json文件应该存在于包顶级目录下
* 二进制文件应该包含在bin目录下。
* JavaScript代码应该包含在lib目录下。
* 文档应该在doc目录下。
* 单元测试应该在test目录下。

由上文的require的查找过程可以知道，Node.js在没有找到目标文件时，会将当前目录当作一个包来尝试加载，所以在package.json文件中最重要的一个字段就是main。**而实际上，这一处是Node.js的扩展，标准定义中并不包含此字段，对于require，只需要main属性即可。但是在除此之外包需要接受安装、卸载、依赖管理，版本管理等流程，所以CommonJS为package.json文件定义了如下一些必须的字段**：

* name。包名，需要在NPM上是唯一的。不能带有空格。
* description。包简介。通常会显示在一些列表中。
* version。版本号。一个语义化的版本号（[http:\/\/semver.org\/](http://semver.org/) ），通常为x.y.z。该版本号十分重要，常常用于一些版本控制的场合。
* keywords。关键字数组。用于NPM中的分类搜索。
* maintainers。包维护者的数组。数组元素是一个包含name、email、web三个属性的JSON对象。
* contributors。包贡献者的数组。第一个就是包的作者本人。在开源社区，如果提交的patch被merge进master分支的话，就应当加上这个贡献patch的人。格式包含name和email。如：
* ```
  "contributors": [{
      "name": "Jackson Tian",
      "email": "mail @gmail.com"
  	}, {
      "name": "fengmk2",
      "email": "mail2@gmail.com"
  }],
  ```

* bugs。一个可以提交bug的URL地址。可以是邮件地址（mailto:mailxx@domain），也可以是网页地址（http:\/\/url）。
* licenses。包所使用的许可证。例如：
* ```
  "licenses": [{
      "type": "GPLv2",
      "url": "http://www.example.com/licenses/gpl.html",
  }]
  ```

* repositories。托管源代码的地址数组。
* dependencies。当前包需要的依赖。这个属性十分重要，NPM会通过这个属性，帮你自动加载依赖的包。

以下是Express框架的package.json文件，值得参考。

```
{
    "name": "express",
    "description": "Sinatra inspired web development framework",
    "version": "3.0.0alpha1-pre",
    "author": "TJ Holowaychuk 
```

除了前面提到的几个必选字段外，我们还发现了一些额外的字段，如bin、scripts、engines、devDependencies、author。这里可以重点提及一下scripts字段。包管理器（NPM）在对包进行安装或者卸载的时候需要进行一些编译或者清除的工作，scripts字段的对象指明了在进行操作时运行哪个文件，或者执行拿条命令。如下为一个较全面的scripts案例：

```
"scripts": {
    "install": "install.js",
    "uninstall": "uninstall.js",
    "build": "build.js",
    "doc": "make-doc.js",
    "test": "test.js",
}
```

如果你完善了自己的JavaScript库，使之实现了CommonJS的包规范，那么你可以通过NPM来发布自己的包，为NPM上5000+的基础上再加一个模块。

```
npm publish <folder>
```

命令十分简单。但是在这之前你需要通过npm adduser命令在NPM上注册一个帐户，以便后续包的维护。NPM会分析该文件夹下的package.json文件，然后上传目录到NPM的站点上。用户在使用你的包时，也十分简明：

```
npm install <package>
```

甚至对于NPM无法安装的包（因为某些奇怪的网络原因），可以通过github手动下载其稳定版本，解压之后通过以下命令进行安装：

```
npm install <package.json folder>
```

只需将路径指向package.json存在的目录即可。然后在代码中require\('package'\)即可使用。

Node.js中的require内部流程之复杂，而方法调用之简单，实在值得叹为观止。更多NPM使用技巧可以参见[http:\/\/www.infoq.com\/cn\/articles\/msh-using-npm-manage-node.js-dependence](http://www.infoq.com/cn/articles/msh-using-npm-manage-node.js-dependence)。

## Node.js模块与前端模块的异同

通常有一些模块可以同时适用于前后端，但是在浏览器端通过script标签的载入JavaScript文件的方式与Node.js不同。Node.js在载入到最终的执行中，进行了包装，使得每个文件中的变量天然的形成在一个闭包之中，不会污染全局变量。而浏览器端则通常是裸露的JavaScript代码片段。所以为了解决前后端一致性的问题，类库开发者需要将类库代码包装在一个闭包内。以下代码片段抽取自著名类库underscore的定义方式。

    (function () {
        // Establish the root object, `window` in the browser, or `global` on the server.
        var root = this;
        var _ = function (obj) {
                return new wrapper(obj);
            };
        if (typeof exports !== 'undefined') {
            if (typeof module !== 'undefined' && module.exports) {
                exports = module.exports = _;
            }
            exports._ = _;
        } else if (typeof define === 'function' && define.amd) {
            // Register as a named module with AMD.
            define('underscore', function () {
                return _;
            });
        } else {
            root['_'] = _;
        }
    }).call(this);

**首先，它通过function定义构建了一个闭包，将this作为上下文对象直接call调用，以避免内部变量污染到全局作用域。续而通过判断exports是否存在来决定将局部变量\_绑定给exports，并且根据define变量是否存在，作为处理在实现了AMD规范环境（**[**http:\/\/wiki.commonjs.org\/wiki\/Modules\/AsynchronousDefinition**](http://wiki.commonjs.org/wiki/Modules/AsynchronousDefinition)**）下的使用案例。仅只当处于浏览器的环境中的时候，this指向的是全局对象（window对象），才将\_变量赋在全局对象上，作为一个全局对象的方法导出，以供外部调用。**

**所以在设计前后端通用的JavaScript类库时，都有着以下类似的判断：**

```
if (typeof exports !== "undefined") {
    exports.EventProxy = EventProxy;
} else {
    this.EventProxy = EventProxy;
}
```

**即，如果exports对象存在，则将局部变量挂载在exports对象上，如果不存在，则挂载在全局对象上**。

对于更多前端的模块实现可以参考国内淘宝玉伯的seajs（[http:\/\/seajs.com\/](http://seajs.com/)），或者思科杜欢的oye（[http:\/\/www.w3cgroup.com\/oye\/](http://www.w3cgroup.com/oye/)）。

## 参考文献

* [http:\/\/www.commonjs.org](http://www.commonjs.org/)
* [http:\/\/npmjs.org\/doc\/README.html](http://npmjs.org/doc/README.html)
* [http:\/\/www.infoq.com\/cn\/articles\/msh-using-npm-manage-node.js-dependence](http://www.infoq.com/cn/articles/msh-using-npm-manage-node.js-dependence)
* [http:\/\/nodejs.org\/docs\/latest\/api\/modules.html](http://nodejs.org/docs/latest/api/modules.html)

