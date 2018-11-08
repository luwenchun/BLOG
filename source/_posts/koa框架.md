---
title: 从头实现一个koa框架
date: 2018-01-09 
---

看完之后甚是膜拜，决定收藏记录下，引用地址https://zhuanlan.zhihu.com/p/35040744

笔者认为，理解koa，主要需要搞懂四条主线，其实也是实现koa的四个步骤，

   分别是：

		1. 封装node http Server
		2. 构造resquest, response, context对象
		3. 中间件机制
		4. 错误处理
		

#### 主线一：封装node http Server: 从hello world说起

首先，不考虑框架，如果使用原生http模块来实现一个返回hello world的后端app，代码如下：

		let http = require('http');

		let server = http.createServer((req, res) => {
			res.writeHead(200);
			res.end('hello world');
		});

		server.listen(3000, () => {
			console.log('listenning on 3000');
		});

实现koa的第一步，就是对这个原生的过程进行封装，为此，我们首先创建application.js实现一个Application对象：

		// application.js
		let http = require('http');

		class Application {

			/**
			* 构造函数
			*/
			constructor() {
				this.callbackFunc;
			}

			/**
			* 开启http server并传入callback
			*/
			listen(...args) {
				let server = http.createServer(this.callback());
				server.listen(...args);
			}

			/**
			* 挂载回调函数
			* @param {Function} fn 回调处理函数
			*/
			use(fn) {
				this.callbackFunc = fn;
			}

			/**
			* 获取http server所需的callback函数
			* @return {Function} fn
			*/
			callback() {
				return (req, res) => {
					this.callbackFunc(req, res);
				};
			}

		}

		module.exports = Application;

然后创建example.js:

		let simpleKoa = require('./application');
		let app = new simpleKoa();

		app.use((req, res) => {
			res.writeHead(200);
			res.end('hello world');
		});

		app.listen(3000, () => {
			console.log('listening on 3000');
		});

可以看到，我们已经初步完成了对于http server的封装，主要实现了app.use注册回调函数，app.listen语法糖开启server并传入回调函数了，典型的koa风格。

但是美中不足的是，我们传入的回调函数，参数依然使用的是req和res，也就是node原生的request和response对象，这些原生对象和api提供的方法不够便捷，不符合一个框架需要提供的易用性。因此，我们需要进入第二条主线了。

#### 主线二：构造request, response, context对象

如果阅读koa文档，会发现koa有三个重要的对象，分别是request, response, context。其中request是对node原生的request的封装，response是对node原生response对象的封装，context对象则是回调函数上下文对象，挂载了koa request和response对象。下面我们一一来说明。

首先要明确的是，对于koa的request和response对象，只是提供了对node原生request和response对象的一些方法的封装，明确了这一点，我们的思路是，使用js的getter和setter属性，基于node的对象req/res对象封装koa的request/response对象。

规划一下我们要封装哪些易用的方法。这里在文章中为了易懂，姑且只实现以下方法：

对于simpleKoa request对象，实现query读取方法，能够读取到url中的参数，返回一个对象。

对于simpleKoa response对象，实现status读写方法，分别是读取和设置http response的状态码，以及body方法，用于构造返回信息。

而simpleKoa context对象，则挂载了request和response对象，并对一些常用方法进行了代理。

首先创建request.js:

		// request.js
		let url = require('url');

		module.exports = {

			get query() {
				return url.parse(this.req.url, true).query;
			}

		};

很简单，就是导出了一个对象，其中包含了一个query的读取方法，通过url.parse方法解析url中的参数，并以对象的形式返回。需要注意的是，代码中的this.req代表的是node的原生request对象，this.req.url就是node原生request中获取url的方法。稍后我们修改application.js的时候，会为koa的request对象挂载这个req。

然后创建response.js:

		// response.js
		module.exports = {

			get body() {
				return this._body;
			},

			/**
			* 设置返回给客户端的body内容
			*
			* @param {mixed} data body内容
			*/
			set body(data) {
				this._body = data;
			},

			get status() {
				return this.res.statusCode;
			},

			/**
			* 设置返回给客户端的stausCode
			*
			* @param {number} statusCode 状态码
			*/
			set status(statusCode) {
				if (typeof statusCode !== 'number') {
					throw new Error('statusCode must be a number!');
				}
				this.res.statusCode = statusCode;
			}

		};

status读写方法分别设置或读取this.res.statusCode。同样的，这个this.res是挂载的node原生response对象。而body读写方法分别设置、读取一个名为this._body的属性。这里设置body的时候并没有直接调用this.res.end来返回信息，这是考虑到koa当中我们可能会多次调用response的body方法覆盖性设置数据。真正的返回消息操作会在application.js中存在。

然后我们创建context.js文件，构造context对象的原型：

		// context.js
		module.exports = {

			get query() {
				return this.request.query;
			},

			get body() {
				return this.response.body;
			},

			set body(data) {
				this.response.body = data;
			},

			get status() {
				return this.response.status;
			},

			set status(statusCode) {
				this.response.status = statusCode;
			}

		};