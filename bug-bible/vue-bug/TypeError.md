# TypeError

## sale_price is not a function

### 错误1.1

报错信息：

```js
[Vue warn]: Error in render: "TypeError: sale_price is not a function"

(found in <Root>)
warn @ vue.js:634
logError @ vue.js:1893
globalHandleError @ vue.js:1888
handleError @ vue.js:1848
Vue._render @ vue.js:3547
updateComponent @ vue.js:4061
get @ vue.js:4472
Watcher @ vue.js:4461
mountComponent @ vue.js:4068
Vue.$mount @ vue.js:9038
Vue.$mount @ vue.js:11923
Vue._init @ vue.js:5006
Vue @ vue.js:5072
(anonymous) @ vuetest6.html?_ijt=5ggpj94mjdir3hlbilh3q5krqo:15
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
<div id="app">
    原价格：{{ price|k(2) }}<br/>
    折扣价：{{ sale_price(2) }}
</div>
</body>
<script>
    let vm = new Vue({
        el: '#app',
        data: {
            price: 20.3,
            sale: 0.6,
        },
        filters: {
            k(value, num){
                return value.toFixed(num)
            }
        },
        computed: {
            sale_price(val){
                let s_price = this.price * this.sale;
                return s_price.toFixed(val)
            }
        }

    })
</script>
</html>
```

错误原因：

计算属性不能传参数。因为从名字就能看出来，属性，不是方法。

解决方法：

把计算属性 sale_price 的参数去掉。

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="js/vue.js"></script>
</head>
<body>
<div id="app">
    原价格：{{ price|k(2) }}<br/>
    折扣价：{{ sale_price }}
</div>
</body>
<script>
    let vm = new Vue({
        el: '#app',
        data: {
            price: 20.3,
            sale: 0.6,
        },
        filters: {
            k(value, num){
                return value.toFixed(num)
            }
        },
        computed: {
            sale_price(){
                let s_price = this.price * this.sale;
                return s_price.toFixed(2)
            }
        }

    })
</script>
</html>
```