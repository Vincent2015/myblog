说说Angular的配置块和运行块
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

说说指令中的require的作用
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
