说说Angular的配置块和运行块
    
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
     .factory('myservice',function(){
     var service ={};
     return service;
     })
     .directive('mydirective',function(){
     return {
     template:'<a>baidu.com</a>'
     }
     })
