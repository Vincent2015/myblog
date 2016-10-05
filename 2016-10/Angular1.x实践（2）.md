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
**独立作用域绑定策略有3种：@，＝，&**。

&绑定，对于带参数的调用应该如下

JS代码：
    
    .directive("direct",function(){     return{ restrict: 'ECMA', template: '<div><input ng-model="model"/></div>'+'<div><button ng-click="show({name:model})">show</button>', scope:{ show:'&'  }  } })  .controller("nameController",function($scope){ $scope.showName=function(name){   alert(name);  }  });

HTML代码：

<div ng-controller="nameController">  <direct show="showName(name)"></direct>   </div>

这个例子中，通过模板中的ng-click触发了show函数并将一个叫做model的对象作为name参数传递了进去，而在html中，我们把show的属性值设为showName(name)。这其中的道理跟无参的例子是大同小异的。


最后就controller与scope的概念再举例说命一下，一定要知道controller就是函数，$scope是局部作用域。

    var module=angular.module('myApp',[])
         // module.controller('mainController',function() {
        // // body...//
             this.name ="Ari";

            // this.hello = function(){
            // alert('hi');
            // }
        // });

    module.controller('mainController',mainController);

    function mainController(){ 
        this.name ="Ari";

         this.hello = function(){ 
                        alert('hi'); 
                    }
    }

    module.controller('controller2',controller2);

    function controller2($scope){ 
        $scope.sex ="女";
    }
借用controler as可以在代码中去除对$scope的依赖，让你写出耦合性小的纯JS函数。

** 可以规定对于controller as的写法如下：[链接](http://www.cnblogs.com/whitewolf/p/3493362.html)

    angular.module("app",[])
     .controller("demoController",[function(){
         var vm = this;
         vm.title = "angualr";
        return vm; 
    }])



其优势为：

1,定义vm这样会让我们更好的避免JavaScript的this的坑。

2,如果某个版本的angular不再支持controller as,可以轻易的注入$scope,修改为 var vm = $scope;

3,因为不再注入$scope了，controller更加的POJO，就是一个很普通的JavaScript对象。

4,也因为没有了$scope，而controller实例将会成为scope上的一个属性，所以在controller中我们再也不能使用$watch,$emit,$on之类的特殊方法，因为这些东西往往不该出现在controller中的，给大家一个警告，更好的控制。但是一旦如果没办法必须用的话，可以在征得项目组一致同意，将此controller退回$scope.

5,因为controller实例将会只是$scope的一个属性，所以view模板上的所有字段都会在一个引用的属性上，这可以避开JavaScript原型链继承对于值类型的坑。参加https://github.com/angular/angular.js/wiki/Understanding-Scopes.

6,controller as 对于 coffescript,liveScript更友好。 

7.模板上定义的每个字段方法都会在scope寄存在controller as别名上的引用上，所以在controller继承中，不会在出现命名冲突的问题。