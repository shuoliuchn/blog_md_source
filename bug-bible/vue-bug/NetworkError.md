# Network Error

## Cannot find element

### 错误1.1

报错信息：

```js
Access to XMLHttpRequest at 'http://tingapi.ting.baidu.com/v1/restserver/ting?' from origin 'http://localhost:63342' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.
01corstest.html?_ijt=ctqipqp7hrjsei9g4g47lt3gu3:32
Error: Network Error
    at createError (axios.js:913)
    at XMLHttpRequest.handleError (axios.js:766)
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

错误原因：

浏览器的同源访问限制。因为浏览器中输入的网址与请求的域名 `http://tingapi.ting.baidu.com` 不同，所以浏览器自动拒绝返回的响应，并报错。错误的关键字是 `Access-Control-Allow-Origin`。详细内容，参见： [ajax 和同源策略](..\..\web\ajax-cors.md)。

解决方法：

我们无法修改百度的服务器，所以是无法通过浏览器访问到数据的。但是可以通过写 Python 脚本的方法，避开浏览器的限制。