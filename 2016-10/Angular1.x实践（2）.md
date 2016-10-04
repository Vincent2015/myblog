**关于module,controller,作用域的概念**

**Angular中控制器**是一个函数，是用来增强视图的，用来向视图的作用域添加功能。默认的控制器函数，是定义在全局作用域中的。如

    function myController($scope){
       $scope.message ='hi guys!'
    }
创建一个控制器时，angular会生成一个新的$scope（局部作用域）,控制器的作用域是可以嵌套的，如父控制器和子控制器。

**$scope可以**

提供观察者以监测数据模型的变化

可以把数据模型的变化通知给应用

可以嵌套

给表达式提供运行时环境

>$rootscope是和根ng-app绑定的。

**为了避免污染全局，就要用到模块**，修改为如下

    angular.module('myapp',[])
    .controller('myController',function($scope){
     $scope.message ='hi guys!'
    })

和以上几个概念都有关的还有一个概念指令directives,可以理解为自定义html元素，和属性。

指令在创建时，angular也会为她创建一个作用域。这个作用域分为外部作用域和独立作用域。

**来看下最简单的指令**
<my-directive></my-directive>

    angular.module('myapp',[])
    .directive('my-directive',function(){
        return {
             restrict:'E',
             template:'<a href="http://baidu.com">Click me to baidu</a>'    
            }
    })

给指令传递数据，可以直接使用外部controller的作用域的属性。存在的问题是一旦控制器被移除或修改，我们的指令就要修改。

可以如下形式，通过创建独立作用域，使用绑定策略来传递数据给指令
<my-directive></my-directive>

     angular.module('myapp',[])
         .directive('my-directive',function(){
         return {
         scope:{}/*创建独立作用域*/
         restrict:'E',
         template:'<a href="http://baidu.com">Click me to baidu</a>'
        }
    })

实例如下

<div my-directive my-url="http://baidu.com" my-link-text="Click me to baidu">

     angular.module('myapp',[])
     .directive('my-directive',function(){
       return{
            restrict:'A',
            repalce:true,
            scope:{
                   myUrl:@,
                    myLinkText:@
                  },
             template:'<a href='{{myUrl}}'>'+'{{myLinkText}}</a>'
            };
      });
独立作用域绑定策略有3种：@，＝，&