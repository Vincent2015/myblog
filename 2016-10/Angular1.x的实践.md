使用Angularjs开发项目的人，绝大部分顶多只能说曾经用过其中的一些组件或者构件，有的对自己使用的东西很熟练，有的不熟练而已。即便有一些大牛，对anguar的核心代码研读，精通。可是真的在使用Angular做项目时，是否正确的使用了其中的技术组件，又是否能够高效的开发完成一个可维护，高性能，可扩展的项目呢？

结合最近一年的项目，最近又把《AngularJS权威教程》图灵版，《AngularJS高级编程》清华版拿出来研读。

Angularjs中一般开发人员接触最多的就是Module,controller,$scope, directive,filter,service.这些概念。

因为Angualr提供了MVVM的模型，所以很多初学者，在使用angular开发时，主要使用controoler,$scope,内置的指令来开发项目。

我们部门有一个老的angular遗留项目就是这样的。

1，项目的业务逻辑代码，都在控制器中；控制器写rest请求去后端读取数据，然后直接放scope中。导致很多冗余的代码，并导致一些控制器特别大，最大的文件达近60k。

2.视图层面完全是ng-bind/ng-src/ng-bind-html等等。

不是说这样的好与不好，一个DEMO或者原型，使用这样的开发是可以的。但是一个正式的项目，这样做，无法扩展，无法维护。随着项目业务和逻辑的增加，这样的项目肯定最后垮掉。

Angular的service就是用来提供业务逻辑的组件，可以用来在controller间共享，且是单实例模式。非常有利于优化代码结构。

Angular还有一个非常非常重要的就是自定义指令，通过自定义指令，将DOM操作放到指令中去处理，并提供可复用的组件。

先说service,这里以一个提供从后端读取数据的service为例来说明

    angualr.module('myApp',[])

    .factory('userService',function($http){
  
    var url ="http://api.service.com";
   
    var userRequest = function(username,path){
        return $http({
               method:'JSONP',
               url:url+'/users/'+username+'/'+path+'?callback=JSONCallBack'
         });
      };
    return {
         events:function(username){return userRequest(username,'events')}
        }
})

这里的return $http()返回的是一个promise

然后在controller中使用

    angualr.module('myApp',[])

    .controller('userController',function($scope, userService){
        $scope.user = userService.event('auser');
    })

创建服务的方法
>factory('name',function(){})

>factory('name',['$http',function($http){}])

>service('name',constrctor/*构建函数*/)

>constant(‘appkey’,’123123')

>value('appsecritykey','hhh7711h098')

>provider()

重点说下provider,其它的方法都是provider的语法糖。

    angualr.module('myApp',[])
    .provider('userService',function($http){
     var serverUrl ="http://api.service.com";

     setserverUrl:function(url){
      if(url){serverUrl =url}
    }
    
    method:'JSONP'
    
    $get:function($http){
      self=this;
     return $http({

     method:self.method,
    
     url:url+'/users/'+username+'/'+path+'?callback=JSONCallBack'

         });

     };

    })

通过provider定义的service可以在config块中进行配置

    angualr.module('myapp',[])
    .config(function(userServiceProvidee){
        userService.setserverUrl('http://cnode.com/v1/api');
    })

采用provider定义的service，如果名称为name，那么就会有对应一个name+provider的服务提供者。

const定义的服务常量也可以注入到config中

     angualr.module('myapp',[])
    .constant('apikey','7686111')
    .config(function(apikey){
    
     })

angular的一些实战建议收集

1，不推荐在controller中使用$watch
