要优化，就得搞清楚angular的运行机理。表面上看几行代码，就实现了数据的双向绑定，这一实现内部归功于angular的$digest循转。

在angualr混入浏览器后，就有了一个angular上下文是运行在angular事件循环内的特定代码。这个循环就是$digest循环，包含

1.$watch列表

对于$scope对象上的属性在被视图绑定时，就会在$watch列表中，添加一个$watch函数；这个函数有两个必选参数和一个可选参数

watchExpression 监测的对象

listener/callback 变化时的回调

obejctEquality 说明是否检查严格相等

angular应用持续跟踪当前监控的值，会遍历$watch列表，如果值为变化，会继续监控；如果变化，会启用新值，并再次运行这一$digest循环。

2.$evalAsync列表

$evalAsync()是一种在当前作用域上调度表达式在未来某个时刻运行的方式。无论何时，只要你想要在一个行为的执行上下文中的外部之行另一个行为，就可以使用该函数。


说说$apply()函数

$apply()函数可以从angular框架的外部让表达式在angular的上下文中之行。

    $scope.$apply('message="hi grils"');

    $scope.$apply(function(){
     $scope.message='hi boy'
    })

通常指令内部都会自己调用$apply(),但是对于第三方的框架，就需要借助$apply来将值传入angular上下文

    app.directive('mypicker',function(){
    return function(scope,ele,attr,ctrl){
        $(function(){
            ele.datapicker({
                 dateFormat:'mm/dd/yy',
                 onSelect:function(){
                        $scope.apply(function(){
                                        ctrl.$setViewValue(date)    
                                    })
                            }    
                    })
            })
        }
    })

datePicker插件暴露了一个onSelect事件，为了在angular 拿到选择的值，使用$apply()处理回调。

了解了$digest循环，我们来说下优化

1，减少$digest循环的数量，可以对循环的数量优化，避免不要的循环

2，$watch的回调函数要能快速之行完成

3，每个$watch是一个监控器，对于不需要监控变化的数据，可以去除监控器，可以用使用bingonce只从模型到视图一次绑定

4，每个过滤器在页面上至少会被调用2次，减少过滤器。可以对数据做预处理后再绑定到视图

5，可以使用$templateCache优化模版加载。

想要一个高性能的应用，性能优化必须做，理解angular的内部机制，会帮助你事半功倍