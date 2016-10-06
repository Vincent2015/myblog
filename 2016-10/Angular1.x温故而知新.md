说说Angular的配置块和运行块
配置块会在提供者注册和配置的过程中，对模块或者服务进行配置。这是唯一在应用启动前可以修改的地方。
    
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

