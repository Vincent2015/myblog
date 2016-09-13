###Gulp构建解决移动端css/js缓存问题

####1, js／css文件替换字符为时间戳实现版本号

	var gulp = require('gulp');
	var replace = require('gulp-replace');
	option = {
        buildPath: "./"//构建目录
    };
	gulp.task("default", function () {
    var date = new Date().getTime();
    gulp.src('./index.html')
        .pipe(replace(/_VERSION_/gi, date))
        .pipe(gulp.dest(option.buildPath + '/'))
	})


	<link rel="stylesheet" href="style/base.css?v=_VERSION_"/>
	<link rel="stylesheet" href="style/index.css?v=_VERSION_"/>


替换后的html文件中的引用

		<link rel="stylesheet" href="assets/css/live.css?v=1473388750410" />
		<link rel="stylesheet" href="assets/css/swiper-3.3.1.min.css？v=1473388750410"" />

####2,gulp增加md5后缀实现版本号
	var gulp = require('gulp');
	var concat = require('gulp-concat');                            //- 多个文件合并为一个；
	var minifyCss = require('gulp-minify-css');                     //- 压缩CSS为一行；
	var rev = require('gulp-rev');                                  //- 对文件名加MD5后缀
	var revCollector = require('gulp-rev-collector');               //- 路径替换
	var rename = require('gulp-rename');                            // 文件重命名
	var uglify = require("gulp-uglify");

	gulp.task('concat', function() {          //- 创建一个名为 concat 的 task
  		 return gulp.src(['./src/css/*.css'])         //- 需要处理的css文件，放到一个字符串数组里
        //.pipe(concat('wrap.min.css'))        ／/- 合并后的文件名
        .pipe(minifyCss())                                      //- 压缩处理成一行
        .pipe(rev())                                            //- 文件名加MD5后缀
        .pipe(gulp.dest('./dist/css'))                               //- 输出文件本地
        .pipe(rev.manifest())                                   //- 生成一个rev-manifest.json
        .pipe(gulp.dest('./rev/css'                    //- 将 rev-manifest.json 保存到 rev 目录内
		});


	gulp.task('minify-js', function () {
	  return  gulp.src(['./src/js/*.js']) // 要压缩的js文件
    .pipe(rev())   
    .pipe(uglify())
    .pipe(gulp.dest('./dist/js'))//压缩后的路径
    .pipe(rev.manifest())                                   //- 生成一个rev-manifest.json
    .pipe(gulp.dest('./rev/js'));     //- 将 rev-manifest.json 保存到 rev 目录内
	});

	gulp.task('css-rev',['concat'], function() {
	   return gulp.src(['./rev/css/*.json', './src/*.html']) 
        .pipe(revCollector())                                   //- 执行文件内css名的替换
        .pipe(gulp.dest('./src'));                             //- 替换后的文件输出的目录
	});



	gulp.task('default', ['minify-js','css-rev'],function(){
    gulp.src(['./rev/js/*.json', './src/*.html']) 
        .pipe(revCollector())                                   //- 执行文件内css名的替换
        .pipe(gulp.dest('./dist'));    
	});	

