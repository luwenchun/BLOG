---
title: 基于gulp的前端脚手架
date: 2018-05-02
---

最近看到gulp和webpack各种脚手架，这次来入门下这二种脚手架。

## Gulp快速入门

首先确保本地已安装node.js和npm
然后通过npm全局安装gulp

		$ npm install -g gulp   

根据不同项目的要求，可选择适合的gulp插件进行后续开发。如果项目的要求基本相似（如小型的活动运营H5，平台页面等）可建立自己的脚手架工具以便于后续快速开发。

### 搭建脚手架

1、首先在项目根目录下通过命令行安装项目依赖

		$ npm install --save-dev gulp

2、在项目根目录下新建文件gulpfile.js。要想完成相关插件的配置，首先需要了解gulp相关方法

		gulp只有五个方法： task run watch src dest

		task 这个API用来创建任务，在命令行下可以输入 gulp test 来执行test的任务。
		run 这个API用来运行任务
		watch 这个API用来监听任务。
		src 这个API设置需要处理的文件的路径，可以是多个文件以数组的形式[main.scss,vender.scss]，也可以是正则表达式/
		*/ .scss。 
		dest 这个API设置生成文件的路径，一个任务可以有多个生成路径，一个可以输出未压缩的版本，另一个可以输出压缩后的版本。

		其实整个gulp的配置文件，基本上都是在做一些任务的配置，比如创建任务，监听任务等等。

### 基于脚手架进行开发

1、拷贝package.json和gulpfile.js到相印项目根目录下，使用以下命令安装各插件

		$ npm install

2、在根目录下键入相印命令进行所需的操作，如

		$ gulp

### 基本设定

先大致地梳理一遍我们想要的功能和一些前提的设定。

1、脚手架自动化工具基于Gulp

2、基本的Task

		初始化目录结构
		初始化Index文件结构（迭代时可考虑基于Mobile OR PC）
		Sass自动编译
		CSS AutoPrefixer
		监听文件，自动刷新
		合并雪碧图

### 编写自己的Gulpfile.js

有了上面的基本约束后，现在建立自己的gulpfile文件，这里会详细记录每一个task的配置和使用。

首先把package.json和gulpfile.js建立起来

使用 npm init 初始化package.json（可一直按回车，或填写一些基本信息）。使用 npm install --save-dev gulp 使用gulp作为项目依赖

安装所需插件以及gulpfile.js的task配置

#### 初始化目录与文件

首先需要初始化目录结构，这里使用fs-extra

使用以下命令进行安装 （后续的插件安装不再赘述，大家可从npmjs处查找相印的安装以及Api）

		npm install --save fs-extra

先看一眼我们需要生成的目录结构

		├── dist # 编译后的文件
			├── html
				└── index.html # home
			├── css
			├── js
			└── img
		├── src 
			├── img
			├── sprite
			├── pics
			├── js
			└── sass
				└── style.scss
		├── psd

写入gulpfile.js

		var gulp = require('gulp');

		// 引入组件
		var path   = require('path'), // node自带组件
			fse    = require('fs-extra'); // 通过npm下载

		// 获取当前文件路径
		var PWD = process.env.PWD || process.cwd(); // 兼容windows

		gulp.task('init', function() {

			var dirs = ['dist','dist/html','dist/css','dist/img','dist/js','src','src/sass','src/js','src/img','src/pic','src/sprite','psd'];
			
			dirs.forEach(function (item,index) {
				// 使用mkdirSync方法新建文件夹
				fse.mkdirSync(path.join(PWD + '/'+ item));
			})
			
			// 往index里写入的基本内容
			var template = '<!DOCTYPE html><html lang="zh-CN"><head><meta charset="utf-8"/><meta name="viewport" content="width=device-width,initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no"/><title></title><meta name="apple-touch-fullscreen" content="yes" /><meta name="format-detection" content="telephone=no" /><meta name="apple-mobile-web-app-capable" content="yes" /><meta name="apple-mobile-web-app-status-bar-style" content="black" /></head><body></body></html>';

			fse.writeFileSync(path.join(PWD + '/dist/html/index.html'), template);
			fse.writeFileSync(path.join(PWD + '/src/sass/style.scss'), '@charset "utf-8";');
		})

此时运行$ gulp init发现目录已经生成完成了。再对index.html里的内容用编辑器的插件格式化一下，进入下一步。

### 编译.sass以及为某些属性添加适当的前缀

添加对sass编译的支持 安装插件gulp-sass。在gulpfile写入

		// 编译sass
		var sass = require('gulp-sass');

		gulp.task('sass', function () {
		return gulp
			// 在src/sass目录下匹配所有的.scss文件
			.src('src/sass/**/*.scss')
			
			// 基于一些配置项 运行sass()命令
			.pipe(sass({
				errLogToConsole: true,
				outputStyle: 'expanded'
			}).on('error', sass.logError))
			
			// 输出css
			.pipe(gulp.dest('dist/css'));
		});

这时候在命令行敲入$ gulp sass发现已经跑起来了，但是存在两个问题

		每次修改完sass文件后都需要输入命令
		若sass有书写错误会直接退出gulp监听

这两个问题我们放到最后再一起解决。

下面来为我们写的css的某些不兼容属性添加前缀。这里用到了插件gulp-autoprefixer，看一眼代码。其实都大同小异，大致的内容可在npmjs内查找到。

		var autoprefix = require('gulp-autoprefixer');

		gulp.task('autoprefixer', function () {
		return gulp.src('dist/css/**/*.css')
			.pipe(autoprefixer({
				browsers: ['ios 5','android 2.3'],
				cascade: false
			}))
			.pipe(gulp.dest('dist/css'));
		});

下面测试一下，在scss写入一个css3属性

		perspective: 500;

输入命令 $ gulp autoprefixer 发现文件没有更改。原因是sass文件还未被编译，这时候需要先敲一遍$ gulp sass编译。这时会发现css已经被添加了相印的后缀了

![1111](/../about/1.png)

#### 开启服务器以及监听编译

终于到配置的最后一步了，我们的想法是当sass、js、html有所更改时都执行相印的命令。
如 自动压缩、合并文件、添加前缀、刷新浏览器

这里选用browser-sync同时我想当sass、sprite等文件改变时自动执行所需的task，使用watch命令对文件进行监听。话不多说，还是看源码。

		//=======================
		//     服务器 + 监听
		//=======================
		var browserSync = require('browser-sync').create();
		gulp.task('default', function() {
			// 监听重载文件
			var files = [
			'dist/html/**/*.html',
			'dist/css/**/*.css',
			'src/js/**/*.js',
			'src/sprite/**/*.png'
			]
			browserSync.init(files, {
			server: {
					baseDir: "./",
					directory: true
				},
			open: 'external',
			startPath: "dist/html/"
			});
			// 监听编译文件
			gulp.watch("dist/html/**/*.html").on('change', browserSync.reload);
			gulp.watch("src/sass/**/*.scss", ['sass']);
			gulp.watch("src/sprite/**/*.png", ['sprite']);
			gulp.watch("dist/css/**/*.css", ['autoprefixer']);
		});

在命令行输入 $ gulp 这时我们的项目已经妥妥地跑起来了。