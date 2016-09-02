###node-webkit之截屏应用
   最近基于NW做桌面端的IM特性，发送消息类型要求支持截屏。原本基于浏览器的截屏插件无法使用。专门研究了node c++addons,搭建了vsstuiod 环境，参考网上的教程，做了第一个hello world的的addon.这个过程中，学习并发现基于V8的addons机制目前还是比较复杂，鉴于项目进度和成本的考虑。最终找到了两个模块screenjs和screencapture两个模块。screenjs是windows平台截屏模块，有较完整的交互处理。而screencapture只是简单的截取整个桌面，缺少交互处理。具体代码如下




            var os = require('os');
    		var platform = os.platform();
    		console.log(platform);
    		if (platform == "win32") {
    			require('screencutjs').start(
    			  {type:'file/png'},
    			  function(result){
    			  console.log('screencut result: ' + JSON.stringify(result));
    			  var fs= require('fs');
    			  fs.readFile(result.file,function (err, data) {
    					  if (err) throw err;
    					  console.log(data);
						  //app procress					
    					  
    					});
    			});
    		} else {
    			//其他平台下 截图支持
    			var screencapture = require('screencapture');
    			screencapture(function (err, imagePath) {
    				var fs= require('fs');
    				console.log(imagePath);
    				if (imagePath && !err) {
    					fs.readFile(imagePath, function (err, data) {
    							if (!err) {
    								console.log(data);
    					            //app procress
    							}
    						});
    				}
    
    			})
    		}
这样的必然结果是mac和windows完全不同的用户体验，现阶段由于投入所限，先这样实现。希望后续有时间和资源投入。