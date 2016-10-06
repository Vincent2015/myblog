**说说Angular的配置块和运行块**

配置块会在提供者注册和配置的过程中，对模块或者服务进行配置。这是唯一在应用启动前可以修改的地方。另外只有提供者和常量可以注入到config
    
    angular.module('myapp',[])
    .config(function($provider){
    })
这段代码熟悉吗？可是你知道下面的代码和config有什么关系？

      angular.module('myapp',[])
      .factory('myservice',function(){
            var service ={};
            return service;
      })
      .directive('mydirective',function(){
            return {
                template:'<a>baidu.com</a>'
                }
        })
其实angular在执行上面代码，会先编译为如下形式

    angular.module('myapp',[])
    .config(function($provider,$complileProvider))
     $provider.factory('myservice',function(){
         var service ={};
         return service;
     })
     $compileProvider.directive('mydirective',function(){
         return {
         template:'<a>baidu.com</a>'
         }
     })
可见所有的东西，都是通过提供者provider来提供的。

再说运行块

    angular.module('myapp',[])
    .run(function($rootScope){
    })

run相当于传统的main方法，有一个参数(initializeFn)在angular创建完注入器后执行。

**
说说指令中的require的作用**，require的的参数为数组或者字符串，字符串代表一个指令的名字，require会将（a指令）控制器注入到新指令中，并作为新指令的链接函数的第四个参数。
// <div a b></div> 

    ui.directive('a', function(){ 
        return {
         controller: function(){ 
                this.data = {} 
                this.changeData = function( ... ) {
                  ... } }, 
            link: ($scope, $element, $attributes, controller) {
             controller.data = { ... } 
        } 
   } }) 

    myApp.directive('b', function(){
         return { 
             require: 'a', 
              link: ($scope, $element, $attributes, aController)
             {
                    aController.changeData() 
                    aController.data = { ... } 
            } 
    } })
也就说指令中需要暴露给外部（如其它指令）的方法和数据可以放在指令的controller中定义。link主要是给指令内部的操作使用的。

**指令的compile函数**主要是希望指令和数据放到DOM之前的操作，此时对于DOM的增删是安全的 。

还有一个特殊性的指令 **ngModel**，可以提供底层的API来处理控制器内的数据，当我们在指令中require 'ngmodel',可以访问一个特殊的API，用以做数据绑定，校验，css更新等。一个自定义验证的例子

    angular.moule('app',[])
    .directive('unique',function(
      return {
      require:'ngModel',
      link:function(scope,ele,attr,controller){
          scope.watch(attr.ngModel,function(){
               // 验证属性
              // controller.$setValidity('unique',true);            
            })
          }
        }
    ))

其它
1，表单验证的增强指令ngMessahe 可以bower install angualr-message获取
2，推断式注入转为行内声明注入ngMin.
