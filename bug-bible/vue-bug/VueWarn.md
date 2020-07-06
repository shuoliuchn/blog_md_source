# Vue warn

## Cannot find element

### 错误 1.1

报错信息：

```js
[Vue warn]: Cannot find element: #qpp
warn @ vue.js:634
```

错误代码：

```vue
<div id="app">
    <input type="text" v-model="msg">
    <button @click="query">查询</button>
</div>
</body>
<script>
    let vm = new Vue({
        el: '#qpp',
        data: {
            msg: '',
        }
    })
```

错误原因：

代码输入有误，元素名应该是 app，写成了 qpp。

解决方法：

把 qpp 改回 app：

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

### 错误 1.2

报错信息：

```js
[Vue warn]: Cannot find element: #app
warn @ vue.js:634
```

错误代码：

```vue
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="js/vue.js"></script>
</head>
<body>
<div id="box">
    <button @click="num++">+</button>
    <input type="number" v-model="num">
    <button @click="sub">-</button>
</div>
</body>
<script>
    let vm = new Vue({
        el: '#app',
        data () {
            return {
                num: 5,
            }
        },
        methods: {
            set_zero(){
                this.num = 0
            },
            sub(){
                this.num = this.num<=1?0:this.num--
            }
        }
    })
</script>
</html>
```

错误原因：

代码输入有误，元素名应该是 box，习惯性写成了 app。

解决方法：

把 app 改为 box：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="js/vue.js"></script>
</head>
<body>
<div id="box">
    <button @click="num++">+</button>
    <input type="number" v-model="num">
    <button @click="sub">-</button>
</div>
</body>
<script>
    let vm = new Vue({
        el: '#box',
        data () {
            return {
                num: 5,
            }
        },
        methods: {
            set_zero(){
                this.num = 0
            },
            sub(){
                this.num = this.num<=1?0:this.num--
            }
        }
    })
</script>
</html>
```

## Property or method "query" is not defined on the instance but referenced during render.

### 错误 2.1

报错信息：

```js
[Vue warn]: Property or method "query" is not defined on the instance but referenced during render. Make sure that this property is reactive, either in the data option, or for class-based components, by initializing the property. See: https://vuejs.org/v2/guide/reactivity.html#Declaring-Reactive-Properties.

(found in <Root>)
warn @ vue.js:634
warnNonPresent @ vue.js:2047
has @ vue.js:2092
eval @ VM332:3
Vue._render @ vue.js:3545
updateComponent @ vue.js:4061
get @ vue.js:4472
Watcher @ vue.js:4461
mountComponent @ vue.js:4068
Vue.$mount @ vue.js:9038
Vue.$mount @ vue.js:11923
Vue._init @ vue.js:5006
Vue @ vue.js:5072
(anonymous) @ 01corstest.html?_ijt=ctqipqp7hrjsei9g4g47lt3gu3:16
```

错误代码：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="js/vue.js"></script>
    <script src="js/axios.js"></script>
</head>
<body>
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
        },
        method: {
            query(){
                axios.post('http://tingapi.ting.baidu.com/v1/restserver/ting?', {
                    params: {
                        method: 'baidu.ting.search.catalogSug',
                        query: this.msg,
                    }
                }).then(response=>{
                    console.log(response);
                    console.log(response.data);
                }).catch(error => {
                    console.log(error);
                })
            }
        }
    })
</script>
</html>
```

错误原因：

代码输入有误，元素名应该是 methods，写成了 method。

解决方法：

把 method 改回 methods：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="js/vue.js"></script>
    <script src="js/axios.js"></script>
</head>
<body>
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
        },
        methods: {
            query(){
                axios.post('http://tingapi.ting.baidu.com/v1/restserver/ting?', {
                    params: {
                        method: 'baidu.ting.search.catalogSug',
                        query: this.msg,
                    }
                }).then(response=>{
                    console.log(response);
                    console.log(response.data);
                }).catch(error => {
                    console.log(error);
                })
            }
        }
    })
</script>
</html>
```

## Error in v-on handler

### 错误 3.1

报错信息：

```js
vue.esm.js?efeb:628 [Vue warn]: Error in v-on handler: "ReferenceError: msg is not defined"

found in

---> <SearchBox> at src/components/SearchBox.vue
       <App> at src/App.vue
         <Root>
```

错误代码：

```js
data(){
    return {
        msg: ''
    }
},
methods:{
    add(){
        console.log(msg)
    }
},
```

错误原因：

方法中调用 data 中的数据时，忘记加上 this，因为找不到对应的变量，所以报错

解决方法：

在方法中的相应位置加上 this

```js
data(){
    return {
        msg: ''
    }
},
methods:{
    add(){
        console.log(this.msg)
    }
},
```

### 错误 3.2

报错信息：

```js
vue.esm.js?efeb:628 [Vue warn]: Error in v-on handler: "TypeError: Failed to execute 'setItem' on 'Storage': 2 arguments required, but only 1 present."

found in

---> <SearchBox> at src/components/SearchBox.vue
       <App> at src/App.vue
         <Root>
```

错误代码：

```js
localStorage.setItem(JSON.stringify(task_list))
```

错误原因：

localStorage 的 setItem 方法要有两个参数，即键和值。这里只写了值，没有指定键，所以报错。

解决方法：

把键指定好

```js
localStorage.setItem(’task_list‘, JSON.stringify(task_list))
```

## Unknown custom element

### 错误 1.1

报错信息：

```js
[Vue warn]: Unknown custom element: <addnum> - did you register the component correctly? For recursive components, make sure to provide the "name" option.

(found in <Root>)
warn @ vue.js:634
```

错误代码：

```js
Vue.component ('AddNum', {
        data () {
            return {
                num: 0
            }
        },
...
```

错误原因：

vue 中，组件名不要使用过多的大写字母，最好一个也不要有。大小写差异可能会造成 vue 的报错

解决方法：

把组件名改小写，可带下划线或减号。

```js
Vue.component ('add-num', {
        data () {
            return {
                num: 0
            }
        },
...
```

