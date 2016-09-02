1、APICloud规定的 meta viewport 怎么写？
答：`<meta name="viewport" content="width=device-width,user-scalable=no,initial-scale=1">`

2、APICloud推荐的设计图是什么尺寸？

答：建议UI出图时出640*960规格的图即可

3、APICloud推荐的 CSS 和 JS 加载方式？

答：推荐CSS和JS都放在一个页面中同步加载。

4、api跟 $api分别表示什么意思？

答：$api是前段框架的对象，api是云端API的对象

5、$api的使用文档在哪里?

答：在文档页的技术专题部份，专门有前端框架文档。

6、APICloud能否跟其他前端框架混用？

答：APICloud可以与其他前端框架混用。

7、APICloud的 Window 和 Frame 什么含义？

答：Window是APP的主窗口，frame是以一个个页面；Window可以包含多个多个Frame

8、APICloud如何监听事件？

答：addEventListener({params}, callback(ret, err))
$api.addEvt(element, 'click', function(){});

9、 Android 如何监听主页键和返回键?

答：api.addEventListener({ name:'keymenu'},function(ret,err){   })
api.addEventListener({name: 'keyback'}, function(ret, err){ });

10、scrollPicture模块如何跟随网页一起滚动，一起关闭？

答：scrollPicture.open时设置fixed为false，并设置fixedon属性为当前网页

11、APICloud IDE有哪些特别功能？

答：真机同步测试、运行PC模拟器、本地打包、云端编译、输出手机调试日志、在线文档、APICloud代码提示以及自动补全等功能

12、ICON上传时上传什么尺寸的图片，才能适配不同分辨率的手机？

答：150*150px
13、成员管理中邀请到的新成员是否拥有项目所有管理权限？

答：不是。新成员只拥有项目开发的权限，只有项目创建者才可以邀请新成员。

14、怎样理解文档中的page，widget，win，frame，app？

答：一个app就是一个widget；每个widget允许有多个window，同时只允许一个window位于屏幕上，所以你可以将window看成当前屏幕；每个window下可以有多个frame和frameGroup，frame和frameGroup依附在window上，frameGroup包含一个可以横拖的frame数组。

15、APICloud编译后，JS 和 CSS 文件是打包在安装文件里，还是需要从 Web 服务器获取？

答：是打包在安装文件中的

16、api.openWin能否打开服务器端 URL？

答：可以

17、打开服务器端 URL 或 Web APP，如何控制前进、后退的访问历史？

答：可以使用historyBack和historyForward控制后退和前进。

18、Frame 中如何控制 Window 中的元素？

答：可以使用ExectScript进行指定函数调用，进行操作Window中的元素

19、apiready有什么用？

答：用于 window和frame页面加载完成后，进行窗口和页面的读取数据，设置事件监听等初始化工作

20、APICloud的扩展模块如何调用，比如fs模块？

答：首先通过api.require(‘fs’)载入fs模块，然后利用返回的对象操作模块;

21、APICloud的核心模块如何调用，比如 alert ？

答：引用API对象，如API. alert

22、openFrameGroup做什么用？

答：用于一次打开多个并行的的frame,并预装一个页面，从而提升打开速度。

23、如何切换frameGroup页面？

答：setFrameGroupIndex设置索引为需要显示的页面

24、frameGroup默认预加载几个窗口？

答：默认预加载1个窗口

25、怎么实现左右滚动子窗口，菜单也跟着切换？

答：只需要设置scrollEnabled：true就可以

26、如何配置输入框的键盘弹出方式？

答：设置softInputMode参数

27、tapmode="active" onclick="fun()" tapmode和onclick结合干什么用？

答：tapmode和onclick结合是为了去掉系统默认的300ms的延迟；而且tapmode指定class后还可以在click时改变元素的样式

28、三页切换的引导页面如何实现？

答：可以用ScrollPicture来实现。

29、必须用APICloud云数据库吗？

答：可以使用自己的服务器和数据库，只要遵循返回json格式数据就可以。

30、应用里如何获取服务端接口数据？

答：可以使用MCM或者ajax数据云接口来访问服务端接口数据

31、控制台有些模块无法删除，怎么做？

答：可以在config.xml文件找到对应的 feature，将其去掉。对于apicloud给每个应用默认配置的mcm,push,mam是应用必须的模块，不能删除。

32、能否判断网络类型，如何判断？

答：可以使用API对象的connectionType属性判断

33、APICloud提供哪些数据存储？

答：localstorge,db,file，云数据库

34、使用api.showProgress，一直在加载中，不消失可能是怎么回事？

答：未能执行api.hideProgress

35、关闭程序前的提示，能不能定义？

答：可以自定义。

36、IDE 如何切换用户？

答：IDE窗口右下角，先注销当前用户，以新用户登录即可。

37、 IDE 真机调试快捷键？

答：右键工程，一键真机

38、云修复无效果，可能的原因？

答：在config文件中未开启云修复功能

39、APICloud测试版应用是否支持推送？

答：官方的推送在测试版不支持，第三方推送有的可以

40、如果需要实现一些功能，而APICloud没有提供对应的模块，这个时候怎么办呢？

答：在模块Store寻找；也可以根据APICloud提供的模块开发接口，自行开发

41、AppLoader是什么?

答：Apploader是APICloud  app的测试运行床，可以在调试阶段，模拟提供APIcloud的功能框架。

42、下拉刷新如何实现？

答：可以通过api.setRefreshHeaderInfo的函数来实现，在回调函数中处理业务逻辑。在处理完后别忘记调
用api.refreshHeaderLoadDone(),否则组件不能恢复到初始状态

43、分页加载数据怎么实现？

答：首先获取符合条件的数据总数count, 然后根据每页可以显示的数据条数pagecount,在query设置limit为pagecount,同时设置skip为已显示的数量，将读取会来的数据append到页面元素中。

44、如何刷新 Frame ？

答：设置reload为true,就可以

45、iOS 7+ 状态栏跟应用重叠可能是什么原因？

答：未调用$api.fixIos7Bar(header)处理。

46、为什么应用在AppLoader里运行正常，云编译后显示空白，可能原因？

答：如果安卓手机没有提供sd卡可能会出现上述,情况.请安装sd卡后重试；
某些手机可能存在权限问题,导入无法同步应用.

47、应用启动加载首页 index.html, 它的窗口名字是什么？

答：窗口名字是root

48、提示报错：Uncaught ReferenceError: api is not defined，是什么原因？

答：没有引入api.js文件

49、云端创建新应用时，Native 与 Web 有哪些区别？

答：Native是运行在用户手机端的，web是运行在服务器端的，是通过浏览器来访问的。

50、api.ajax可以跨域请求么？

答：可以进行跨域请求