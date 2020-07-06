## Django 使用 ajax 和通过 csrf 认证的三种方式

### ajax 特点

1. 局部刷新
2. 异步请求

### Django 中 ajax 的写法

ajax 是封装在 jQuery 中的，要使用 ajax，首先要引入 jQuery。

```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<h1>登录页面</h1>
{#<form action="" method="post">#}
{#    {% csrf_token %}#}
{#    用户名: <input type="text" name="username">#}
{#    密码: <input type="text" name="password">#}
{#    <input type="submit">#}
{#</form>#}

{% csrf_token %}
<hr>
用户名: <input type="text" id="uname">
密码: <input type="password" id="pwd">
<button id="sub">提交</button>
<span id="error" style="color:red;font-size: 12px;"></span>
</body>

<!-- jQuery中封装了ajax -->
<script src="https://cdn.bootcss.com/jquery/3.4.1/jquery.js"></script>
{#<script src="{% static 'js/xx.js' %}"></script>#}
<script>
    $('#sub').click(function () {

        var uname = $('#uname').val();
        var pwd = $('#pwd').val();
        var csrf_token = $('[name="csrfmiddlewaretoken"]').val();

        $.ajax({
            url: '{% url "login" %}',    // 请求路径
            type: 'post',    // 请求方法
            // 请求数据,不需要携带数据的请求，不需要写data参数
            data: {'uname': uname, 'pwd': pwd, 'csrfmiddlewaretoken': csrf_token},
            success: function (res) {
                console.log('>>>>', res);    // res参数拿到的是响应数据
                if (res !== 'ok') {
                    $('#error').text('用户名或者密码有误!')
                }else {
                    location.href='/home/';
                }
            }
        })
    })
</script>
</html>
```

### CSRF 简介

CSRF（Cross-site request forgery），中文名称：跨站请求伪造，也被称为：one click attack/session riding，缩写为：CSRF/XSRF。攻击者通过 HTTP 请求将数据传送到服务器，从而盗取回话的 cookie。盗取回话 cookie 之后，攻击者不仅可以获取用户的信息，还可以修改该 cookie 关联的账户信息。

所以解决 CSRF 攻击的最直接的办法就是生成一个随机的 csrftoken 值，保存在用户的页面上，每次请求都带着这个值过来完成校验。

### ajax 通过认证

#### 方式 1 通过 input 标签获取

通过找到 csrf 逻辑经过模板渲染后生成的 input 标签，获取到 csrf token 的值。

##### hmtl 代码

ajax 可以直接提交数据，所以不需要将 input 标签包裹在 form 标签之中。

```html
{% csrf_token %} 
用户名: <input type="text" id="uname">
密码: <input type="password" id="pwd">
<button id="sub">提交</button>
```

##### js 代码

```js
$('#sub').click(function () {

    var uname = $('#uname').val();
    var pwd = $('#pwd').val();
    // 获取到{% csrf_token %}这个模板渲染语法渲染之后的input标签对应的值
    var csrf_token = $('[name="csrfmiddlewaretoken"]').val();

    $.ajax({
        url: '{% url "login" %}',  // 127.0.0.1:8000/login/
        type: 'post',
        // 给post请求提交的数据中添加上csrf_token认证所需要的键值对
        data: {'uname': uname, 'pwd': pwd, 'csrfmiddlewaretoken': csrf_token},
           success:function (res) {
        console.log('>>>>',res);
        if (res !== 'ok'){
            $('#error').text('用户名或者密码有误!')

        }else {
            location.href='/home/';
        }
    }
})
})
```

#### 方式 2 模板渲染直接获取

data 数据部分的 csrf_token 认证的键值对的值直接写

```jinja2
{{ csrf_token }}
```

经过模板渲染之后，它直接就是那个 input 标签的 value 值。

##### html 代码

同样，不需要将 input 标签包裹在 form 标签中。

```html
用户名: <input type="text" id="uname">
密码: <input type="password" id="pwd">
<button id="sub">提交</button>
```

##### js 代码

```js
$('#sub').click(function () {

    var uname = $('#uname').val();
    var pwd = $('#pwd').val();

    $.ajax({
        url: '{% url "login" %}',  // 127.0.0.1:8000/login/
        type: 'post',
        // 需要注意的是：经过模板渲染后的内容是不会带引号的，需要我们手动给csrf_token加引号，表示这是一个字符串，否则将会按照变量来解析，从而出错
        data: {'uname':uname,'pwd':pwd,'csrfmiddlewaretoken':'{{ csrf_token }}'},
        success: function (res) {
            console.log('>>>>',res);
            if (res !== 'ok'){
                $('#error').text('用户名或者密码有误!')
            }else {
                location.href='/home/';
            }
        }
    })
})
```

#### 方式 3 通过 Cookie 获取

jQuery 中没有封装操作 Cookie 的代码，需要我们先引入。

```html
<script src="{% static 'jquery.js' %}"></script>
<script src="{% static 'jquery.cookie.js' %}"></script>
<script>
    $('#btn').click(function () {

        var uname = $('[type="text"]').val();
        var pwd = $('[type="password"]').val();
        // var csrf_token = $('[name="csrfmiddlewaretoken"]').val();

        $.ajax({
            url: '/login/',
            type: 'post',
            // data:{uname:uname,pwd:pwd,csrfmiddlewaretoken:csrf_token},
            headers: {'X-CSRFToken': $.cookie('csrftoken')},    // 获取cookie中的csrftoken，将其设置为ajax的请求头
            data: {uname: uname, pwd: pwd},
            success: function (res) {
                console.log(res);
            }
        })
    })
</script>
```



