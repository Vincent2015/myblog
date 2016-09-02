1, 整体架构

- 项目整体上基于原有的用户帐户体系，替换原来的IM(端连接发送消息，长连接接收消息。整体架构还是比较合理，只是内部的设计和实现经评估不能支持规模应用，还有一方面政治原因). 新的IM采用开放注册的方案，实现用户帐户的迁移，并将发收消息都迁移至长连接，以支持已读未读的业务逻辑。 那么服务端业务改造的核心就是用户信息的同步和管理。

![](http://i.imgur.com/va4M3Dk.png)

2,IM主要采用XMPP协议

XMPP服务器和客户端之间，是通过XML节（XML Stanza）来进行通讯。其中有三种非常重要的XML Stanza类型：`<message>、<presence>、<iq>。`

	<message>：
	
	聊天消息的发送和接收就是通过message节来实现。例如xxg1@host发送一条信息"你好"给xxg2@host，xxg1@host客户端会将下面的这段XML发送到XMPP服务器，服务器再推送给xxg2@host客户端。其中<message>的from属性是发送者，to属性是接收者，<body>子元素的内容就是聊天信息。
	
	<message from="xxg1@host" to="xxg2@host" type="chat">
	    <body>你好</body>
	</message>
	<presence>：
	
	可用于表明用户的状态，例如用户状态改变成“Do not disturb”（“请勿打扰”），会向服务器发送：
	<code>
	<presence from="xxg@host">
	    <status>Do not disturb</status>
	    <priority>0</priority>
	    <show>dnd</show>
	</presence>
	<iq>：
	
	iq即Info/Query，采用“请求-响应”机制，类似于HTTP的机制。下面的例子是客户端通过<iq>请求获取联系人，XMPP服务器将结果返回：
	
	客户端请求获取联系人：
	
	<iq from='xxg@host' id='bv1bs71f' type='get'>
	    <query xmlns='jabber:iq:roster'/>
	</iq>
	服务器结果返回：
	
	Strophe.js连接XMPP服务器Openfire、Tigase实现Web私聊、群聊(MUC)
	<iq to='xxg@host' id='bv1bs71f' type='result'>
	    <query xmlns='jabber:iq:roster'>
	        <item jid='xxg2@host'/>
	        <item jid='xxg3@host'/>
	    </query>
	</iq>

3，前端架构

- IM客户端:JSJaCC库,这里就不上代码了.

- 整个前端采用requirejs+angualr1.3.15的技术栈来搭建，所有的angualr模块/指令/服务/控制器等都封装成了requirejs的模块。如下
    
    	define(['angular'], function () {
    	  'use strict';
    	  return angular.module('app.common',[]);
    	});
       
    	define(['../module'], function(module) {	
    		module.factory("fileConvertService", [function() {
    				return {
    					b64ToFile: function(data, filetype, filename) {
    						for (var t = window.atob(data), n = new Uint8Array(t.length), a = 0; a < t.length; a++) n[a] = t.charCodeAt(a);
    						var l = new Blob([n], {
    							type: filetype
    						});
    						return l.name = filename, l;
    					}
    				}
    	
    			}
    	])
    	})
    
    	define(['../module'], function(module) {
    	.filter('cutString',function(){
    		return function(input,direction,length){
    			if(!input)return '';
    			if(direction){
    				return input.substr(input.length-length,length);
    			}else{
    				return input.substr(0,length);
    			}
    		}
    	});
    	})
    
    	define(['../module'], function(module) {
    		module.directive('switchtool', ['$http', function($http) {
    			 return {
    						restrict:'AE',
    						replace:true,
    						scope:{troggle:'='},
    						template:'<div ng-class={"esn-grpset-set-item-switch":true,"esn-grpset-set-item-switch-on":troggle,"esn-grpset-set-item-switch-off":!troggle}></div>',
    						link: function($scope, element, attrs) {
    								$(element).on('click',function(e){
    									// e.stopPropagation();
    									// e.preventDefault();
    									$scope.troggle = !$scope.troggle;
    								})
    						 }
    					}
    	
    		}]);
    	})
    
    	var app = angular.module('app', ['ui.router', 'app.common' 'ui.select','toaster',
    		  "ngSanitize", "infiniteScroll", "ngStorage",  "ngAnimate", "ngCookies", "ngDialog","imageCropper",'app.app',"com.2fdevs.videogular"
    		])


- 从整体架构上看,我们有一个cache层，目前这个层被设计成为IM前端的数据层，也就是说前端不和服务端直接交互，这里面分两个方面来看，加了cache确实能够简化一部分前端的处理，毕竟前端会为了体验和性能，也会做一些cache；但是加了cache（目前是内存作为cache）后，前端失去了灵活性，性能无法优化，内存占用问题突出，无法按需的去处理数据。毕竟把数据都存在内存中，要去查找和处理都是字符串和对象的列表的查找和处理，这是相当低效的。


4，基于NW做客户端程序

  
- 早期由于对NW的不熟悉及项目给没有去考虑正确的姿势，所以就只是用NW将现有的angular SAP做了一个打包。因此在后面涉及NW本身不能支持的特性，而需要单独开发c++ addon时（IM要支持截屏，视频，音频消息）,遇到一些困难.

- NW端不同于浏览器:NW端主要是体现客户端的体验，是SAP应用。因此很多依赖浏览器F5就能解决的资源占用和释放问题，在NW客户端，如果不显式处理，一逻辑会出问题（你还奇怪浏览器好好的啊），二是资源内存消耗。

5，整体架构问题

- 为了个别功能，目前需维持两个长连接。
 
- IM对接数据同步问题没有彻底解决

6,优化方向

- 去掉整个Cache层，由前端直接处理数据，按需做一定的cache.

- 全Cache方案，cache层要能持久化，并提供优化的存储和查找算法，并能支持分批查找。

- 数据同步应该采取启动同步，和按需同步。

- 简化到一个长连接，否则可能还要连接间还要同步

