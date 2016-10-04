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