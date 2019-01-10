---
title: call、apply、bind 的理解与实例
date: 2018-09-15 
---


call/apply 可以改变函数的this指向。 除了传递参数时有所差别，call和apply作用完全一样。


```javascript
var tim = { 
    name: 'tim', 
    age: 20, 
    getName: function() {
        console.log(this.name);
        return this.name; 
    }
}

var jake = { name: 'jake', age: 20 }

tim.getName(); // tim

// jake对象没有getName方法，但是可以通过call/apply去使用tim对象的getName方法
tim.getName.call(jake);  // jake 
tim.getName.apply(jake); // jake
```

tim.getName.call(jake)的意思是执行getName方法，但是通过call/apply将getName方法中的this指向强行设置为jake对象。因此最终的返回结果会是jake。

call apply的不同之处在于传递参数的形式。其中apply传递的参数为数组形式, call传递的参数为按顺序依次排列。一个简单的实例说明。

```javascript
// 当参数个数不确定或者你觉得用apply比较爽时, 就直接使用apply
// 字面解释就是obj夺舍fn，obj拥有了执行fn函数的能力，并且this指向obj.
var arguments = { 0:'name', 1:'age', 2:'gender' };

fn.apply(obj, arguments);
fn.call(obj, name, age, gender);
```

- 将类数组形式转换为数组

```javascript
// arguments
// 返回值为数组，arguments保持不变
var arg = [].slice.call(arguments);

// nodeList
var nList = [].slice.call(document.getElementsByTagName('li'));
```

- 方法借用

```javascript
var foo = {
    name: 'joker',
    showName: function() {
        console.log(this.name);
    }
};
var bar = {
    name: 'rose'
};

foo.showName.call(bar); // rose
```

- 在继承中的应用

```javascript
// parent
var Person = function(name) {
    this.name = name;
    this.gender = ['man', 'woman'];
}

// child
var Student = function(name, age) {

    // inherit
    Person.call(this);
}
Student.prototype.message = function() {
    console.log('name:'+this.name+', age:'+this.age+', gender:.'+this.gender[0]);
}

var xm = new Student('xiaom', 12);
xm.message(); //name:undefined, age:undefined, gender:.man

```