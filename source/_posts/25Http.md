---
title: Ajax
date: 2017-05-15 
---
## XMLHttpRequest

	    var xhr = new XMLHttpRequest()
		xhr.onreadystatechange = function () {
			// 这里的函数异步执行，可参考之前 JS 基础中的异步模块
			if (xhr.readyState == 4) {
				if (xhr.status == 200) {
					alert(xhr.responseText)
				}
			}
		}
		xhr.open("GET", "/api", false)
		xhr.send(null)

## 状态码说明

上述代码中，有两处状态码需要说明。xhr.readyState是浏览器判断请求过程中各个阶段的，xhr.status是 HTTP 协议中规定的不同结果的返回状态说明。

xhr.readyState的状态码说明：

		0 - (未初始化）还没有调用send()方法
		1 -（载入）已调用send()方法，正在发送请求
		2 -（载入完成）send()方法执行完成，已经接收到全部响应内容
		3 -（交互）正在解析响应内容
		4 -（完成）响应内容解析完成，可以在客户端调用了

xhr.response 的状态码说明：

xhr.status即 HTTP 状态码，有 2xx 3xx 4xx 5xx 这几种，比较常用的有以下几种：

		200 正常
		3xx
			301 永久重定向。如http://xxx.com这个 GET 请求（最后没有/），就会被301到http://xxx.com/（最后是/）
			302 临时重定向。临时的，不是永久的
			304 资源找到但是不符合请求条件，不会返回任何主体。如发送 GET 请求时，head 中有If-Modified-Since: xxx（要求返回更新时间是xxx时间之后的资源），如果此时服务器 端资源未更新，则会返回304，即不符合要求
		404 找不到资源
		5xx 服务器端出错了



## JQuery ajax

		$.ajax({
			type: 'POST',
			url: url,
			data: data,
			dataType: dataType,
			success: function () {},
			error: function () {}
		});

是对原生XHR的封装，除此以外还增添了对JSONP的支持。

缺点

		本身是针对MVC的编程,不符合现在前端MVVM的浪潮
		基于原生的XHR开发，XHR本身的架构不清晰，已经有了fetch的替代方案
		JQuery整个项目太大，单纯使用ajax却要引入整个JQuery非常的不合理（采取个性化打包的方案又不能享受CDN服务）

##  Axios



Axios本质上也是对原生XHR的封装，只不过它是Promise的实现版本，符合最新的ES规范，从它的官网上可以看到它有以下几条特性：

		从浏览器中创建 XMLHttpRequest
		从 node.js 发出 http 请求
		支持 Promise API
		拦截请求和响应
		转换请求和响应数据
		取消请求
		自动转换JSON数据
		客户端支持防止 CSRF/XSRF

#### 引入方式：

		$ npm install axios
		$ cnpm install axios //taobao源
		$ bower install axios
		或者使用cdn：
		<script src="https://unpkg.com/axios/dist/axios.min.js"></script>

**执行 GET 请求**

		// 向具有指定ID的用户发出请求
		axios.get('/user?ID=12345')
		.then(function (response) {
			console.log(response);
		})
		.catch(function (error) {
			console.log(error);
		});
		// 也可以通过 params 对象传递参数
		axios.get('/user', {
			params: {
			ID: 12345
			}
		})
		.then(function (response) {
			console.log(response);
		})
		.catch(function (error) {
			console.log(error);
		});

**执行 POST 请求**

		axios.post('/user', {
			firstName: 'Fred',
			lastName: 'Flintstone'
		})
		.then(function (response) {
			console.log(response);
		})
		.catch(function (error) {
			console.log(error);
		});

**执行多个并发请求**

		function getUserAccount() {
		return axios.get('/user/12345');
		}
		function getUserPermissions() {
		return axios.get('/user/12345/permissions');
		}
		axios.all([getUserAccount(), getUserPermissions()])
		.then(axios.spread(function (acct, perms) {
			//两个请求现已完成
		}));


#### axios API

**axios(config)**

		// 发送一个 POST 请求
		axios({
		method: 'post',
		url: '/user/12345',
		data: {
			firstName: 'Fred',
			lastName: 'Flintstone'
		}
		});

**axios(url[, config])**

		// 发送一个 GET 请求 (GET请求是默认请求模式)
		axios('/user/12345');

**请求方法别名**

为了方便起见，已经为所有支持的请求方法提供了别名。

		axios.request（config）
		axios.get（url [，config]）
		axios.delete（url [，config]）
		axios.head（url [，config]）
		axios.post（url [，data [，config]]）
		axios.put（url [，data [，config]]）
		axios.patch（url [，data [，config]]）

当使用别名方法时，不需要在config中指定url，method和data属性。

#### 并发

帮助函数处理并发请求。

		axios.all（iterable）
		axios.spread（callback）

**创建实例**

您可以使用自定义配置创建axios的新实例。

		axios.create（[config]）

				var instance = axios.create({
					baseURL: 'https://some-domain.com/api/',
					timeout: 1000,
					headers: {'X-Custom-Header': 'foobar'}
				});

		**请求配置**

		这些是用于发出请求的可用配置选项。 只有url是必需的。 如果未指定方法，请求将默认为GET。

		{
		// `url`是将用于请求的服务器URL
		url: '/user',
		// `method`是发出请求时使用的请求方法
		method: 'get', // 默认
		// `baseURL`将被添加到`url`前面，除非`url`是绝对的。
		// 可以方便地为 axios 的实例设置`baseURL`，以便将相对 URL 传递给该实例的方法。
		baseURL: 'https://some-domain.com/api/',
		// `transformRequest`允许在请求数据发送到服务器之前对其进行更改
		// 这只适用于请求方法'PUT'，'POST'和'PATCH'
		// 数组中的最后一个函数必须返回一个字符串，一个 ArrayBuffer或一个 Stream
		transformRequest: [function (data) {
			// 做任何你想要的数据转换
			return data;
		}],
		// `transformResponse`允许在 then / catch之前对响应数据进行更改
		transformResponse: [function (data) {
			// Do whatever you want to transform the data
			return data;
		}],
		// `headers`是要发送的自定义 headers
		headers: {'X-Requested-With': 'XMLHttpRequest'},
		// `params`是要与请求一起发送的URL参数
		// 必须是纯对象或URLSearchParams对象
		params: {
			ID: 12345
		},
		// `paramsSerializer`是一个可选的函数，负责序列化`params`
		// (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
		paramsSerializer: function(params) {
			return Qs.stringify(params, {arrayFormat: 'brackets'})
		},
		// `data`是要作为请求主体发送的数据
		// 仅适用于请求方法“PUT”，“POST”和“PATCH”
		// 当没有设置`transformRequest`时，必须是以下类型之一：
		// - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
		// - Browser only: FormData, File, Blob
		// - Node only: Stream
		data: {
			firstName: 'Fred'
		},
		// `timeout`指定请求超时之前的毫秒数。
		// 如果请求的时间超过'timeout'，请求将被中止。
		timeout: 1000,
		// `withCredentials`指示是否跨站点访问控制请求
		// should be made using credentials
		withCredentials: false, // default
		// `adapter'允许自定义处理请求，这使得测试更容易。
		// 返回一个promise并提供一个有效的响应（参见[response docs]（＃response-api））
		adapter: function (config) {
			/* ... */
		},
		// `auth'表示应该使用 HTTP 基本认证，并提供凭据。
		// 这将设置一个`Authorization'头，覆盖任何现有的`Authorization'自定义头，使用`headers`设置。
		auth: {
			username: 'janedoe',
			password: 's00pers3cret'
		},
		// “responseType”表示服务器将响应的数据类型
		// 包括 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
		responseType: 'json', // default
		//`xsrfCookieName`是要用作 xsrf 令牌的值的cookie的名称
		xsrfCookieName: 'XSRF-TOKEN', // default
		// `xsrfHeaderName`是携带xsrf令牌值的http头的名称
		xsrfHeaderName: 'X-XSRF-TOKEN', // default
		// `onUploadProgress`允许处理上传的进度事件
		onUploadProgress: function (progressEvent) {
			// 使用本地 progress 事件做任何你想要做的
		},
		// `onDownloadProgress`允许处理下载的进度事件
		onDownloadProgress: function (progressEvent) {
			// Do whatever you want with the native progress event
		},
		// `maxContentLength`定义允许的http响应内容的最大大小
		maxContentLength: 2000,
		// `validateStatus`定义是否解析或拒绝给定的promise
		// HTTP响应状态码。如果`validateStatus`返回`true`（或被设置为`null` promise将被解析;否则，promise将被
		// 拒绝。
		validateStatus: function (status) {
			return status >= 200 && status < 300; // default
		},
		// `maxRedirects`定义在node.js中要遵循的重定向的最大数量。
		// 如果设置为0，则不会遵循重定向。
		maxRedirects: 5, // 默认
		// `httpAgent`和`httpsAgent`用于定义在node.js中分别执行http和https请求时使用的自定义代理。
		// 允许配置类似`keepAlive`的选项，
		// 默认情况下不启用。
		httpAgent: new http.Agent({ keepAlive: true }),
		httpsAgent: new https.Agent({ keepAlive: true }),
		// 'proxy'定义代理服务器的主机名和端口
		// `auth`表示HTTP Basic auth应该用于连接到代理，并提供credentials。
		// 这将设置一个`Proxy-Authorization` header，覆盖任何使用`headers`设置的现有的`Proxy-Authorization` 自定义 headers。
		proxy: {
			host: '127.0.0.1',
			port: 9000,
			auth: : {
			username: 'mikeymike',
			password: 'rapunz3l'
			}
		},
		// “cancelToken”指定可用于取消请求的取消令牌
		// (see Cancellation section below for details)
		cancelToken: new CancelToken(function (cancel) {
		})
		}

使用 then 时，您将收到如下响应：

		axios.get('/user/12345')
			.then(function(response) {
			console.log(response.data);
			console.log(response.status);
			console.log(response.statusText);
			console.log(response.headers);
			console.log(response.config);
		});



## Fetch API

目前已经有一个获取 HTTP 请求更加方便的 API：Fetch，通过Fetch提供的fetch()这个全局函数方法可以很简单地发起异步请求，并且支持Promise的回调。但是 Fetch API 是比较新的 API，具体使用的时候还需要查查 [caniuse](https://caniuse.com/)，看下其浏览器兼容情况（IE11以下不兼容）。

		fetch('some/api/data.json', {
			method:'POST', //请求类型 GET、POST
			headers:{}, // 请求的头信息，形式为 Headers 对象或 ByteString
			body:{}, //请求发送的数据 blob、BufferSource、FormData、URLSearchParams（get 或head 方法中不能包含 body）
			mode:'', //请求的模式，是否跨域等，如 cors、 no-cors 或 same-origin
			credentials:'', //cookie 的跨域策略，如 omit、same-origin 或 include
			cache:'', //请求的 cache 模式: default、no-store、reload、no-cache、 force-cache 或 only-if-cached
		})
		.then(function(response) { ... });

Fetch 支持headers定义，通过headers自定义可以方便地实现多种请求方法（ PUT、GET、POST 等）、请求头（包括跨域）和cache策略等；除此之外还支持 response（返回数据）多种类型，比如支持二进制文件、字符串和formData等。