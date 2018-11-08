---
title: JavaScript创建对象和实现继承
date: 2018-01-09
---

## JavaScript创建对象

### 构造函数模式创建对象

		function Person(name, job) {
			this.name = name
			this.job = job
			this.sayName = function() {
				console.log(this.name)
			}
		}
		var person1 = new Person(‘Jiang’, ‘student’)
		var person2 = new Person(‘X’, ‘Doctor’)

没有显示的创建对象，使用new来调用这个构造函数，使用new后会自动执行如下操作

		创建一个新对象
		这个新对象会被执行[[prototype]]链接
		这个新对象会绑定到函数调用的this
		返回这个对象

使用这个方式创建对象可以检测对象类型

		person1 instanceof Object // true
		person1 instanceof Person //true

但是使用构造函数创建对象，每个方法都要在每个实例上重新创建一次

### 原型模式创建对象

			function Person() {
			}
			Person.prototype.name = ‘Jiang’
			Person.prototype.job = ‘student’
			Person.prototype.sayName = function() {
			console.log(this.name)
			}
			var person1 = new Person()

将信息直接添加到原型对象上。使用原型的好处是可以让所有的实例对象共享它所包含的属性和方法，不必在构造函数中定义对象实例信息。

原型是一个非常重要的概念，在一篇文章看懂proto和prototype的关系及区别中讲的非常详细

更简单的写法

		function Person() {
		}
		Person.prototype = {
		name: ‘jiang’,
		job: ‘student’,
		sayName: function() {
			console.log(this.name)
		}
		}
		var person1 = new Person()
		
将Person.prototype设置为等于一个以对象字面量形式创建的对象，但是会导致.constructor不在指向Person了。

使用这种方式，完全重写了默认的Person.prototype对象，因此 .constructor也不会存在这里

Person.prototype.constructor === Person  // false
如果需要这个属性的话，可以手动添加

		function Person() {
		}
		Person.prototype = {
			constructor：Person
			name: ‘jiang’,
			job: ‘student’,
			sayName: function() {
				console.log(this.name)
			}
		}

不过这种方式还是不够好，应为constructor属性默认是不可枚举的，这样直接设置，它将是可枚举的。所以可以时候，Object.defineProperty方法

		Object.defineProperty(Person.prototype, ‘constructor’, {
		enumerable: false,
		value: Person
		})

缺点使用原型，所有的属性都将被共享，这是个很大的优点，同样会带来一些缺点

原型中所有属性实例是被很多实例共享的，这种共享对于函数非常合适。对于那些包含基本值的属性也勉强可以，毕竟实例属性可以屏蔽原型属性。但是引用类型值，就会出现问题了

		function Person() {
		}
		Person.prototype = {
			name: ‘jiang’,
			friends: [‘Shelby’, ‘Court’]
		}
		var person1 = new Person()
		var person2 = new Person()
		person1.friends.push(‘Van’)
		console.log(person1.friends) //[“Shelby”, “Court”, “Van”]
		console.log(person2.friends) //[“Shelby”, “Court”, “Van”]
		console.log(person1.friends === person2.friends) // true

friends存在与原型中，实例person1和person2指向同一个原型，person1修改了引用的数组，也会反应到实例person2中