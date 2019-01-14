---
title: Vuex原理探索
date: 2018-11-23
---
路由Router

1. 配置 {path:'/login',component:Login}
2. 路由出口 router-view
3. 传参 
   -  {path:'/login/:id',component:Login}   $route.params.id
   - ?foo=aaa                    $route.query.foo
4. 守卫
   - 全局 router.beforeEach
   - 路由beforeEnter
   - 组件beforeRouteEnter
5. 嵌套 {children:[]}

状态管理Vuex

1. 配置

   ```
   {
       state: {
           cart: localStorage.getItem('cart')||""
       },
       mutations:{
           addCart:(state,new)=>{
               state.cart=new
           }
       },
       getter:{
            cartnum=state.cart+1
       },
       actions:{
           htttp.get("/api/get",{},res=>{
               res.code==1&& commit("addCart")
           })
       }
   }
   ```

2. 使用

   - commit()//直接修改state
   - dispatch()//通过后台接口响应状态判断是否要修改state
   - $store.state.cart//直接取state
   - $store.getter.cartnum//直接取加工后的state


### vuex

![img](https://ws3.sinaimg.cn/large/006tNc79ly1fz0jxbys94j30jh0fbwef.jpg)

```js
class KVuex {
    constructor (options) {
        this.state = options.state
        this.mutations = options.mutations
        this.actions = options.actions
        // 借用vue本身的响应式的通知机制
        // state 会将需要的依赖收集在 Dep 中
        this._vm = new KVue({
            data: {
                $state: this.state
            }
        })
    }

    commit (type, payload, _options) {
        const entry = this.mutations[type]
        entry.forEach(handler=>handler(payload))
    }

    dispatch (type, payload) {
        const entry = this.actions[type]

        return entry(payload)
    }
}

```



https://github.com/vuejs/vuex

### vue-router

使用

```js
const routes = [
    { path: '/', component: Home },
    { path: '/book', component: Book },
    { path: '/movie', component: Movie }
]

const router = new VueRouter(Vue, {
    routes
})

new Vue({
    el: '#app',
    router
})
```



```js
class VueRouter {
    constructor(Vue, options) {
        this.$options = options
        this.routeMap = {}
        this.app = new Vue({
            data: {
                current: '#/'
            }
        })

        this.init()
        this.createRouteMap(this.$options)
        this.initComponent(Vue)
    }

    // 初始化 hashchange
    init() {
        window.addEventListener('load', this.onHashChange.bind(this), false)
        window.addEventListener('hashchange', this.onHashChange.bind(this), false)
    }

    createRouteMap(options) {
        options.routes.forEach(item => {
            this.routeMap[item.path] = item.component
        })
    }

    // 注册组件
    initComponent(Vue) {
        Vue.component('router-link', {
            props: {
                to: String
            },
            template: '<a :href="to"><slot></slot></a>'
        })

        const _this = this
        Vue.component('router-view', {
            render(h) {
                var component = _this.routeMap[_this.app.current]
                return h(component)
            }
        })
    }

    // 获取当前 hash 串
    getHash() {
        return window.location.hash.slice(1) || '/'
    }

    // 设置当前路径
    onHashChange() {
        this.app.current = this.getHash()
    }
}
```



