关于module,controller,作用域的概念

Angular中控制器是一个函数，是用来增强视图的，用来向视图的作用域添加功能。默认的控制器函数，是定义在全局作用域中的。如

    function myController($scope){
       $scope.message ='hi guys!'
    }