# TypeError

## this.info is not iterable"

### 错误1.1

报错信息：

```python
[Vue warn]: Error in render: "TypeError: this.info is not iterable"

(found in <Root>)
warn @ vue.js:634

TypeError: this.info is not iterable
    at Vue.total_amt (4-购物车.html?_ijt=97becnbh2f1hd1bjjkg858h7cd:61)
    at Watcher.get (vue.js:4472)
    at Watcher.evaluate (vue.js:4577)
    at Proxy.computedGetter (vue.js:4826)
    at Proxy.eval (eval at createFunction (vue.js:11628), <anonymous>:3:786)
    at Vue._render (vue.js:3545)
    at Vue.updateComponent (vue.js:4061)
    at Watcher.get (vue.js:4472)
    at new Watcher (vue.js:4461)
    at mountComponent (vue.js:4068)
```

错误代码：

```python
    let vm = new Vue({
        el: '#app',
        data: {
            info: {
                1: {id: 1, name: 'oldboy plus 5', price: 1888, amount: 1},
                2: {id: 2, name: 'oldboy plus 6', price: 2888, amount: 3},
                3: {id: 3, name: 'oldboy plus 7', price: 2888, amount: 5},
            }
        },
        computed: {
            total_amt(){
                let total = 0;
                for (goods of this.info) {
                    total += (goods.price + goods.amount)
                }
                return total
            }
        },
```

错误原因：

自定义对象的 for 循环不可以使用 of，数组循环才可以使用。自定义对象的 for 循环只能用 in。

解决方法：

把 of 改成 in。

```python
    let vm = new Vue({
        el: '#app',
        data: {
            info: {
                1: {id: 1, name: 'oldboy plus 5', price: 1888, amount: 1},
                2: {id: 2, name: 'oldboy plus 6', price: 2888, amount: 3},
                3: {id: 3, name: 'oldboy plus 7', price: 2888, amount: 5},
            }
        },
        computed: {
            total_amt(){
                let total = 0;
                for (goods in this.info) {
                    total += (goods.price + goods.amount)
                }
                return total
            }
        },
```

