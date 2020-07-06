## 通过 axios 实现数据请求



vue.js 默认没有提供 ajax 功能的。

所以使用 vue 的时候，一般都会使用 axios 的插件来实现 ajax 与后端服务器的数据交互。

注意，axios 本质上就是 javascript 的 ajax 封装，所以会被**同源策略**限制。

下载地址：

```
https://unpkg.com/axios@0.18.0/dist/axios.js
https://unpkg.com/axios@0.18.0/dist/axios.min.js
```

axios 提供发送请求的常用方法有两个：`axios.get()` 和 `axios.post()` 。其他常用的方法还有：

```
增   post
删   delete
改   put（修改整个数据的所有字段，最常用）/patch（修改数据的单个字段）
查   get 
```

axios 发送 get 请求的写法为：

```javascript
// 发送get请求
// 参数1: 必填，字符串，请求的数据接口的url地址，例如请求地址：http://www.baidu.com?id=200
// 参数2：可选，json对象，要提供给数据接口的参数
// 参数3：可选，json对象，请求头信息
axios.get('服务器的资源地址',{    // 'http://www.baidu.com'
        params:{  // get参数的地址可写在这里，也可直接写在地址中
            参数名:'参数值', // id: 200,
        },
        header:{  // 请求头，若没有可不加此参数

        }
    }).then(function (response) { // 请求成功以后的回调函数
        console.log("请求成功");
        console.log(response.data); // 获取服务端提供的数据

    }).catch(function (error) {   // 请求失败以后的回调函数（如果then里面代码报错，也会执行这里的代码）
        console.log("请求失败");
        console.log(error.response);  // 获取错误信息
});
```

axios 发送 post 请求的写法同 get 请求基本一致，发送 put、delete 等其他请求也是这样写，只需把方法名替换即可：

```javascript
// 发送post请求，参数和使用和axios.get()一样。
// 参数1: 必填，字符串，请求的数据接口的url地址
// 参数2：必填，json对象，要提供给数据接口的参数,如果没有参数，则必须使用{}
// 参数3：可选，json对象，请求头信息
axios.post('服务器的资源地址',{
        username: 'xiaoming',
        password: '123456'
    },{
        responseData:"json",
    })
        .then(function (response) { // 请求成功以后的回调函数
        console.log(response);
    })
        .catch(function (error) {   // 请求失败以后的回调函数
        console.log(error);
});
```

