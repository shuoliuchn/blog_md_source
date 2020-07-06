# Uncaught Error

## route config "component" for path: / cannot be a string id

### 错误 1.1

报错信息：

```js
Uncaught Error: [vue-router] route config "component" for path: / cannot be a string id. Use an actual component instead.
    at assert (vue-router.esm.js?fe87:10)
    at addRouteRecord (vue-router.esm.js?fe87:1325)
    at eval (vue-router.esm.js?fe87:1282)
    at Array.forEach (<anonymous>)
    at createRouteMap (vue-router.esm.js?fe87:1281)
    at createMatcher (vue-router.esm.js?fe87:1471)
    at new VueRouter (vue-router.esm.js?fe87:2690)
    at eval (index.js?3672:8)
    at Object../src/router/index.js (app.js:1315)
    at __webpack_require__ (app.js:679)
```

错误代码：

```html
<template>
    首页页面组件
  <br>
  <a href="/user">a 标签的个人中心</a>
  <router-link to="/user">个人中心1</router-link>
  <router-link :to="url">个人中心1</router-link>
  <router-link :to="{name: 'User'}">个人中心1</router-link>
</template>
```

错误原因：

组件中的 template 标签代码应该用一个总的标签包裹起来，一般用的是 div 标签。

解决方法：

使用 div 标签包裹代码。

```html
<div id="app">
    <input type="text" v-model="msg">
    <button @click="query">查询</button>
</div>
</body>
<script>
    let vm = new Vue({
        el: '#app',
        data: {
            msg: '',
        }
    })
```