---
title: 简单实现VUE中的MVVM
date: 2018-03-09
---

## defineProperty

首先，我们需要了解一下 js 中的一个 API :
[Object.defineProperty(obj, prop, descriptor)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)

一般情况下我们为一个对象添加一个属性一般都会这么写

        let object = {}
        object.test = 'test'

Object.defineProperty 也能做到同样的效果

        let object = {}, test = 'test'
        Object.defineProperty(object, 'test', {
            configurable: true,            // 描述该属性的描述符能否被改变，默认值为 false
            enumerable: true,               // 能否被遍历，比如 for in，默认值为 false
            get: function(){                // 取值的时候调用，object.test，默认值为 false
                console.log('enter get')
                return test
            },
            set: function(newValue){        // 设置值的时候使用
                console.log('enter set')
                test = newValue
            }
        })

这样写虽然代码量多了不少，但是却拥有了控制属性取值和设置值的权利，让我们来测试一下。

         object.test
        // enter get
        // test
        object.test = 'test2'
        // enter set
        // test2

接着我们把 defindProperty 这个函数封装同时改造一下，方便我们调用

        let callback = {
            target: null
        }
        let defineReactive = function(object, key, value){
            let array = []
            Object.defineProperty(object, key, {
                configurable: true,
                enumerable: true,
                get: function(){
                    if(callback.target){
                        array.push(callback.target)
                    }
                    return value
                },
                set: function(newValue){
                    if(newValue != value){
                        array.forEach((fun)=>fun(newValue, value))
                    }
                    value = newValue
                }
            })
        }

可以从代码中看出来，我在函数内部声明了一个数组用于存放 **callback** 中的 **target**，当对 **object** 进行 **get** 操作(取值操作)的时候，就会往 **callback** 中存放函数，进行 **set** 操作(设置值)的时候执行数组中的函数。看看效果如何

        let object = {}
        defineReactive(object, 'test', 'test')
        callback.target = function(newValue, oldValue){console.log('我被添加进去了，新的值是：' + newValue)}
        object.test
        // test
        callback.target = null
        object.test = 'test2'
        // 我被添加进去了，新的值是：test2
        callback.target = function(newValue, oldValue){console.log('添加第二个函数，新的值是：' + newValue)}
        object.test
        // test
        callback.target = null
        object.test = 'test3'
        // 我被添加进去了，新的值是：test3
        // 添加第二个函数，新的值是：test3

这样我们就达成了在 object.test 的值发生改变时，运行一个函数队列（虽然这个队列挺简陋的）的目的。

换个说法，当我们取值的时候，函数自动帮我们添加了针对当前值的依赖，当这个值发生变化的时候，处理了这些依赖，比如说 DOM 节点的变化。

这个也是 VUE 中实现 MVVM 的最核心的代码，当然在 VUE 中，这个依赖收集的过程远比现在的代码要复杂，这里仅仅实现了依赖的收集和触发，对于依赖的管理这里的代码还做不到。

