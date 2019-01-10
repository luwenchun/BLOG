---
title: vue组件之间的数据传递及共享
date: 2018-04-21
---

最近在整理项目中代码，在组件之间数据传递遇到了问题，所以做了这次总结，如有不对的地方，望指正。

### 父组件如何将数据传到子组件中

父组件可以通过Prop传递数据到子组件中。

这里需要注意的是：

		Prop 是单向绑定的：当父组件的属性变化时，将传导给子组件，但是反过来不会。这是为了防止子组件无意间修改了父组件的状态，
		来避免应用的数据流变得难以理解。

		每次父组件更新时，子组件的所有 Prop 都会更新为最新值。这意味着你不应该在子组件内部改变 prop。如果你这么做了，
		Vue 会在控制台给出警告。

 在两种情况下，我们很容易忍不住想去修改 prop 中数据：

1、Prop 作为初始值传入后，子组件想把它当作局部数据来用；

解决方法：定义一个局部变量，并用 prop 的值初始化它：

		props: ['initialCounter'],
		data: function () {
		return { counter: this.initialCounter }
		}    

2、Prop 作为原始数据传入，由子组件处理成其它数据输出。

解决方法： 定义一个计算属性，处理 prop 的值并返回：

		props: ['size'],
		computed: {
		normalizedSize: function () {
			return this.size.trim().toLowerCase()
		}
		}

PS：上边的内容是vue文档里边有说的，我只是把自己在项目中遇到的问题抽出来了。

		// 父组件 index.vue
		<template>
			<div class="content">
				<child :lists="lists"></child>
			</div>
		</template>
		<script>
			import child from './child.vue';
			export default {
				components: {
					child
				},
				data() {
					return {
						lists: []
					};
				},
				mounted() {
					this.lists = [{
						name: '01',
						content: 'hi,'
					}, {
						name: '02',
						content: 'my name is Ellyliang'
					}];
				}
			};
		</script>

		// 子组件 child.vue
		<template>
			<ul class="content">
			<li v-for="(list, index) in getLists" :key="index" v-html="list.name + list.content"></li>
			</ul>
		</template>
		<script>
			export default {
				props: ['lists'],
				data() {
					return {
						getLists: this.lists
					};
				},
				mounted() {
					this.getLists.push({
						name: '03',
						content: '不要在乎内容，我就是测试'
					});
				}
			};
		</script>

### 子组件如何将数据传到父组件中

子组件可通过vm.$emit将数据传递给父组件

 **vm.$emit是啥**

触发当前实例上的事件。附加参数都会传给监听器回调

		// 父组件 index.vue
		<template>
			<div class="content">
				<child :lists="lists" @listenToChild="getChildMsg"></child>
			</div>
		</template>
		<script>
			import child from './child.vue';
			export default {
				components: {
					child
				},
				data() {
					return {
						lists: []
					};
				},
				mounted() {
					this.lists = [{
						name: '01',
						content: 'hi,'
					}, {
						name: '02',
						content: 'my name is Ellyliang'
					}];
				},
				methods: {
					getChildMsg(val) {
						alert(val);  // 'hello'
					}
				}
			};
		</script>

		// 子组件 child.vue
		<template>
			<div class="content">
				<ul class="lists">
					<li v-for="(list, index) in getLists" :key="index" v-html="list.name + list.content"></li>
				</ul>
			</div>
		</template>
		<script>
			export default {
				props: ['lists'],
				data() {
					return {
						getLists: this.lists
					};
				},
				mounted() {
					this.getLists.push({
						name: '03',
						content: '不要在乎内容，我就是测试'
					});
					
					setTimeout(() => {
						this.$emit('listenToChild', 'hello');
					}, 15000);
				}
			};
		</script>

子组件给父组件传数据是不是也很方便。实现方法是就是在子组件中$emit数据，然后在父组件中通过事件@事件名接收值。

### Event Bus

事件巴士这种方法，不仅能处理父子组件传递，子父组件传递，还是处理平级组件之间的数值传递。其实现方法就是在全局new一个vue实例，然后传值给bus, 就是let bus = new vue();。通过这个全局的bus中的$emit, $on等等去实现数据的传递。这样处理有个问题，由于event bus处理数据传递很方便，不管在哪里都可以传递，这样导致滥用，从而导致代码很难去理解。

 Event Bus实现

		let bus = new Vue();
		// 触发组件 A 中的事件
		bus.$emit('id-selected', 1);
		// 在组件 B 创建的钩子中监听事件
		bus.$on('id-selected', function (id) {
		// ...
		});

具体组件的封装和使用，可参考vue-bus。

### vuex

vuex官网的专业术语，让有些人还是感觉，摸不着头脑，做一些实用场景给大家看

		state 用来数据共享数据存储
		mutation 用来注册改变数据状态
		getters 用来对共享数据进行过滤操作
		action 解决异步改变共享数据

此时采用的演示方式还是用官方提供的vue-cli webpack版本

在src目录下我们创一个vuex文件夹，分别创建index.js,mutations.js,state.js,getters.js,actions.js

这样我们可对四种特性进行分文件，这样可以更加明确，清楚

![目录](https://lc-mhke0kuv.cn-n1.lcfile.com/74a488ee6821acb7e263.png)

我们分别把这四个特性放入index.js中进行store的实列化

![state主目录](https://lc-mhke0kuv.cn-n1.lcfile.com/df4e0b9aed60a84d45ef.png)

再把实列化的store引入就是所谓的index.js文件夹引入到main.js中，也可以同时把store注册到每一个组件中

![state实例化](https://lc-mhke0kuv.cn-n1.lcfile.com/60c0bddd7c2b31df2879.png)

#### state如何用？

在页面中，title肯定是必备，那每个组件页面的title都肯定不一样，那我们如何去拿到title,title适合放在那里，根据每个页面切换，而改变title,这个牵扯的就是组件与组件中的通信

我们可以在state.js中先声明一数据值

		export default{
			title : "首页"
		}

**那我们如何在首页中拿到这个title值**

![](https://lc-mhke0kuv.cn-n1.lcfile.com/a6dfd2f072b4b9a012e9.jpg)

那我们在mian.js中再加入new Vue,绑定title作用域的实例代码

我们在computed里进行数据监听，

此时我们就可以从store里拿到state.title

最后一步，我们在index.html中我们再进行{{title}}绑定

**index.html**

		<title>{{title}}</title>

此时我们运行一下，打开dev-tools你会发现

![](https://lc-mhke0kuv.cn-n1.lcfile.com/46eded6760bba9be1619.jpg)

title这个数据已经在全局被共享了

#### matutions如何使用

应用场景：

如果我们要改变顶层的共享数据，我们应该要用matutions来进行改变，如果你做公众号，后台一般会在连接的上给你一些参数，比方说sid,ck,tm,或者一些其它东西，你想把他存在state中，如果去做，那我们就通过matutions来进行注册事件，为了演示，我才这么做

注意：
对于vuex，我只推荐state状态存储只在一个页面中组件与组件之间的通信，不适合跨页面， 放一些状态

**state.js**

		export default{
			START_PARMA : {},
			title : "首页"
		}

START_PARMA用来存放我以上连接参数的数据，我们先前一定要定意好

**mutations.js**

		export default{
			getParam (state,Object) {
			state.START_PARMA = Object
			}
		}

我们对改变state数据进行一个事件注册，第一个参数是拿到state对象，第二个是传入的参数

* getParam官方说是type，其实就是注册的事件名
* 可以是单个参数
* 如果是多个参数，我们则用对象放入，如果你写两个参数，会报错

		export default {
				name : 'advertisement',
				created () {
					const keyCode = sessionStorage.keyCode = getQueryString('keyCode')
					const keyWord = sessionStorage.keyWord =  keyCode.split("_")[0]
					const hunterCode = sessionStorage.hunterCode = keyCode.split("_")[1]
					const sid = sessionStorage.sid= getQueryString('sid')
					const ck = sessionStorage.ck = getQueryString('ck')
					const tm = sessionStorage.tm = getQueryString('tm')
					this.$store.commit('getParam',{
						keyCode,
						keyWord,
						hunterCode,
						sid,
						ck,
						tm
					})
				}
			}

我们自己创建一个视图，然后在created里进行截取参数，因为store是注册到每个组件中的，所以我们要用this.$store来访问，那commit就是一个触发器，第一个是type类形名，第二个参我们用对象的方式传入，里面用的是es6的语法

此时你会发现

![](https://lc-mhke0kuv.cn-n1.lcfile.com/f0aa5d0df0fbe76c49f3.png)

此时的截取的状态放到了一个对象里，我们就可以使用了

#### getters如何使用

如果说getter就是对state里的数据进行一些过滤，改造等等

那比方说State里有一些这样的数据

**state.js**

		people : [
				{name : 'ziksang1',age:21},
				{name : 'ziksang2',age:10},
				{name : 'ziksang3',age:30},
				{name : 'ziksang4',age:40},
				{name : 'ziksang5',age:50},
				{name : 'ziksang6',age:30},
				{name : 'ziksang7',age:80}
			]


如果我们定义这些数据，然后我们要从，这些数据中筛选出年纪大于30的人，再进行返回，我们就可以用到getter,这里的getter的意思就是对vuex顶层数据进行过滤，而不改动state里原本的数据

**getters.js**

		export default{
			changePeople: (state) =>{
				return state.people.filter(item=>{
					if(item.age>30){
						return true
					}
				})
			}
		}

好我们如何应用呢，我们在组件中里只要写入

		created () {
				console.log(this.changePeople)
		},
		computed : {
					changePeople () {
						return this.$store.getters.changePeople
					}
		},

那我们可以打开命台看一下，看回的数据，

![](https://lc-mhke0kuv.cn-n1.lcfile.com/6dca03740c0b56087763.jpg)
接下来你如何想对数据进行操作那就看你自己的了

#### action 如何使用？

action.是用来解决异步流程来改变state数据的，有想人说，那我直接在matution里面进行写进不就行了麻，那你可以试一下，因为matution是直接进行同步操作的

**mutations.js**

		export default{
			getParam (state,Object) {
			setTimeout(()=>{
					state.START_PARMA = Object
			},1000)
			}
		}

还是拿上面例子，如果你在mutations里进行异步操作，你会发现没用，并不会起任何效果，那怎么办，只有通过action->mutations->states,这个流程进行操作

**action.js**

		export default {
			getParamSync (context,Object) {
				setTimeout(()=>{
					context.commit('getParam',Object)
				},3000)
			}
		}

写一个getParamSync函灵敏，第一个参数就是上下文，context是一个store对象，你也可以用解构的方式写出来,第二个参数还是我们要写入的接收到的参数，来改变触发mutations事件,再通过mutation来改变state,很好理解不难

然后我们就在组件里这么调用就可以了

            this.$store.dispatch('getParamSync',{
                keyCode,
                keyWord,
                hunterCode,
                sid,
                ck,
                tm
            })

那组合action又是怎么玩呢？我们有时候向后台请求时，要通过第一个AJAX返回值来进行下一个action分发

我们可以用promise来进行异步处理

**actions.js**

		export default {
			getParamSync (context,Object) {
				return new Promise((reslove,reject)=>{
					setTimeout(()=>{
						context.commit('getParam',Object)
						return reslove('成功')
					},3000)
				})

			},
			changetitleSync ({commit},title){
				setTimeout(()=>{
					commit('changetitle',title)
				},3000)
			}
		}

在getParamSync使用new promise之后，在resolve里返回‘成功’，再分发一个changetitleSync改变title的action方法

![](https://lc-mhke0kuv.cn-n1.lcfile.com/4a3b12a68fd64e5aa0ea.png)

**mutations.js**

		export default{
			getParam (state,Object) {
			state.START_PARMA = Object
			},
			changetitle (state,title){
				state.title = title
			}
		}

再在注册一个改变title的changetitle的type类型

**组中间调用**

		created(){
		this.$store.dispatch('getParamSync',{
						keyCode,
						keyWord,
						hunterCode,
						sid,
						ck,
						tm
					}).then((res)=>{
						this.$store.dispatch('changetitleSync',res)
					})
		}

我们就可以在组件中进行一种链式调用，解决异步回调，来action套action,就成了一个组合action


**关于其它辅助用法**

* mapState 辅助函数
* mapGetters 辅助函数
* mapMutations 辅助函数
* mapActions 辅助函数

尤大神已经写的很全面了，大家可以参考vuex的官网，进行阅读一下。我在这里就没有必要再进行重新讲解了

**在vuex中，我认为vuex用在那里比较好？**

只适合运用于一个视图内组件与组件之间的组合传递，不适用于跨页面，但是可以共用，为了不因用户刷新页面，要进行初始化再次调用。

**不适合运用在那里**

在自己写一些Ui组件给大家或者开源用的话，不适用于写在vuex中，应该暴露所接收的Props,通过$emit来进行触发事件，一些关键性全局状态，不也适合存vuex中，你可以选择localStorage,sessionStorage


