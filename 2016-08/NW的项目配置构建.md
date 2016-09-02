基于nw-bulider的gulp构建文件。在构建前创建项目的配置文件，实现区分DEBUG和PRODUCE的不同环境及后续的变更。这里创建了config.json文件去项目配置文件。
	var NwBuilder = require('nw-builder');
	var gulp = require('gulp');
	var gutil = require('gulp-util');
	var fs = require('fs');
	
	function InitConfig(debug){
	  var  settings,settingsFilename= "./appdir/config.json";
	  if (!debug){
	    settings = {
	        'show_sidebar':false,
	        'show_icons':false
	        };
	  }else{
	    settings = {
	        'show_sidebar':true,
	        'show_icons':true
	        };
	  }
	  
	  fs.writeFile(settingsFilename, JSON.stringify(settings));
	}
	
	gulp.task('produce',function(){
	  InitConfig(true);
	  console.log('produce');
	});
	
	
	gulp.task('debug', function () {
	  InitConfig(false);
	  console.log('debug');
	});
	
	gulp.task('p',['produce'], function () {
	  var nw = new NwBuilder({
	        version: '0.12.3',
	        files: './appdir/**',
	        macIcns: './appdir/nw.icns',
	        macPlist: {mac_bundle_id: 'yonyouDesktop'}, //  mac  应用的名称
	        platforms: ['win32', 'win64', 'osx32', 'osx64','linux','linux64'] //  支持的应用平台
	    });
	
	    // Log stuff you want
	    nw.on('log', function (msg) {
	        gutil.log('nw-builder', msg);
	    });
	
	    // Build returns a promise, return it so the task isn't called in parallel
	    return nw.build().catch(function (err) {
	        gutil.log('nw-builder', err);
	    });
	});
	
	gulp.task('d',['debug'], function () {
	  var nw = new NwBuilder({
	        version: '0.12.3',
	        files: './appdir/**',
	        macIcns: './appdir/nw.icns',
	        macPlist: {mac_bundle_id: 'yonyouDesktop'}, //  mac  应用的名称
	        platforms: ['win32', 'win64', 'osx32', 'osx64','linux','linux64'] //  支持的应用平台
	    });
	
	    // Log stuff you want
	    nw.on('log', function (msg) {
	        gutil.log('nw-builder', msg);
	    });
	
	    // Build returns a promise, return it so the task isn't called in parallel
	    return nw.build().catch(function (err) {
	        gutil.log('nw-builder', err);
	    });
	});

打包后的项目运行期，采用如下方式读取配置文件，作为项目的各种配置.

    var fs = require('fs')
    fs.readFile(".//config.json", 'utf8', function (err, data) {
    var savedSettings = JSON.parse(data);
    console.log(savedSettings);
    });

原理很简单，但是可以此方案，实现支持国际化构建及各种配置优化