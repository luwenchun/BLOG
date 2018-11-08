---
title: 基于Vue2.0实现后台系统权限控制
date: 2017-08-23
---
项目背景:现有一个后台管理系统，共存在两种类型的人员

①超级管理员（称作admin），②普通用户（称作editor）

每种类型的人看到的操作栏并不一样，可以进行的操作也不尽相同，于是就需要程序处理一下各个权限问题。

### 具体实现思路

1 创建vue实例的时候将vue-router挂载，但这个时候vue-router挂载一些登录或者不用权限的公用的页面。

2 当用户登录后，获取用role，将role和路由表每个页面的需要的权限作比较，生成最终用户可访问的路由表。

3 调用router.addRoutes(store.getters.addRouters)添加用户可访问的路由。

4 使用vuex管理路由表，根据vuex中可访问的路由渲染侧边栏组件。

**在路由router.js里面声明权限为admin的路由(异步挂载的路由asyncRouterMap)**

        // router.js
        import Vue from 'vue'
        import Router from 'vue-router'

        Vue.use(Router)

        export const constantRouterMap = [
        {
            path: '/',
            redirect: '/login',
            hidden: true
        },
        {
            path: '/login',
            name: '登录页面',
            hidden: true,
            component: resolve => require(['../views/login/Login.vue'], resolve)
        },
        {
            path: '/Readme',
            // name: 'Readmehome',
            index: 'Readme',
            meta: {
            title: 'Readme',
            icon: 'el-icon-menu'
            },
            component: resolve => require(['../components/common/Home.vue'], resolve),
            children: [
            {
                name: 'Readme',
                path: '/',
                meta: { title: 'Readme', icon: 'el-icon-menu' },
                component: resolve => require(['../components/page/Readme.vue'], resolve)
            }
            ]
        }
        ]

        export default new Router({
        routes: constantRouterMap
        })
        // 异步挂载的路由
        // 动态需要根据权限加载的路由表
        export const asyncRouterMap = [
        {
            path: '/permission',
            // name: 'permissionhome',
            meta: {
            title: 'permission',
            icon: 'el-icon-setting',
            roles: ['admin']
            },
            component: resolve => require(['../components/common/Home.vue'], resolve),
            children: [
            {
                name: 'permission',
                path: '/permission',
                meta: {
                title: 'permission', icon: 'el-icon-menu', roles: ['admin']
                },
                component: resolve => require(['../components/page/permission.vue'], resolve)
            }
            ]
        },
        { path: '*', redirect: '/404', hidden: true }
        ]


这里我们根据 [vue-router官方推荐](https://router.vuejs.org/en/advanced/meta.html) 的方法通过meta标签来标示改页面能访问的权限有哪些。如meta: { role: ['admin','super_editor'] }表示该页面只有admin和超级编辑才能有资格进入。

注意事项：这里有一个需要非常注意的地方就是 404 页面一定要最后加载，如果放在constantRouterMap一同声明了404，后面的所以页面都会被拦截到404，详细的问题见[addRoutes when you've got a wildcard route for 404s does not work](https://github.com/vuejs/vue-router/issues/1176)

**当用户登录后，获取用role，将role和路由表每个页面的需要的权限作比较，调用router.addRoutes(store.getters.addRouters)添加用户可访问的路由，生成最终用户可访问的路由表。路由表存在vuex里面**

**permission.js**

        // permission.js
        import router from './router'
        import store from './store'
        import { Message } from 'element-ui'
        import { getToken } from '@/utils/auth' // 验权

        const whiteList = ['/login', '/authredirect'] // 不重定向白名单

        router.beforeEach((to, from, next) => {
        if (getToken()) { // 判断是否有token
            if (to.path === '/login') {
            next({ path: '/' })
            } else {
            if (store.getters.roles.length === 0) {
                console.log('roles====0')
                store.dispatch('GetInfo').then(res => { // 拉取用户信息
                const roles = res.data.roles // note: roles must be a array! such as: ['editor','develop']
                console.log('roles?', roles)
                store.dispatch('GenerateRoutes', { roles }).then(() => { // 根据roles权限生成可访问的路由表
                    console.log('addrouters', store.getters.addRouters)
                    router.addRoutes(store.getters.addRouters) // 动态添加可访问路由表
                    next({ ...to, replace: true }) // hack方法 确保addRoutes已完成 ,set the replace: true so the navigation will not leave a history record
                })
                }).catch(() => {
                store.dispatch('FedLogOut').then(() => {
                    Message.error('验证失败,请重新登录')
                    next({ path: '/login' })
                })
                })
            } else {
                console.log('====1')
                next() // 当有用户权限的时候，说明所有可访问路由已生成 如访问没权限的全面会自动进入404页面
            }
            }
        } else {
            if (whiteList.indexOf(to.path) !== -1) {
            next()
            } else {
            next('/login')
            }
        }
        })

**使用vuex管理路由表，根据vuex中可访问的路由渲染侧边栏组件（菜单）。**

![copy](/../about/vuex.png)


