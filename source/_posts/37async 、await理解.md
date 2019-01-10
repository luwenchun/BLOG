---
title: 理解async/await
date: 2018-08-29 
---

### CallBacks

回调函数就是函数A作为参数传递给函数B，并且在未来某一个时间被调用。callback的异步模式最大的问题就是，理解困难加回调地狱（callback hell），看下面的代码的执行顺序：


        A();
        ajax('url1', function(){
            B();
            ajax('url2', function(){
                C();
            }
            D();
        });
        E();


其执行顺序为：A => E => B => D => C，这种执行顺序的确会让人头脑发昏，另外由于由于多个异步操作之间往往会耦合，只要中间一个操作需要修改，那么它的上层回调函数和下层回调函数都可能要修改，这就陷入了回调地狱。而 Promise 对象就很好的解决了异步操作之间的耦合问题，让我们可以用同步编程的方式去写异步操作。


### Promise

Promise 对象是一个构造函数，用来生成promise实例。Promise 代表一个异步操作，有三种状态：pending，resolved（异步操作成功由 pending 变为 resolved ），rejected（异步操作失败由 pending 变为 rejected ），一旦变为后两种状态将不会再改变。Promise 对象作为构造函数接受一个函数作为参数，而这个函数又接受 resolve 和 reject 两个函数做为参数，这两个函数是JS内置的，无需配置。resolve 函数在异步操作成功后调用，将pending状态变为resolved，并将它的参数传递给回调函数；reject 函数在异步操作失败时调用，将pending状态变为rejected，并将参数传递给回调函数。


- Promise.prototype.then()

Promise构造函数的原型上有一个then方法，它接受两个函数作为参数，分别是 resolved 状态和 rejected 状态的回调函数，而这两个回调函数接受的参数分别是Promise实例中resolve函数和reject函数中的参数。 另外rejected状态的回调函数是可省略的。

```javascript
const instance = new Promise((resolve, reject) => {
    // 一些异步操作
    if(/*异步操作成功*/) {
      resolve(value);
    } else {
      reject(error);
    }
  }
})
instance.then(value => {
  // do something...
}, error => {
  // do something...
})
```

注意Promise实例在生成后会立即执行，而 then 方法只有在所有同步任务执行完后才会执行，看看下面的例子：



```javascript
const promise = new Promise((resolve, reject) => {
  console.log('async task begins!');
  setTimeout(() => {
    resolve('done, pending -> resolved!');
  }, 1000);
})

promise.then(value => {
  console.log(value);
}) 

console.log('1.please wait');
console.log('2.please wait');
console.log('3.please wait');
// async task begins!
// 1.please wait
// 2.please wait
// 3.please wait
// done, pending -> resolved!
```


- 链式调用 then 方法


用 then 的链式写法，按顺序实现一系列的异步操作，这样就可以用同步编程的形式去实现异步操作，来看下面的例子，实现隔两秒打一次招呼：


```javascript
function sayHi(name) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(name);
    }, 2000)
  })
}

sayHi('张三')
  .then(name => {
    console.log(`你好， ${name}`);
    return sayHi('李四');    // 最终 resolved 函数中的参数将作为值传递给下一个then
  })
  // name 是上一个then传递出来的参数
  .then(name => {                
    console.log(`你好， ${name}`);
    return sayHi('王二麻子');
  })
  .then(name => {
    console.log(`你好， ${name}`);
  })
// 你好， 张三
// 你好， 李四
// 你好， 王二麻子
```


可以看到使用链式then的写法，将异步操作变成了同步的形式，但是也带来了新的问题，就是异步操作变成了很长的then链，新的解决方法就是Generator，这里跨过它直接说它的语法糖：async/await。


### async/await

- async

async/await实际上是Generator的语法糖。顾名思义，async关键字代表后面的函数中有异步操作，await表示等待一个异步方法执行完成。声明异步函数只需在普通函数前面加一个关键字async即可，如：

```javascript
async function funcA() {}
```

async 函数返回一个Promise对象（如果指定的返回值不是Promise对象，也返回一个Promise，只不过立即 resolve ，处理方式同 then 方法），因此 async 函数通过 return 返回的值，会成为 then 方法中回调函数的参数：

```javascript
async function funcA() {
  return 'hello!';
}

funcA().then(value => {
  console.log(value);
})
// hello!
```

单独一个 async 函数，其实与Promise执行的功能是一样的，来看看 await 都干了些啥。

- await

顾名思义， await 就是异步等待，它等待的是一个Promise，因此 await 后面应该写一个Promise对象，如果不是Promise对象，那么会被转成一个立即 resolve 的Promise。 async 函数被调用后就立即执行，但是一旦遇到 await 就会先返回，等到异步操作执行完成，再接着执行函数体内后面的语句。总结一下就是：async函数调用不会造成代码的阻塞，但是await会引起async函数内部代码的阻塞。看看下面这个例子：

```javascript
async function func() {
  console.log('async function is running!');
  const num1 = await 200;
  console.log(`num1 is ${num1}`);
  const num2 = await num1+ 100;
  console.log(`num2 is ${num2}`);
  const num3 = await num2 + 100;
  console.log(`num3 is ${num3}`);
}

func();
console.log('run me before await!');
// async function is running!
// run me before await!
// num1 is 200
// num2 is 300
// num3 is 400
```

** 值得注意的是， await 后面的 Promise 对象不总是返回 resolved 状态，只要一个 await 后面的Promise状态变为 rejected ，整个 async 函数都会中断执行，为了保存错误的位置和错误信息，我们需要用 try...catch 语句来封装多个 await 过程，如下： **

```javascript
async function func() {
  try {
    const num1 = await 200;
    console.log(`num1 is ${num1}`);
    const num2 = await Promise.reject('num2 is wrong!');
    console.log(`num2 is ${num2}`);
    const num3 = await num2 + 100;
    console.log(`num3 is ${num3}`);
  } catch (error) {
    console.log(error);
  }
}

func();
// num1 is 200
// 出错了
// num2 is wrong!
```

如上所示，在 num2 处 await 得到了一个状态为 rejected 的Promise对象，该错误会被传递到 catch 语句中，这样我们就可以定位错误发生的位置。

- async/await比Promise强在哪儿？

接下来我们用async/await改写一下Promise章节中关于sayHi的一个例子，代码如下：

```javascript
function sayHi(name) {
  return new Promise((resolved, rejected) => {
    setTimeout(() => {
      resolved(name);
    }, 2000)
  })
}

async function sayHi_async(name) {
  const sayHi_1 = await sayHi(name)
  console.log(`你好， ${sayHi_1}`)
  const sayHi_2 = await sayHi('李四')
  console.log(`你好， ${sayHi_2}`)
  const sayHi_3 = await sayHi('王二麻子')
  console.log(`你好， ${sayHi_3}`)
}

sayHi_async('张三')
// 你好， 张三
// 你好， 李四
// 你好， 王二麻子
```






