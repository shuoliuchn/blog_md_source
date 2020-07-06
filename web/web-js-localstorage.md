## js 的本地存储

本地存储是原生 js 里面的内容。主要作用是方便我们临时或者永久得在本地客户端中保存数据使用。

html5提供给开发者保存数据到客户端的两个新对象：

```js
window.localStorage    // 本地存储, 永久存储数据到客户端中
window.sessionStorage  // 会话存储, 临时存储数据到客户端中,在用一个会话期保存数据
```

这两个对象都是保存数据的，只是保存数据的周期不一样而已。localStorage 是永久存储，sessionStorage 只会在当前会话期保存数据，一旦浏览器关闭，数据即消失。

这两个对象的用法基本一样，仅以 localStorage 为例：

```js
localStorage.变量名 = 变量值              // 存储数据
localStorage.setItem("变量名","变量值");  // 存储数据

localStorage.变量名                      // 获取数据
localStorage.getItem("变量名");          // 获取数据

localStorage.removeItem("变量名");       // 删除数据
localStorage.clear();                   // 清空本地存储中的所有数据
```

本地存储的作用：减少请求服务端的次数，减轻服务器的存储压力，