1、HTML 如何加载样式？

答：`<link rel="stylesheet" type="text/css" href="css/api.css"/>`

2、 HTML placeholder 属性有什么用？

答：用于为input元素设置提示文字

3、点击输入框时显示数字键盘，如何设置？

答：设置input type=number

4、HTML5 是否支持播放音频、视频？

答：支持。播放音频、视频是HTML5的重要特性，

5、 CSS 如何控制字体大小？

答：通过font-size设置字体大小

6、CSS 如何设置背景图片？

答：background-image或background设置url（../xx/aa.png）

7、CSS 内边距和外边界用什么属性表示？

答：内边距padding，外边距margin

8、 CSS 常用的选择器有哪些？

答：标签选择器，id选择器，class选择器，

9、CSS 中 display 属性可以是那些值？

答：none,block,inline-bloc;,inline

10、px和em单位什么区别？

答：px是页面元素尺寸的绝对度量单位，em是px的相对单位，浏览器默认1em=16px

11、CSS 的 position 有几种定位方式？

答：static,relative,absolute,fixed,

12、CSS 中的 -webkit- 是什么意思？

答：使用-webkit-为了设置兼容–webkit核心的浏览器的CSS 属性

13、CSS 弹性布局如何实现？

答： 
    	
    	<div id=”father”>
    	<div id=” son1”></div>
    	<div id=” son2”></div>
    	<div id=” son3”></div>
    	</div>
         #father{display:box;}
    	 #son1{box-flex:1  }
    	#son2{box-flex:1  }
    	  #son3{box-flex:1  }

14、移动端 :active 和 :hover 伪类是否生效？

答：不再生效

15、CSS3 实现阴影

答：box-shadow: 10px 10px 5px 10px ;

box-shadow: 10px 10px 5px 10px  inset;//内阴影

16、 CSS3 实现圆角

答：设置border-radius属性：border-radius:5px;

17、CSS3 能否实现动画，用到什么属性？

答：使用animation属性

18、CSS 能否使用字体图标或特殊字体，用哪个属性？

答：通过CSS的content属性给HTML元素添加图标对应的字符。然后通过@font-face加载制作图标的的字体，这样注入的字符就会变成所需的图标。

19、CSS 如何设置透明度？

答：设置opacity=1为表示不透明，设为0表示完全透明

20、HTML5 本地存储如何实现？

答：使用localstorage或者indexdb/web sql database

21、为什么有些 HTML5 特性不支持？

答：因为浏览器支持完整的 W3C HTML5有一个过程，同时各浏览器分别代表了不同的利益群体。

22、源码示例中的 doT.js 做什么用？

答：doT.js是一个页面模板引擎，可以通过将数据赋值给模板，就可以生成需要的页面内容显示

23、 JavaScript 的数据类型都有哪些？

答：字符串（String）、数字(Number)、布尔(Boolean)、数组(Array)、对象(Object)、空（Null）、未定义（Undefined）

24、如何判断一个变量是什么类型的？

答：typeof操作符来检测变量的数据类型

25、JS 如何绑定事件？

答：elment .addEventListener("eventType", funPro, captureFlag);

26、移动端开发使用的触摸事件是什么？

答：1. touchstart：手指放在一个DOM元素上。
2. touchmove：手指拖曳一个DOM元素。
3. touchend：手指从一个DOM元素上移开。
4. 
27、JS 如何定义变量？

答：varcarname=new String;
var x=      new Number;
var y=      new Boolean;
var cars=   new Array;
var person= new Object;

28、JS 如何发起 HTTP 请求？

答：

    function httpGet(theUrl){
       	 varxmlHttp = null; 
       	 xmlHttp = new XMLHttpRequest();
       	 xmlHttp.open( "GET", theUrl, false );
       	 xmlHttp.send( null );
       	 return xmlHttp.responseText;
    }

29、JS 请求后端数据接口，可以随便跨域吗？

答：由于安全限制，js缺省是不可以跨域的。Js跨越需要使用跨域设置的方案

30、JS 如何创建数组？

答：

    varmycars = new Array();
    mycars[0] = "Saab";
    mycars[1] = "Volvo";
    mycars[2] = "BMW";
31、JS 如何创建 JSON 对象？
答：var employees = [
{ "firstName":"John" , "lastName":"Doe" },
{ "firstName":"Anna" , "lastName":"Smith" },
{ "firstName":"Peter" , "lastName": "Jones" }
];

32、JSON 对象转换成字符串？

答：text=JSON.stringify(jsonobj)；

33、 JSON 字符串转换成 JSON 对象？

答：varobj = JSON.parse(text);

34、 console.log('error') 什么含义？

答：在控制台输出error信息

35、alert('error') 什么含义？

答：在窗口弹出 error提示框

36、 JS 如何获取 id 为 test 的元素？

答：document.getElementById("test ");

37、 JS 如何选取某一个元素？

答：document.getElementById("test ").getElementsByTagName("div");

38、 JS 如何选取某一组元素？

答：document.getElementsByTagName("tagname");
document.getElementsByClassName("classname")

39、 JS 如何延迟执行一个方法？

答：一般利用setInterval，setTimeout进行延迟异步执行，或者利用while循环，sleep()进行同步阻塞。

40、JS 页面加载完毕事件？

答：

    <script type="text/javascript" language="JavaScript">
     //: 判断网页是否加载完成
    document.onreadystatechange = function () {   
     if(document.readyState=="complete") {  
      alert('ok');
      }   
      } </script>

41、JS 给数组 [{1:1},{1:2},{3:1},{2:2},{2:1}]筛选并排序，结果为[{1:1},{2:1},{3:1}]。

答：
vara= [{1:1},{1:2},{3:1},{2:2},{2:1}]；a.slice(0,1).concat(a.slice(4).concat(a.slice(2,3)));

42、JS 实现dom元素class的转换(JQuery的toggleClass的效果)。

答：

    <style type="text/css">
    	div.testClass{
    		background-color:gray;
    	}
    </style>
    <script type="text/javascript">
    function hasClass(obj, cls) {
    return obj.className.match(new RegExp('(\\s|^)' + cls + '(\\s|$)'));
    }
    function addClass(obj, cls) {
    if (!this.hasClass(obj, cls)) obj.className += " " + cls;
    }
    function removeClass(obj, cls) {
    if (hasClass(obj, cls)) {
    varreg = new RegExp('(\\s|^)' + cls + '(\\s|$)');
    obj.className = obj.className.replace(reg, ' ');
    }
    }
    function toggleClass(obj,cls){
    	if(hasClass(obj,cls)){
    		removeClass(obj, cls);
    	}else{
    		addClass(obj, cls);
    	}
    }
    function toggleClassTest(){
    	varobj = document. getElementById('test');
    	toggleClass(obj,"testClass");
    }
    </script>
    
    <body>
    	<div id = "test" style = "width:250px;height:100px;">
    		sssssssssssss
    	</div>
    	<input type = "button" value = "toggleClassTest" onclick = "toggleClassTest();" />
    </body>

43、如何引入名为xxx.js的外部脚本。

答：<script type="text/javascript" src="script/xxx.js"></script>

44、某个上传只允许上传jpg，png，gif，bmp四种类型的图片，若文件名为fileName，如何在前端进行筛
选。

答：<input id="File1" type="file"  accept=".jpg,.png,.gif,.bmp" />

45、varreg = /abc/gi;
varstr = 'abcdefg';
var str2 = 'abcdefg';
console.log(reg.test(str));
console.log(reg.test(str2));
上面的代码，输出结果是什么？

答：true;false

46、console.log('3-2的结果是：'+3-2);
上面代码，输出的结果是什么？

答：NaN

47、JS中的三种弹出式消息提醒(警告窗口、确认窗口、信息输入窗口)的命令是什么？

答：alert，confirm，prompt

48、CSS 同一个DIV下的三个SPAN之间会有一个小的间隙，如何去掉这个间隙？

答：设置span为块元素，然后设置对应的margin为0；

49、JS call()跟apply()之间的区别。

答：接收参数方面不同，apply()接收两个参数，一个是函数运行的作用域(this)，另一个是参数数组。call()方法第一个参数与apply()方法相同，但传递给函数的参数必须列举出来

50、== 与 === 有什么区别？

答：==是判断值是否相等;===是判断值及类型是否完全相等
