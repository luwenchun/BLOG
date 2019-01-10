---
title: Express中间件
date: 2017-12-25
---

中间件（Middleware） 是一个函数，它可以访问请求对象（request）, 响应对象（response）, 和 web 应用中处于请求-响应循环流程中的中间件(next)。

**中间件的功能包括：**

        1、执行任何代码。
        2、修改请求和响应对象。
        3、终结请求-响应循环。
        4、调用堆栈中的下一个中间件.

简单的说，中间件就是在请求-响应的过程中给我们提供一个可以修改内容的机会。

看看express中如何使用中间件

        const express = require('express');
        const app = express();

        app.use((req, res, next) => {
            console.log(1);
            next();
        });

        app.use((req, res, next) => {
            console.log(2);
            next();
        });

        app.route('/').get((req, res) => {
            res.jsonp(123);
        })

        app.listen(3031, () => {
            console.log(`connect success in http://localhost:3031`);
        });

当你访问http://localhost:3031时，终端会显示：

![你看](/../about/middleware1.png)

从上面的代码中，我们可以看到，每个use中间件(函数)，除了请求对象req、响应对象res，还有一个next，这是神马呢？

其实，它表示是下一个中间件(函数)，也就是第一个的中间件(函数)中的next是第二个中间件(函数)，如果你不调用next，就不会调用下一个中间件(函数)，也就是说到此结束了。

而express的逻辑是只有所有中间件都被执行了，才会执行路由，也就是说，如果上面的实例中，第二个中间件不调用next，就不会执行route路由。

下面来简单的实现express中的中间件

        const http = require('http');

        function express() {
            const func = [];
            let request;
            let i = 0;
            function app(req, res) {
                let count = 0;
                function next() {
                    const task = func[count++];
                        console.log(count);
                    if (!task) {
                        //等待所有中间件执行完毕，再执行路由
                        if (request && count >= func.length) {
                            request(req, res);
                        }
                        return;
                    }
                    task(req, res, next);
                }
                next();
            }
            //中间件函数
            app.use = (task) => {
                func.push(task);
            }
            // 路由函数
            app.route = (fn) => {
                request = fn;
            }

            return app;
        }
        const app = express();

        app.use((req, res, next) => {
            console.log(1);
            next();
        });

        app.use((req, res, next) => {
            console.log(2);
            next();
        });

        app.route((req, res) => {
            // 主页
        if (req.url == "/") {
            res.writeHead(200, { "Content-Type": "text/html" });
            res.end("Welcome to the homepage!");
        }

        // About页面
        else if (req.url == "/about") {
            res.writeHead(200, { "Content-Type": "text/html" });
            res.end("Welcome to the about page!");
        }
        })

        http.createServer(app).listen(3031, () => {
            console.log(`connect success in http://localhost:3031`);
        });


当访问http://localhost:3031时，可以看到中间件执行顺序：

![你看](/../about/middleware2.png)

在上面的代码中，我们实现了express函数，其中利用闭包的特性，声明了一个用来保存中间件(函数)的数组func，
同时在next函数中又声明了一个用来记录已执行的中间件的个数.

最后，定义了两个静态方法use和route。

这就是express中中间件的逻辑，每调用一次use，就将函数抛到一个数组中，然后每次处理请求时，都会调用所有中间件，也就是循环一次数组(建立在你每次中间件都调用next的情况下)，最后执行route。

