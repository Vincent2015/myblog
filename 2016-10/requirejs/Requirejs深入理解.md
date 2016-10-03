requirejs＋angular的应用，非常重要的一点就是要把angular当作一个AMD的模块来用，整个应用的入口是requirejs,整个框架也是requirejs


1,Controller的异步加载, 使用resolve来实现

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

