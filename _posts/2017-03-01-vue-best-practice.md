---
layout: post
title: Vue 最佳实践
category: program
---

不知不觉已经用`Vue`做了4个项目了;

整理了一些`Vue`全家桶常见的坑:

### WebStorage 中的数据不是`Observable`数据

这一条说起来是理所当然的，但是实际代码中我取巧，在`Vuex`的`Mutation`里将登录信息存储到`LocalStorage`中, 然后在`Getters`直接取用

~~~
// mutation
SET_LOGIN_SESSION({ state, token }) {
  Vue.set(state, 'token', token);
  // 持久化
  if(!Vue.$isServer) global.localStorage.setItem('token', token);
}

// getter

export token = state => state.token || global.localStorage.getItem('token');
~~~

但这样在没有重新调用`refresh_token`方法的时候，单纯对`localStorage`中`token`的修改就不会触发`Watcher`相关的任何方法啦。

### 组件的相同的生命周期钩子钩子函数, 子组件先于父组件执行

为了解决上一个问题, 我换成通过`Vuex Plugins`来控制持久化的部分. 在顶级`Layout`组件的`mounted`钩子中调用`sync`的`mutaion`将有效的token更新到`store`中去

~~~
// Layout.vue
...
mounted() {
  this.$store.commit('sync', token);
},
...
~~~

但是，子组件依然无法获取到有效的token.

原因是`Vue`中子组件的生命周期是先于父组件执行的, 大概是这样的顺序:

~~~
... -> childrenBeforeMount -> parentBeforeMount -> childrenMounted -> parentMount
~~~

所以子组件中在`mounted`里使用`token`当然取不到啦，只需要改一下生命周期就好

~~~
// Layout.vue
...
beforeMount() {
  this.$store.commit('sync', token);
},
...
~~~

### `Created`之后(包含)的钩子函数如果改动了`Watch`的变量，都会触发`Watch`函数

因为有`computed`的存在，所以`watch`中一般会声明一些非幂等的，有很多副作用的函数。

如果不希望生命周期被重复触发, 可以在`beforeCteate`中做一些处理；

同样如果在生命周期中会修改`Watch`的数据，就要注意`Watch`函数副作用的问题

### directives 里的 inserted 勾子函数会先于组件的 mounted 函数执行

### slot的原生事件会冒泡触发组件事件;

### 尽量不要使用v-html和{{{}}}

### Vuex Mutation 的时机问题

在实际的代码中，经常会需要重复加载列表数据。

这时候有几种不同的处理方案:

- 先清空数据，再插入: 由于`action`是异步的，所以如果不做适当的`throttle`就有可能造成数据正确加载之后，又被清空的问题(类似多线程)
- 直接插入数据: 不做适当的`throttle`就有可能造成数据重复加载的问题

终于在前端遇到类似多线程的问题了竟然还有点开心

### The client-side rendered virtual DOM tree is not matching server-rendered content错误

这是由于前后端对某个路由的处理不同步导致的，最经常发生的场景是某个路由需要验证登录，前端判断需要跳转登录页面，后端判断不需要登录，直接进入该路由页面，所以就导致前后端渲染的页面不一致的错误

### 在 Vuex 相关的代码中，始终保持 Immutable 的写法

不然会出现光是定位问题就需要几十个小时的诡异BUG
