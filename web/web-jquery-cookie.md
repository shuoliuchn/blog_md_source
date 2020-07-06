## jQuery 操作 Cookie

JavaScript 可以直接操作到 Cookie，这就意味着除了后端，我们也可以在前端使用到 Cookie。

使用 jQuery 操作 Cookie，需要依赖 jQuery 环境。同时，jQuery 并没有封装操作 Cookie 的组件，我们还需要引入 jQuery 的 Cookie 组件。

jQuery 的 CDN 链接：https://cdn.bootcss.com/jquery/3.4.1/jquery.min.js

jQuery Cookie 组件的 CDN 链接：https://cdn.bootcss.com/jquery-cookie/1.4.1/jquery.cookie.min.js

使用 jQuery 设置和获取 Cookie 的方式为：

```js
$.cookie('xx','sss');   // 设置cookie
$.cookie('xx');         // 获取cookie
```

以通过 cookie 解决 csrf 问题的代码为例：

```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
{% csrf_token %}
用户名:<input type="text">
密码: <input type="password">
<button id="btn">go</button>
</body>
<script src="{% static 'jquery.js' %}"></script>
<script src="{% static 'jquery.cookie.js' %}"></script>
<script>
    $('#btn').click(function () {

        var uname = $('[type="text"]').val();
        var pwd = $('[type="password"]').val();
        var csrf_token = $('[name="csrfmiddlewaretoken"]').val();

        $.cookie('xx','sss');   // 设置cookie

        $.ajax({
            url:'/login/',
            type:'post',
            // data:{uname:uname,pwd:pwd,csrfmiddlewaretoken:csrf_token},
            headers:{'X-CSRFToken': $.cookie('csrftoken')},    // 获取cookie
            data:{uname:uname,pwd:pwd,},

            success:function (res) {
                console.log(res);
            }
        })
    })
</script>
</html>
```