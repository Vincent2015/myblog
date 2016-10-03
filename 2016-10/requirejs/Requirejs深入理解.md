requirejs＋angular的应用，非常重要的一点就是要把angular当作一个AMD的模块来用，整个应用的入口是requirejs,整个框架也是requirejs


1,Controller的异步加载, 使用resolve来实现

efine(['angular', 'require', 'angular-route'], function (angular, require) {

 var app = angular.module('webapp', [ 'ngRoute' ]);

 app.config(['$routeProvider', '$controllerProvider', function($routeProvider, $controllerProvider) {

 var routeMap = { '/module2/:name': { //路由 path: 'module2/module2.js', //模块的代码路径 controller: 'module2Controller' //控制器名称 } }; var defaultRoute = '/module2'; //默认跳转到某个路由

 $routeProvider.otherwise({redirectTo: defaultRoute}); for (var key in routeMap) { $routeProvider.when(key, { template: '', controller: routeMap[key].controller, resolve:{ keyName: requireModule(routeMap[key].path,  
          routeMap[key].controller) } }); }

 function requireModule(path, controller) { return function ($route, $q) { var deferred = $q.defer(); require([path], function (ret) { $controllerProvider.register(controller, ret.controller); $route.current.template = ret.tpl; deferred.resolve(); }); return deferred.promise; } }

 }]);

2，每个模块可以按照

define(['angular', 'text!module2/tpl.html'], function (angular, tpl){};

这样的形式去做定义。

3，入口是
 require(['angular', 'router'], function(angular){ angular.bootstrap(document, ['webapp']); });
 
或

require(['domReady'], function (domReady) { 
   
domReady(function () { 

    });
});

