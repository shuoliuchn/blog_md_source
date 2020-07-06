# 用户登陆认证

[TOC]

## 前端显示登陆页面

### 登录页组件

`Login.vue` 组件代码：

```vue
<template>
    <div class="sign">
    <div class="logo"><a href="/"><img src="/static/image/nav-logo.png" alt="Logo"></a></div>
    <div class="main">


<h4 class="title">
  <div class="normal-title">
    <a class="active" href="/login">登录</a>
    <b>·</b>
    <a id="js-sign-up-btn" class="" href="/register">注册</a>
  </div>
</h4>
<div class="js-sign-in-container">
  <form id="new_session" action="" method="post">
      <div class="input-prepend restyle js-normal">
        <input placeholder="手机号或邮箱" type="text" name="session[email_or_mobile_number]" id="session_email_or_mobile_number">
        <i class="iconfont ic-user"></i>
      </div>
    <!-- 海外登录登录名输入框 -->

    <div class="input-prepend">
      <input placeholder="密码" type="password" name="password" id="session_password">
      <i class="iconfont ic-password"></i>
    </div>
    <div class="remember-btn">
      <input type="checkbox" value="true" checked="checked" name="remember_me" id="session_remember_me"><span>记住我</span>
    </div>
    <div class="forget-btn">
      <a class="" data-toggle="dropdown" href="">登录遇到问题?</a>
    </div>
    <button class="sign-in-button" id="sign-in-form-submit-btn" type="button">
      <span id="sign-in-loading"></span>
      登录
    </button>
</form>
  <!-- 更多登录方式 -->
  <div class="more-sign">
    <h6>社交帐号登录</h6>
    <ul>
  <li id="weibo-link-wrap" class="">
    <a class="weibo" id="weibo-link">
      <i class="iconfont ic-weibo"></i>
    </a>
  </li>
  <li><a id="weixin" class="weixin" target="_blank" href=""><i class="iconfont ic-wechat"></i></a></li>
  <li><a id="qq" class="qq" target="_blank" href=""><i class="iconfont ic-qq_connect"></i></a></li>
</ul>
  </div>
</div>

    </div>
  </div>
</template>

<script>
    export default {
        name: "Login"
    }
</script>

<style scoped>
input{
  outline: none;
}
*, :after, :before {
    box-sizing: border-box;
}
.sign {
	height: 100%;
	min-height: 750px;
	text-align: center;
	font-size: 14px;
	background-color: #f1f1f1
}

.sign:before {
	content: "";
	display: inline-block;
	height: 85%;
	vertical-align: middle
}

.sign .disable,.sign .disable-gray {
	opacity: .5;
	pointer-events: none
}

.sign .disable-gray {
	background-color: #969696
}

.sign .tooltip-error {
	font-size: 14px;
	line-height: 25px;
	white-space: nowrap;
	background: none
}

.sign .tooltip-error .tooltip-inner {
	max-width: 280px;
	color: #333;
	border: 1px solid #ea6f5a;
	background-color: #fff
}

.sign .tooltip-error .tooltip-inner i {
	position: static;
	margin-right: 5px;
	font-size: 20px;
	color: #ea6f5a;
	vertical-align: middle
}

.sign .tooltip-error .tooltip-inner span {
	vertical-align: middle;
	display: inline-block;
	white-space: normal;
	max-width: 230px
}

.sign .tooltip-error.right .tooltip-arrow-border {
	border-right-color: #ea6f5a
}

.sign .tooltip-error.right .tooltip-arrow-bg {
	left: 2px;
	border-right-color: #fff
}

.sign .slide-error {
	position: relative;
	padding: 10px 0;
	border: 1px solid #c8c8c8;
	border-radius: 4px
}

.sign .slide-error i {
	position: static!important;
	margin-right: 10px;
	color: #ea6f5a!important;
	vertical-align: middle
}

.sign .slide-error span {
	font-size: 15px;
	vertical-align: middle
}

.sign .slide-error div {
	margin-top: 10px;
	font-size: 13px
}

.sign .slide-error a {
	color: #3194d0
}

.sign .js-sign-up-forbidden {
	color: #999;
	padding: 80px 0 100px
}

.sign .js-sign-up-container .slide-error {
	border-bottom: none;
	border-radius: 0
}

.sign .logo {
	position: absolute;
	top: 56px;
	margin-left: 50px
}

.sign .logo img {
	width: 100px
}

.sign .main {
	width: 400px;
	margin: 60px auto 0;
	padding: 50px 50px 30px;
	background-color: #fff;
	border-radius: 4px;
	box-shadow: 0 0 8px rgba(0,0,0,.1);
	vertical-align: middle;
	display: inline-block
}

.sign .reset-title,.sign .title {
	margin: 0 auto 50px;
	padding: 10px;
	font-weight: 400;
	color: #969696
}

.sign .reset-title a,.sign .title a {
	padding: 10px;
	color: #969696
}

.sign .reset-title a:hover,.sign .title a:hover {
	border-bottom: 2px solid #ea6f5a
}

.sign .reset-title .active,.sign .title .active {
	font-weight: 700;
	color: #ea6f5a;
	border-bottom: 2px solid #ea6f5a
}

.sign .reset-title b,.sign .title b {
	padding: 10px
}

.sign .reset-title {
	color: #333;
	font-weight: 700
}

.sign form {
	margin-bottom: 30px
}

.sign form .input-prepend {
	position: relative;
	width: 100%
}

.sign form .input-prepend input {
	width: 100%;
	height: 50px;
	margin-bottom: 0;
	padding: 4px 12px 4px 35px;
	border: 1px solid #c8c8c8;
	border-radius: 0 0 4px 4px;
	background-color: hsla(0,0%,71%,.1);
	vertical-align: middle
}

.sign form .input-prepend i {
	position: absolute;
	top: 14px;
	left: 10px;
	font-size: 18px;
	color: #969696
}

.sign form .input-prepend span {
	color: #333
}

.sign form .input-prepend .ic-show {
	top: 18px;
	left: auto;
	right: 8px;
	font-size: 12px
}

.sign form .geetest-placeholder {
	height: 44px;
	border-radius: 4px;
	background-color: hsla(0,0%,71%,.1);
	text-align: center;
	line-height: 44px;
	font-size: 14px;
	color: #999
}

.sign form .restyle {
	margin-bottom: 0
}

.sign form .restyle input {
	border-bottom: none;
	border-radius: 4px 4px 0 0
}

.sign form .no-radius input {
	border-radius: 0
}

.sign form .slide-security-placeholder {
	height: 32px;
	background-color: hsla(0,0%,71%,.1);
	border-radius: 4px
}

.sign form .slide-security-placeholder p {
	padding-top: 7px;
	color: #999;
	margin-right: -7px
}

.sign .overseas-btn {
	font-size: 14px;
	color: #999
}

.sign .overseas-btn:hover {
	color: #2f2f2f
}

.sign .remember-btn {
	float: left;
	margin: 15px 0
}

.sign .remember-btn span {
	margin-left: 5px;
	font-size: 15px;
	color: #969696;
	vertical-align: middle
}

.sign .forget-btn {
	float: right;
	position: relative;
	margin: 15px 0;
	font-size: 14px
}

.sign .forget-btn a {
	color: #999
}

.sign .forget-btn a:hover {
	color: #333
}

.sign .forget-btn .dropdown-menu {
	top: 20px;
	left: auto;
	right: 0;
	border-radius: 4px
}

.sign .forget-btn .dropdown-menu a {
	padding: 10px 20px;
	color: #333
}

.sign #sign-in-loading {
	position: relative;
	width: 20px;
	height: 20px;
	vertical-align: middle;
	margin-top: -4px;
	margin-right: 2px;
	display: none
}

.sign #sign-in-loading:after {
	content: "";
	position: absolute;
	left: 0;
	top: 0;
	width: 100%;
	height: 100%;
	background-color: transparent
}

.sign #sign-in-loading:before {
	content: "";
	position: absolute;
	top: 50%;
	left: 50%;
	width: 20px;
	height: 20px;
	margin: -10px 0 0 -10px;
	border-radius: 10px;
	border: 2px solid #fff;
	border-bottom-color: transparent;
	vertical-align: middle;
	-webkit-animation: rolling .8s infinite linear;
	animation: rolling .8s infinite linear;
	z-index: 1
}

.sign .sign-in-button,.sign .sign-up-button {
	margin-top: 20px;
	width: 100%;
	padding: 9px 18px;
	font-size: 18px;
	border: none;
	border-radius: 25px;
	color: #fff;
	background: #42c02e;
	cursor: pointer;
	outline: none;
	display: block;
	clear: both
}

.sign .sign-in-button:hover,.sign .sign-up-button:hover {
	background: #3db922
}

.sign .sign-in-button {
	background: #3194d0
}

.sign .sign-in-button:hover {
	background: #187cb7
}

.sign .btn-in-resend,.sign .btn-up-resend {
	position: absolute;
	top: 7px;
	right: 7px;
	width: 100px;
	height: 36px;
	font-size: 13px;
	color: #fff;
	background-color: #42c02e;
	border-radius: 20px;
	line-height: 36px
}

.sign .btn-in-resend {
	background-color: #3194d0
}

.sign .sign-up-msg {
	margin: 10px 0;
	padding: 0;
	text-align: center;
	font-size: 12px;
	line-height: 20px;
	color: #969696
}

.sign .sign-up-msg a,.sign .sign-up-msg a:hover {
	color: #3194d0
}

.sign .overseas input {
	padding-left: 110px!important
}

.sign .overseas .overseas-number {
	position: absolute;
	top: 0;
	left: 0;
	width: 100px;
	height: 50px;
	font-size: 18px;
	color: #969696;
	border-right: 1px solid #c8c8c8
}

.sign .overseas .overseas-number span {
	margin-top: 17px;
	padding-left: 35px;
	text-align: left;
	font-size: 14px;
	display: block
}

.sign .overseas .dropdown-menu {
	width: 100%;
	max-height: 285px;
	font-size: 14px;
	border-radius: 0 0 4px 4px;
	overflow-y: auto
}

.sign .overseas .dropdown-menu li .nation-code {
	width: 65px;
	display: inline-block
}

.sign .overseas .dropdown-menu li a {
	padding: 6px 20px;
	font-size: 14px;
	line-height: 20px
}

.sign .overseas .dropdown-menu li a::hover {
	color: #fff;
	background-color: #f5f5f5
}

.sign .more-sign {
	margin-top: 50px
}

.sign .more-sign h6 {
	position: relative;
	margin: 0 0 10px;
	font-size: 12px;
	color: #b5b5b5
}

.sign .more-sign h6:before {
	left: 30px
}

.sign .more-sign h6:after,.sign .more-sign h6:before {
	content: "";
	border-top: 1px solid #b5b5b5;
	display: block;
	position: absolute;
	width: 60px;
	top: 5px
}

.sign .more-sign h6:after {
	right: 30px
}

.sign .more-sign ul {
	margin-bottom: 10px;
	list-style: none
}

.sign .more-sign ul li {
	margin: 0 5px;
	display: inline-block
}

.sign .more-sign ul a {
	width: 50px;
	height: 50px;
	line-height: 50px;
	display: block
}

.sign .more-sign ul i {
	font-size: 28px
}

.sign .more-sign .ic-weibo {
	color: #e05244
}

.sign .more-sign .ic-wechat {
	color: #00bb29
}

.sign .more-sign .ic-qq_connect {
	color: #498ad5
}

.sign .more-sign .ic-douban {
	color: #00820f
}

.sign .more-sign .ic-more {
	color: #999
}

.sign .more-sign .weibo-loading {
	pointer-events: none;
	cursor: pointer;
	position: relative
}

.sign .more-sign .weibo-loading:after {
	content: "";
	position: absolute;
	left: 0;
	top: 0;
	width: 100%;
	height: 100%;
	background-color: #fff
}

body.reader-night-mode .sign .more-sign .weibo-loading:after {
	background-color: #3f3f3f
}

.sign .more-sign .weibo-loading:before {
	content: "";
	position: absolute;
	top: 50%;
	left: 50%;
	width: 20px;
	height: 20px;
	margin: -10px 0 0 -10px;
	border-radius: 10px;
	border: 2px solid #e05244;
	border-bottom-color: transparent;
	vertical-align: middle;
	-webkit-animation: rolling .8s infinite linear;
	animation: rolling .8s infinite linear;
	z-index: 1
}

@keyframes rolling {
	0% {
		-webkit-transform: rotate(0deg);
		transform: rotate(0deg)
	}

	to {
		-webkit-transform: rotate(1turn);
		transform: rotate(1turn)
	}
}

@-webkit-keyframes rolling {
	0% {
		-webkit-transform: rotate(0deg)
	}

	to {
		-webkit-transform: rotate(1turn)
	}
}

.sign .reset-password-input {
	border-radius: 4px!important
}

.sign .return {
	margin-left: -8px;
	color: #969696
}

.sign .return:hover {
	color: #333
}

.sign .return i {
	margin-right: 5px
}

.sign .icheckbox_square-green {
	display: inline-block;
	*display: inline;
	vertical-align: middle;
	margin: 0;
	padding: 0;
	width: 18px;
	height: 18px;
	background: url(/static/image/green.png) no-repeat;
	border: none;
	cursor: pointer;
	background-position: 0 0
}

.sign .icheckbox_square-green.hover {
	background-position: -20px 0
}

.sign .icheckbox_square-green.checked {
	background-position: -40px 0
}

.sign .icheckbox_square-green.disabled {
	background-position: -60px 0;
	cursor: default
}

.sign .icheckbox_square-green.checked.disabled {
	background-position: -80px 0
}


.geetest_panel_box>* {
	box-sizing: content-box
}

@media (max-width:768px) {
	body {
		min-width: 0
	}

	.sign {
		height: auto;
		min-height: 0;
		background-color: transparent
	}

	.sign .logo {
		display: none
	}

	.sign .main {
		position: absolute;
		left: 50%;
		margin: 0 0 0 -200px;
		box-shadow: none
	}
}
</style>
```

把素材中的 `iconfont.css` 和 `iconfont.eof` 字体文件放到项目中引入进来。因为在别的页面也会使用到图标，所以我们直接的入口文件 `index.html` 或者 `main.js` 中全局引入。

如果之前没有引入这两个文件的话，可以在 `main.js` 中插入下面的代码：

```javascript
// 全局导入字体图标
import "../static/css/iconfont.css";
import "../static/css/iconfont.eot";
```

### 绑定登陆页面路由地址

在路由配置 `router/index.js` 中导入 Login 组件并注册：

```javascript
import Vue from "vue"
import Router from "vue-router"

// 导入需要注册路由的组件
import Home from "../components/Home"
import Login from "../components/Login"
Vue.use(Router);

// 配置路由列表
export default new Router({
  mode:"history",
  routes:[
    // 路由列表
		...
    {
      path: '/login',
      name: 'Login',
      component: Login,
    },
  ]
})
```

#### 调整首页头部子组件中登陆按钮的链接信息

修改头部组件 `Header.vue` 中的登录链接，也顺便修改头部的 logo 链接为首页链接：

```vue
<router-link class="logo" to="/"><img src="/static/image/nav-logo.png" /></router-link>


<router-link class="btn log-in" id="sign_in" to="/user/login">登录</router-link>
```

修改登陆组件 `Login.vue` 中的登录链接，还要顺便把头部的荏苒图片的链接修改为首页链接：

```vue
<div class="logo"><router-link to="/"><img src="/static/image/nav-logo.png" alt="Logo"></router-link></div>


<router-link class="active" to="/user/login">登录</router-link>
```

## 后端实现登陆认证

Django 默认已经提供了认证系统 Auth 模块。认证系统包含：

- 用户管理
- 权限（RBAC 权限机制 Role-Base Access Control，基于用户角色的访问控制机制）
- 用户组
- 密码哈希系统
- 用户登录或内容显示的表单和视图
- 一个可插拔的后台系统（admin 后台运营站点，实际开发中大部分人使用的是 xadmin 后台运营站点）

Django 默认用户的认证机制依赖 session 机制。我们在项目中将引入 JWT 认证机制，将用户的身份凭据存放在 Token 中，然后对接 Django 的认证系统，帮助我们来实现：

- 用户的数据模型
- 用户密码的加密与验证
- 用户的权限系统

### Django 用户模型类

Django 内置的用户模型文件： `django/contrib/auth/model.py`

Django 认证系统中提供了用户模型类 User 保存用户的数据，默认的 User 包含以下常见的基本字段：

| 字段名           | 字段描述                                                     |
| ---------------- | ------------------------------------------------------------ |
| username         | 必选，150 个字符以内。用户名可能包含字母数字，`_`，`@`，`+` ，`.` 和 `-` 字符。 |
| first_name       | 可选（`blank=True`），少于等于 30 个字符。                   |
| last_name        | 可选（`blank=True`），少于等于 30 个字符。                   |
| email            | 可选（`blank=True`），邮箱地址。                             |
| password         | 必选， 密码的哈希加密串 （Django 不保存原始密码）。原始密码可以无限长而且可以包含任意字符。 |
| groups           | 与 Group 之间的多对多关系。                                  |
| user_permissions | 与 Permission 之间的多对多关系。                             |
| is_staff         | 布尔值，设置用户是否可以访问 Admin 站点。                    |
| is_active        | 布尔值，指示用户的账号是否激活。 它不是用来控制用户是否能够登录，而是描述一种帐号的使用状态。 |
| is_superuser     | 是否是超级用户。超级用户具有所有权限。                       |
| last_login       | 用户最后一次登录的时间。                                     |
| date_joined      | 账户创建的时间。 当账号创建时，默认设置为当前的 date/time。  |

#### 常用方法

- `set_password(raw_password)`

  设置用户的密码为给定的原始字符串，并负责密码的。 不会保存 User 对象。当 raw_password 为 None 时，密码将设置为一个不可用的密码。

- `check_password(raw_password)`

  如果给定的 raw_password 是用户的真实密码，则返回 True，可以在校验用户密码时使用。

#### 管理器方法

管理器方法即可以通过 `User.objects.` 进行调用的方法。

- `create_user(username, email=None, password=None, **extra_fields)`

  创建、保存并返回一个 User 对象。

- `create_superuser(username, email, password, **extra_fields)`

  与 `create_user()` 相同，但是设置 `is_staff` 和 `is_superuser` 为 `True`。

### 创建用户模块的子应用

```shell
cd renranapi/apps
python ../../manage.py startapp users
```

![1582980963537](renran-login.assets/1582980963537.png)

在 `settings.py` 文件中注册子应用。

```python
INSTALLED_APPS = [
		...
  	'users',
]
```

如果像往常一样，我们按照上面的写法添加应用，Django 将无法识别，会报错。这是因为我们的应用没有放在项目的根目录下。我们当然可以把应用的路径写成 `'renranapi.apps.users'` 规避这个问题，但是未免太麻烦了些。如果以后我们有很多应用，就会造成代码大量重复。

为了解决因为我们调整子应用保存目录以后导致 Django 无法识别子应用的BUG，我们可以把apps设置为导包路径

```python
import os

# Build paths inside the project like this: os.path.join(BASE_DIR, ...)
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))

# 把网站子应用所在目录设置为了导报路径
import sys
sys.path.insert(0, os.path.join(BASE_DIR, "apps"))

# ...中间省略一堆字

# Application definition

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'corsheaders',

    'users',
]

# ...后面省略
```

可以通过右键点击 users 应用文件夹，将这个文件夹标记为导报路径，这样 PyCharm 就不会对 users 文件夹飘黄处理了。

![1582982851097](renran-login.assets/1582982851097.png)

### 创建自定义的用户模型类

Django 认证系统中提供的用户模型类及方法很方便，我们可以使用这个模型类，但是字段有些无法满足项目需求，如本项目中需要保存用户的手机号，需要给模型类添加额外的字段。

Django 提供了 `django.contrib.auth.models.AbstractUser` 用户抽象模型类允许我们继承，扩展字段来使用 Django 认证系统的用户模型类。

我们可以在 apps 中创建 Django 应用 users，并在配置文件中注册 users 应用。

在创建好的应用 `models.py` 中定义用户的用户模型类。

```python
from django.db import models
from django.contrib.auth.models import AbstractUser
# Create your models here.
class User(AbstractUser):
    """用户模型类"""
    mobile = models.CharField(max_length=15, null=True, unique=True, help_text="手机号码",verbose_name="手机号码")
    wechat = models.CharField(max_length=100, null=True, unique=True, help_text="微信账号",verbose_name="微信账号")
    alipay = models.CharField(max_length=100, null=True, unique=True, help_text="支付宝账号",verbose_name="支付宝账号")
    qq_number = models.CharField(max_length=11, null=True, unique=True, help_text="QQ号",verbose_name="QQ号")
    # 保存文件的子目录
    avatar = models.ImageField(upload_to="avatar", null=True, default=None, verbose_name="头像")

    class Meta:
        db_table = "rr_users"
        verbose_name = "用户信息"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.username
```

我们自定义的用户模型类还不能直接被 Django 的认证系统所识别，需要在配置文件中告知 Django 认证系统使用我们自定义的模型类。

在配置文件 `settings/dev.py`，中进行设置：

```python
# 自定义用户模型，让django后面对于用户的操作，读取AUTH_USER_MODEL里的模型类
# 值的格式必须是"子应用目录.模型类名"。中间不要加models，有且只能有一个点
AUTH_USER_MODEL = 'users.User'
```

`AUTH_USER_MODEL` 参数的设置以 `.` 来分隔，表示 `应用名.模型类名`。

如果之前没有设置 apps 为导报路径，到了这里，还是要把它设置为导报路径，否则 users 是找不到的。

**注意：Django 强烈建议我们对于 AUTH_USER_MODEL 参数的设置一定要在第一次数据库迁移之前就设置好，否则后续使用可能出现未知错误。**

如果之前已经进行了数据库迁移，可以参考 [解决 Django 自定义用户模型之前就已经数据迁移的 BUG](../django/django-migration-bug) 解决出现的问题。

执行数据库迁移：

```shell
python manage.py makemigrations
python manage.py migrate
```

### Django REST framework JWT

> Json web token（JWT），是为了在网络应用环境间传递声明而执行的一种基于 JSON 的开放标准（RFC 7519)。该 token 被设计为紧凑且安全的，特别适用于分布式站点的单点登录（SSO）场景。JWT 的声明一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源，也可以增加一些额外的其它业务逻辑所必须的声明信息，该 token 也可直接被用于认证，也可被加密。

在用户注册或登录后，我们想记录用户的登录状态，或者为用户创建身份认证的凭证。我们不再使用 session 认证机制，而使用 Json Web Token 认证机制。

### JWT 的构成

JWT 就一段字符串，由三段信息构成的，将这三段信息文本用`.`链接一起就构成了 JWT 字符串。就像这样：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

第一部分我们称它为头部（header），第二部分我们称其为载荷（payload，类似于飞机上承载的物品），第三部分是签证（signature）。

### header

JWT 的头部承载两部分信息：

- 声明类型，这里是 JWT
- 声明加密的算法 通常直接使用 HMAC SHA256

完整的头部就像下面这样的 JSON：

```python
{
  'typ': 'JWT',
  'alg': 'HS256'
}
```

然后将头部进行 base64 加密（该加密是可以对称解密的），构成了第一部分。

```python
# python代码：
headers = '{"typ": "JWT", "alg": "HS256"}'
import base64
header_str = base64.b64encode(headers.encode()).decode()
print(header_str)
# 打印效果：
# eyJ0eXAiOiAiSldUIiwgImFsZyI6ICJIUzI1NiJ9
```

### payload

载荷就是存放有效信息的地方。这个名字像是特指飞机上承载的货品，这些有效信息包含三个部分

- 标准声明
- 公共声明
- 私有声明

标准中注册的声明 (建议但不强制使用) ：

- iss：jwt 签发者
- sub：jwt 所面向的用户
- aud：接收 jwt 的一方
- exp：jwt 的过期时间，这个过期时间必须要大于签发时间
- nbf：定义在什么时间之前，该jwt都是不可用的
- iat：jwt 的签发时间
- jti：jwt 的唯一身份标识，主要用来作为一次性 token，从而回避重放攻击

公共的声明 ： 公共的声明可以添加任何的信息，一般添加用户的相关信息或其他业务需要的必要信息。但不建议添加敏感信息，因为该部分在客户端可解密。

私有的声明 ： 私有声明是提供者和消费者所共同定义的声明。一般不建议存放敏感信息，因为 base64 是对称解密的，意味着该部分信息可以归类为明文信息。

定义一个 payload:

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true,
  "auth": "2dsg343sdaq223256ddd5454",
}
```

然后将其进行 base64 加密，得到 JWT 的第二部分。

```
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9
```

### signature

JWT 的第三部分是一个签证信息。这个签证信息的主要作用并非防止解密，而是为了防止别人恶意串改。由三部分组成：

- header（base64 后的）
- payload（base64后的）
- secret 密钥

这个部分需要 base64 加密后的 header 和 base64 加密后的 payload 使用 `.` 连接组成的字符串，然后通过 header 中声明的加密方式进行加盐 `secret` 组合加密，然后就构成了 JWT 的第三部分。

```javascript
// javascript如果要模拟生成你的jwttoken，可能可以采用以下代码生成[注意：伪代码]
var encodedString = base64UrlEncode(header) + '.' + base64UrlEncode(payload);

var signature = HMACSHA256(encodedString, 'secret'); // TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

将这三部分用 `.` 连接成一个完整的字符串,构成了最终的 JWT：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

注意：secret 是保存在服务器端的，JWT 的签发生成也是在服务器端的。secret 是用来进行 JWT 的签发和 JWT 的验证。所以，它就是你服务端的私钥，在任何场景都不应该流露出去。一旦客户端得知这个 secret，那就意味着客户端可以自我签发 JWT 了。

关于签发和核验 JWT，我们可以使用 Django REST framework JWT 扩展来完成。

文档：https://jpadilla.github.io/django-rest-framework-jwt/

### 安装配置 JWT

JWT 是 Django REST framework 的一个插件性质的东西，可以通过 pip 直接安装：

```bash
pip install djangorestframework-jwt
```

修改 `settings/dev.py` 配置文件，将 JWT 授权和另外两个授权加入到 REST_FRAMEWORK 配置中：

```python
REST_FRAMEWORK = {
    # 认证方式，新增jwt认证
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.BasicAuthentication',
    ),
}

import datetime
# jwt认证相关配置
JWT_AUTH = {
    # 签发给客户端的token有效期，默认值为datetime.timedelta(seconds=300)
    'JWT_EXPIRATION_DELTA': datetime.timedelta(days=1),
}
```

其中，JWT_EXPIRATION_DELTA 用来指明 token 的有效期，

### 手动生成 JWT

Django REST framework JWT 扩展的说明文档中提供了手动签发 JWT 的方法

```python
from rest_framework_jwt.settings import api_settings

jwt_payload_handler = api_settings.JWT_PAYLOAD_HANDLER
jwt_encode_handler = api_settings.JWT_ENCODE_HANDLER

payload = jwt_payload_handler(user)
token = jwt_encode_handler(payload)
```

在用户注册或登录成功后，在序列化器中返回用户信息同时返回 token 即可。

### 后端实现登陆认证接口

Django REST framework JWT 提供了登录获取 token 的视图，可以直接使用。

在子应用 users 路由 `urls.py` 中，增加 login 和 refresh 的路由。login 用来登录认证，refresh 用来刷新 JWT：

```python
from django.urls import path
from rest_framework_jwt.views import obtain_jwt_token,refresh_jwt_token
urlpatterns = [
    path("login/", obtain_jwt_token ),
    path("refresh/", refresh_jwt_token ),
]
```

别忘了还要在项目的总路由中，引入当前子应用的路由文件：

```python
urlpatterns = [
		...
    path('users/', include("users.urls")),
    # include 的值必须是 模块名.urls 格式,字符串中间只能出现一个圆点
]
```

接下来，我们可以通过 postman 来测试下功能。

首先，创建一个管理员账号。在 renranapi 项目根目录下输入命令，并输入用户名和密码：

```
python manage.py createsuperuser
```

用户创建好之后，将项目运行在 `api.renran.cn:8000`，我们就可以使用 postman 来测试我们的接口了：

![1583148337815](renran-login.assets/1583148337815.png)

用户名和密码是刚刚创建的超级用户的用户名和密码，最后成功获得到 token。

## 前端实现登陆功能

在登陆组件中找到登陆按钮，绑定点击事件

```html
<button class="login_btn" @click="loginHandler">登录</button>
```

在 methods 中加上请求后端的 loginHandler 方法

```vue
<template>
<div class="sign">
    <div class="logo"><router-link to="/"><img src="/static/image/nav-logo.png" alt="Logo"></router-link></div>
    <div class="main">

        <h4 class="title">
            <div class="normal-title">
                <router-link class="active" to="/user/login">登录</router-link>
                <b>·</b>
                <a id="js-sign-up-btn" class="" href="/register">注册</a>
    </div>
    </h4>
        <div class="js-sign-in-container">
            <form id="new_session" action="" method="post">
                <div class="input-prepend restyle js-normal">
                    <input placeholder="手机号或邮箱" type="text" v-model="username" id="session_email_or_mobile_number">
                    <i class="iconfont ic-user"></i>
    </div>
                <!-- 海外登录登录名输入框 -->
                <div class="input-prepend">
                    <input placeholder="密码" type="password" v-model="password" id="session_password">
                    <i class="iconfont ic-password"></i>
    </div>
                <div class="remember-btn">
                    <input type="checkbox" value="true" checked="checked" v-model="remember_me" id="session_remember_me"><span>记住我</span>
    </div>
                <div class="forget-btn">
                    <a class="" data-toggle="dropdown" href="">登录遇到问题?</a>
    </div>
                <button class="sign-in-button" id="sign-in-form-submit-btn" type="button" @click="loginHandler">
                    <span id="sign-in-loading"></span>
                    登录
    </button>
    </form>
            <!-- 更多登录方式 -->
            <div class="more-sign">
                <h6>社交帐号登录</h6>
                <ul>
                    <li id="weibo-link-wrap" class="">
                        <a class="weibo" id="weibo-link">
                            <i class="iconfont ic-weibo"></i>
    </a>
    </li>
                    <li><a id="weixin" class="weixin" target="_blank" href=""><i class="iconfont ic-wechat"></i></a></li>
                    <li><a id="qq" class="qq" target="_blank" href=""><i class="iconfont ic-qq_connect"></i></a></li>
    </ul>
    </div>
    </div>

    </div>
    </div>
</template>

<script>
    export default {
        name: "Login",
        data () {
            return {
                username: '',
                password: '',
                remember_me: true,
            }
        },
        methods: {
            loginHandler () {
                // 用于校验用户名和密码是否为空
                if (this.username && this.password) {
                    // 发送axios请求到后端api
                    this.$axios.post('http://api.renran.cn:8000/users/login/', {
                        username: this.username,
                        password: this.password,
                    }).then(response => {    // 接收返回结果
                        this.$message.success(response.data.token)
                    }).catch(error => {    // 接收报错信息
                        if (error.response.status === 400) {
                            this.$message.error('提交数据有误，请检查您输入的账号密码是否正确！')
                        } else {
                            console.log(error)
                        }
                    })
                } else {
                    this.$message.error("对不起,账号或密码不能为空!");
                    return false;
                }
            }
        }
    }
</script>
```

输入用户名或密码后就可以看到 JWT 信息了。

![1583151816737](renran-login.assets/1583151816737.png)

若密码错误，还是会报错的：

![1583152145186](renran-login.assets/1583152145186.png)

### 前端保存 JWT

我们可以将 JWT 保存在 cookie 中，也可以保存在浏览器的本地存储里。我们保存在浏览器本地存储中。

浏览器的本地存储提供了sessionStorage 和 localStorage 两种：

- sessionStorage 浏览器关闭即失效
- localStorage 长期有效

使用方法：

```js
sessionStorage.变量名 = 变量值   // 保存数据
sessionStorage.变量名  // 读取数据
sessionStorage.clear()  // 清除所有sessionStorage保存的数据

localStorage.变量名 = 变量值   // 保存数据
localStorage.变量名  // 读取数据
localStorage.clear()  // 清除所有localStorage保存的数据
```

登陆组件代码 `Login.vue`：

```vue
<script>
    export default {
        name: "Login",
        data() {
            return {
                username: '',
                password: '',
                remember_me: true,
            }
        },
        methods: {
            loginHandler() {
                // 用于校验用户名和密码是否为空
                if (this.username && this.password) {
                    // 发送axios请求到后端api
                    this.$axios.post('http://api.renran.cn:8000/users/login/', {
                        username: this.username,
                        password: this.password,
                    }).then(response => {    // 接收返回结果
                        // 根据用户选择，决定是否永久存储JWT
                        if (this.remember_me) {
                            // 永久存储（一天）
                            localStorage.user_token = response.data.token;
                            sessionStorage.removeItem('user_token');
                        } else {
                            // 临时存储
                            sessionStorage.user_token = response.data.token;
                            localStorage.removeItem('user_token');
                        }
                        this.$confirm('欢迎回到荏苒~', '登陆成功！', {
                            confirmButtonText: '去个人中心',
                            cancelButtonText: '返回上一页',
                            type: 'warning'
                        }).then(() => {
                            // 跳转到个人中心
                            this.$router.push('/user');
                        }).catch(() => {
                            // 跳转到上一页
                            this.$router.back();
                        });

                    }).catch(error => {    // 接收报错信息
                        if (error.response.status === 400) {
                            this.$message.error('提交数据有误，请检查您输入的账号密码是否正确！')
                        } else {
                            console.log(error)
                        }
                    })
                } else {
                    this.$message.error("对不起,账号或密码不能为空!");
                    return false;
                }
            }
        }
    }
</script>
```

默认的返回值仅有 token，我们还需在返回值中增加 username 和 id，方便在客户端页面中显示当前登陆用户

通过修改该视图的返回值可以完成我们的需求。

因为之前的用户模型并没有提供昵称，所以我们在用户模型增加一个新的字段，nickname 表示用户昵称。

修改 `apps/users/models.py` 文件，加一行代码：

```python
from django.db import models
from django.contrib.auth.models import AbstractUser
# Create your models here.
class User(AbstractUser):
    mobile = models.CharField(max_length=15, null=True, unique=True, help_text="手机号码",verbose_name="手机号码")
    wxchat = models.CharField(max_length=100, null=True, unique=True, help_text="微信账号",verbose_name="微信账号")
    alipay = models.CharField(max_length=100, null=True, unique=True, help_text="支付宝账号",verbose_name="支付宝账号")
    qq_number = models.CharField(max_length=11, null=True, unique=True, help_text="QQ号",verbose_name="QQ号")
    # 保存文件的子目录
    avatar = models.ImageField(upload_to="avatar", null=True, default=None, verbose_name="头像")
    nickname = models.CharField(max_length=100, null=True, default=None, verbose_name="用户昵称")
    class Meta:
        db_table = "rr_users"
        verbose_name = "用户信息"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.username
```

数据迁移，命令：

```bash
python manage.py makemigrations
python manage.py migrate
```

在 `users/utils.py` 中，重写 jwt_response_payload_handler 方法，返回多个需要的数据：

```python
def jwt_response_payload_handler(token, user=None, request=None):
    """jwt自定义响应载荷"""
    return {
        "token":token,
        'id': user.id,
        'username': user.username,
        'avatar': "" if not user.avatar else user.avatar.url,
        'nickname': user.nickname,
    }
```

修改 `settings/dev.py` 配置文件，自定义返回载荷信息，应用重写的方法：

```python
# JWT
JWT_AUTH = {
    'JWT_EXPIRATION_DELTA': datetime.timedelta(days=1),
    'JWT_RESPONSE_PAYLOAD_HANDLER': 'users.utils.jwt_response_payload_handler',
}
```

登陆组件代码 `Login.vue`

```vue
<script>
    export default {
        name: "Login",
        data() {
            return {
                username: '',
                password: '',
                remember_me: true,
            }
        },
        methods: {
            loginHandler() {
                // 用于校验用户名和密码是否为空
                if (this.username && this.password) {
                    // 发送axios请求到后端api
                    this.$axios.post('http://api.renran.cn:8000/users/login/', {
                        username: this.username,
                        password: this.password,
                    }).then(response => {    // 接收返回结果
                        // 根据用户选择，决定是否永久存储JWT
                        if (this.remember_me) {
                            // 永久存储（一天）
                            localStorage.user_token = response.data.token;
                            localStorage.user_name = response.data.username;
                            localStorage.user_id = response.data.id;
                            localStorage.user_nickname = response.data.nickname;
                            localStorage.user_avatar = response.data.avatar;
                            sessionStorage.removeItem("user_token");
                            sessionStorage.removeItem("user_name");
                            sessionStorage.removeItem("user_id");
                            sessionStorage.removeItem("user_nickname");
                            sessionStorage.removeItem("user_avatar");
                        } else {
                            // 临时存储
                            sessionStorage.user_token = response.data.token;
                            sessionStorage.user_name = response.data.username;
                            sessionStorage.user_id = response.data.id;
                            sessionStorage.user_nickname = response.data.nickname;
                            sessionStorage.user_avatar = response.data.avatar;
                            localStorage.removeItem("user_token");
                            localStorage.removeItem("user_name");
                            localStorage.removeItem("user_id");
                            localStorage.removeItem("user_nickname");
                            localStorage.removeItem("user_avatar");
                        }
                        this.$confirm('欢迎回到荏苒~', '登陆成功！', {
                            confirmButtonText: '去个人中心',
                            cancelButtonText: '返回上一页',
                            type: 'warning'
                        }).then(() => {
                            // 跳转到个人中心
                            this.$router.push('/user');
                        }).catch(() => {
                            // 跳转到上一页
                            this.$router.back();
                        });

                    }).catch(error => {    // 接收报错信息
                        if (error.response.status === 400) {
                            this.$message.error('提交数据有误，请检查您输入的账号密码是否正确！')
                        } else {
                            console.log(error)
                        }
                    })
                } else {
                    this.$message.error("对不起,账号或密码不能为空!");
                    return false;
                }
            }
        }
    }
</script>
```

经过上面的代码编写，我们就完成了登录基本功能，接下来就可以提交一个代码版本。

```bash
cd ~/Desktop/renran  # 项目根目录
git add .
git commit -m "登录功能的基本实现"   # 记录版本
git push origin dev:dev   # 这一步，可以commit 多次以后，一并发送。
```

## 多条件登录

JWT 扩展的登录视图，在收到用户名与密码时，也是调用 Django 的认证系统中提供的 `authenticate()` 来检查用户名与密码是否正确。

我们可以通过修改 Django 认证系统的认证后端（主要是 authenticate 方法）来支持登录账号既可以是用户名也可以是手机号。

修改 Django 认证系统的认证后端需要继承 `django.contrib.auth.backends.ModelBackend`，并重写 authenticate 方法。

`authenticate(self, request, username=None, password=None, **kwargs)` 方法的参数说明：

- request 本次认证的请求对象
- username 本次认证提供的用户账号
- password 本次认证提供的密码

我们想要让用户既可以以用户名登录，也可以以手机号登录，那么对于 authenticate 方法而言，username 参数即表示用户名或者手机号。

重写 authenticate 方法的思路：

1. 根据 username 参数查找用户 User 对象，username 参数可能是用户名，也可能是手机号等其他认证信息
2. 若查找到 User 对象，调用 User 对象的 check_password 方法检查密码是否正确

在 `users/utils.py` 中编写：

```python
from django.contrib.auth import get_user_model
from django.db.models import Q
def get_user_by_account(account):
    """
    通过账户信息获取用户模型
    :param account: 可以是username,也可以手机号或者邮箱,获取其他的唯一字段信息
    :return: user模型
    """
    User = get_user_model()
    try:
        return User.objects.get(Q(mobile=account) | Q(username=account) | Q(email=account) | Q(qq_number=account))
    except User.DoesNotExist:
        return None

from django.contrib.auth.backends import ModelBackend
class AccountModelBackend(ModelBackend):
    def authenticate(self, request, username=None, password=None, **kwargs):
        user = get_user_by_account(username)
        if user is not None and user.check_password(password) and self.user_can_authenticate(user):
            return user
```

在配置文件 `settings/dev.py` 中告知 Django 使用我们自定义的认证后端：

```python
AUTHENTICATION_BACKENDS = ['users.utils.AccountModelBackend',]
```

附：通过源代码分析 jwt 中的登录认证方法，是怎么通过用户名和密码来获取用户模型的

![分析jwt中的登录认证方法，是怎么通过用户名和密码来获取用户模型的](renran-login.assets/分析jwt中的登录认证方法，是怎么通过用户名和密码来获取用户模型的.png)

## 在登录认证中接入腾讯防水墙验证码

目前主流的验证码大致可分为三种：图形验证码、短信验证码、滑动验证码。

除了腾讯防水墙，还有一个比较有名的验证服务提供商是极验验证码。但相比于腾讯防水墙，极验验证码操作更为复杂繁琐。

所有的验证方式都不是能保证万无一失的，我们的验证码主要还是防君子不防小人。

官网：[https://007.qq.com](https://007.qq.com/)

使用微信扫码登录腾讯云控制台，然后根据官方文档，把验证码集成到项目中

Python 快速接入：https://007.qq.com/python-access.html?ADTAG=acces.start

1. 接口接入流程：https://cloud.tencent.com/document/product/1110/36839

2. 访问云 API 秘钥：https://console.cloud.tencent.com/cam/capi

   ```python
   APPID: 1259340409
   SecretId: AKIDSggmeI7z2qSUHoaf18zb4JKdZv61PEZf 
   SecretKey: 06xbzB7VabOyY3asztbkdIfqlovtLYXG
   ```

3. 访问验证码控制台：https://console.cloud.tencent.com/captcha

4. 新建验证[ 新用户可以领取一个免费的验证码套餐 ]

   ![1578277301522](renran-login.assets/1578277301522.png)

输入相关信息：

![1578277415727](renran-login.assets/1578277415727.png)

点击查看详情：

![1578277442350](renran-login.assets/1578277442350.png)

获取当前验证码应用的应用 ID 和应用秘钥：

![1578277500168](renran-login.assets/1578277500168.png)

把秘钥和 ID 保存到 `settings/dev.py` 配置文件中：

```python
# 腾讯防水墙配置
TENCENT_CAPTCHA = {
    "GATEWAY": "https://ssl.captcha.qq.com/ticket/verify",
    "APPID": "2072894469",
    "App_Secret_Key": "0vcR-k9wMOk1SArX_gvB7qQ**",
}
```

### 前端获取显示并校验验证码

把防水墙的前端核心 js 文件通过 `index.html` 引入。也可以把 `TCaptcha.js` 下载到项目的 `static/js` 目录中，然后在 `main.js` 中通过 import 导入。

下载地址：https://ssl.captcha.qq.com/TCaptcha.js

在 `src/main.js` 中引入 TCaptcha，代码：

```javascript
// 导入腾讯防水墙验证码的核心js
import "../static/js/TCaptcha";
```

在 `src/settings.js` 中添加配置：

```javascript
export default {
  Host:"http://api.renran.cn:8000",
  TC_captcha:{
    app_id: "2072894469",
  },
}
```

在 `Login.vue` 中显示验证码，将登录按钮的点击事件更换为调用验证码。若验证通过，再进行登录认证。代码：

```vue
<template>
    <div class="sign">
    <div class="logo"><router-link to="/"><img src="/static/image/nav-logo.png" alt="Logo"></router-link></div>
    <div class="main">


<h4 class="title">
  <div class="normal-title">
    <router-link class="active" to="/user/login">登录</router-link>
    <b>·</b>
    <a id="js-sign-up-btn" class="" href="/register">注册</a>
  </div>
</h4>
<div class="js-sign-in-container">
  <form id="new_session" action="" method="post">
      <div class="input-prepend restyle js-normal">
        <input placeholder="手机号或邮箱" type="text" v-model="username" id="session_email_or_mobile_number">
        <i class="iconfont ic-user"></i>
      </div>
    <!-- 海外登录登录名输入框 -->

    <div class="input-prepend">
      <input placeholder="密码" type="password" v-model="password" id="session_password">
      <i class="iconfont ic-password"></i>
    </div>
    <div class="remember-btn">
      <input type="checkbox" value="true" checked="checked" v-model="remember_me" id="session_remember_me"><span>记住我</span>
    </div>
    <div class="forget-btn">
      <a class="" data-toggle="dropdown" href="">登录遇到问题?</a>
    </div>
    <button class="sign-in-button" id="sign-in-form-submit-btn" type="button" @click="showCaptcha">
      <span id="sign-in-loading"></span>
      登录
    </button>
</form>
  <!-- 更多登录方式 -->
  <div class="more-sign">
    <h6>社交帐号登录</h6>
    <ul>
  <li id="weibo-link-wrap" class="">
    <a class="weibo" id="weibo-link">
      <i class="iconfont ic-weibo"></i>
    </a>
  </li>
  <li><a id="weixin" class="weixin" target="_blank" href=""><i class="iconfont ic-wechat"></i></a></li>
  <li><a id="qq" class="qq" target="_blank" href=""><i class="iconfont ic-qq_connect"></i></a></li>
</ul>
  </div>
</div>

    </div>
  </div>
</template>

<script>
    export default {
        name: "Login",
        data(){
            return {
                username:"",
                password:"",
                remember_me: false,
            }
        },
        methods:{
            loginHandler(){
                // 1. 验证数据[账号密码]
                if(this.username.length<1 || this.password.length<1){
                    this.$message.error("对不起,账号或密码不能为空!");
                    return ;
                }

                // 2. 发送ajax请求服务端
                this.$axios.post(`http://api.renran.cn:8000/users/login/`,{
                    username: this.username,
                    password: this.password,
                }).then(response=>{ // javascript最新语法中, 可以使用箭头函数
                    // 3. 接收服务端返回的结果[jwt]
                    // 根据用户是否勾选了remember_me[记住我]来使用本地存储保存用户的jwt
                    if(this.remember_me){
                        // 永久存储[一天]
                        localStorage.user_token = response.data.token;
                        localStorage.user_name = response.data.username;
                        localStorage.user_id = response.data.id;
                        localStorage.user_nickname = response.data.nickname;
                        localStorage.user_avatar = response.data.avatar;
                        sessionStorage.removeItem("user_token");
                        sessionStorage.removeItem("user_name");
                        sessionStorage.removeItem("user_id");
                        sessionStorage.removeItem("user_nickname");
                        sessionStorage.removeItem("user_avatar");
                    }else{
                        // 临时存储
                        sessionStorage.user_token = response.data.token;
                        sessionStorage.user_name = response.data.username;
                        sessionStorage.user_id = response.data.id;
                        sessionStorage.user_nickname = response.data.nickname;
                        sessionStorage.user_avatar = response.data.avatar;
                        localStorage.removeItem("user_token");
                        localStorage.removeItem("user_name");
                        localStorage.removeItem("user_id");
                        localStorage.removeItem("user_nickname");
                        localStorage.removeItem("user_avatar");
                    }

                    // 登录跳转
                    this.$confirm(`${response.data.nickname},欢迎回到荏苒~`, '登录成功', {
                      confirmButtonText: '去个人中心',
                      cancelButtonText: '返回上一页',
                      type: 'warning'
                    }).then(() => {
                      // 跳转到个人中心
                      this.$router.push("/user");
                    }).catch(() => {
                      // 跳转到上一页
                      this.$router.back(); // this.$router.go(-1);
                    });

                }).catch(error=>{
                    if(error.response){
                        if(error.response.status==400){
                            this.$message.error("提交数据有误,请检查您输入的账号密码是否正确!");
                        }
                    }else{
                        console.log(error);
                    }

                });

            },
            showCaptcha(){
                // 显示验证码

                if(this.username.length<1 || this.password.length<1){对不起,账号或密码不能为空!
                    this.$message.error("对不起,账号或密码不能为空!");
                    return ;
                }

                var captcha1 = new TencentCaptcha(this.$settings.TC_captcha.app_id, function(res) {
                    console.log(res);
                });
                captcha1.show();
            }
        }
    }
</script>

```

验证码校验返回参数解释：

| 属性名   | 类型   | 内容                                              |
| -------- | ------ | ------------------------------------------------- |
| ret      | Int    | 验证结果。0：验证成功；2：用户主动关闭验证码。    |
| ticket   | String | 验证成功的票据，当且仅当 ret = 0 时 ticket 有值。 |
| appid    | String | 场景 ID。                                         |
| bizState | Any    | 自定义透传参数。                                  |
| randStr  | String | 本次验证的随机串，请求后台接口时需带上。          |

验证码的校验，需要发送 ajax 来到服务端。

我们可以提供预定一个地址给服务端，例如，我们传递数据到服务端 

```
请求方法：post 
请求地址：/users/captcha/
请求数据：验证结果(ret), 票据(ticket), 随机串(randstr)
响应结果：返回验证状态，客户端通过这个状态直接调用loginHandler登录处理
```

Login.vue，验证码发送验证结果，代码：

```vue
<template>
    <div class="sign">
    <div class="logo"><router-link to="/"><img src="/static/image/nav-logo.png" alt="Logo"></router-link></div>
    <div class="main">


<h4 class="title">
  <div class="normal-title">
    <router-link class="active" to="/user/login">登录</router-link>
    <b>·</b>
    <a id="js-sign-up-btn" class="" href="/register">注册</a>
  </div>
</h4>
<div class="js-sign-in-container">
  <form id="new_session" action="" method="post">
      <div class="input-prepend restyle js-normal">
        <input placeholder="手机号或邮箱" type="text" v-model="username" id="session_email_or_mobile_number">
        <i class="iconfont ic-user"></i>
      </div>
    <!-- 海外登录登录名输入框 -->

    <div class="input-prepend">
      <input placeholder="密码" type="password" v-model="password" id="session_password">
      <i class="iconfont ic-password"></i>
    </div>
    <div class="remember-btn">
      <input type="checkbox" value="true" checked="checked" v-model="remember_me" id="session_remember_me"><span>记住我</span>
    </div>
    <div class="forget-btn">
      <a class="" data-toggle="dropdown" href="">登录遇到问题?</a>
    </div>
    <button class="sign-in-button" id="sign-in-form-submit-btn" type="button" @click="show_captcha">
      <span id="sign-in-loading"></span>
      登录
    </button>
</form>
  <!-- 更多登录方式 -->
  <div class="more-sign">
    <h6>社交帐号登录</h6>
    <ul>
  <li id="weibo-link-wrap" class="">
    <a class="weibo" id="weibo-link">
      <i class="iconfont ic-weibo"></i>
    </a>
  </li>
  <li><a id="weixin" class="weixin" target="_blank" href=""><i class="iconfont ic-wechat"></i></a></li>
  <li><a id="qq" class="qq" target="_blank" href=""><i class="iconfont ic-qq_connect"></i></a></li>
</ul>
  </div>
</div>

    </div>
  </div>
</template>

<script>
    export default {
        name: "Login",
        data(){
            return {
                username:"",
                password:"",
                remember_me: false,
            }
        },
        methods:{
            loginHandler(){
                // 1. 验证数据[账号密码]
                if(this.username.length<1 || this.password.length<1){
                    this.$message.error("对不起,账号或密码不能为空!");
                    return ;
                }

                // 2. 发送ajax请求服务端
                this.$axios.post(`http://api.renran.cn:8000/users/login/`,{
                    username: this.username,
                    password: this.password,
                }).then(response=>{ // javascript最新语法中, 可以使用箭头函数
                    // 3. 接收服务端返回的结果[jwt]
                    // 根据用户是否勾选了remember_me[记住我]来使用本地存储保存用户的jwt
                    if(this.remember_me){
                        // 永久存储[一天]
                        localStorage.user_token = response.data.token;
                        localStorage.user_name = response.data.username;
                        localStorage.user_id = response.data.id;
                        localStorage.user_nickname = response.data.nickname;
                        localStorage.user_avatar = response.data.avatar;
                        sessionStorage.removeItem("user_token");
                        sessionStorage.removeItem("user_name");
                        sessionStorage.removeItem("user_id");
                        sessionStorage.removeItem("user_nickname");
                        sessionStorage.removeItem("user_avatar");
                    }else{
                        // 临时存储
                        sessionStorage.user_token = response.data.token;
                        sessionStorage.user_name = response.data.username;
                        sessionStorage.user_id = response.data.id;
                        sessionStorage.user_nickname = response.data.nickname;
                        sessionStorage.user_avatar = response.data.avatar;
                        localStorage.removeItem("user_token");
                        localStorage.removeItem("user_name");
                        localStorage.removeItem("user_id");
                        localStorage.removeItem("user_nickname");
                        localStorage.removeItem("user_avatar");
                    }

                    // 登录跳转
                    this.$confirm(`${response.data.nickname},欢迎回到荏苒~`, '登录成功', {
                      confirmButtonText: '去个人中心',
                      cancelButtonText: '返回上一页',
                      type: 'warning'
                    }).then(() => {
                      // 跳转到个人中心
                      this.$router.push("/user");
                    }).catch(() => {
                      // 跳转到上一页
                      this.$router.back(); // this.$router.go(-1);
                    });

                }).catch(error=>{
                    if(error.response){
                        if(error.response.status==400){
                            this.$message.error("提交数据有误,请检查您输入的账号密码是否正确!");
                        }
                    }else{
                        console.log(error);
                    }

                });

            },
            show_captcha(){
                // 显示验证码

                if(this.username.length<1 || this.password.length<1){
                    this.$message.error("对不起,账号或密码不能为空!");
                    return ;
                }

                var captcha1 = new TencentCaptcha(this.$settings.TC_captcha.app_id, res=>{
                    /*
                    * ret	      Int	      验证结果，0：验证成功。2：用户主动关闭验证码。
                      ticket	  String	  验证成功的票据，当且仅当 ret = 0 时 ticket 有值。
                      appid	    String	  场景 ID。
                      bizState	Any	      自定义透传参数。
                      randstr	  String	 本次验证的随机串，请求后台接口时需带上。
                    *
                    * */
                    if(res.ret === 0){
                      this.$axios.post(`${this.$settings.Host}/users/captcha/`,{
                          ret: res.ret,
                          ticket: res.ticket,
                          randstr: res.randstr,
                      }).then(response=>{
                          if(response.data.message && response.data.randstr === res.randstr){
                              // 验证成功
                              this.loginHandler();
                          }else{
                              this.$meesage.error("验证码验证失败!请重新操作验证码");
                              captcha1.destroy();
                          }
                      }).catch(error=>{
                          console.log("发生错误!", error);
                      })
                    }

                });
                captcha1.show();
            }
        }
    }
</script>

```

api 服务端接入验证码的文档说明: https://007.qq.com/python-access.html?ADTAG=acces.start

服务端接受验证结果并返回，`users/views.py`，代码：

```python
from rest_framework.views import APIView
import json
from urllib.parse import urlencode
from urllib.request import urlopen
from django.conf import settings
from rest_framework.response import Response

import logging
loger = logging.getLogger("django")

# Create your views here.
class CaptchaAPIView(APIView):
    def post(self,request):
        AppSecretKey = settings.TENCENT_CAPTCHA.get("App_Secret_Key")
        appid = settings.TENCENT_CAPTCHA.get("APPID")
        Ticket = request.data.get("ticket")
        Randstr = request.data.get("randstr")
        UserIP = request._request.META.get("REMOTE_ADDR")
        params = {
            "aid": appid,
            "AppSecretKey": AppSecretKey,
            "Ticket": Ticket,
            "Randstr": Randstr,
            "UserIP": UserIP
        }
        params = urlencode(params)

        ret = self.txrequest(AppSecretKey, params)
        return Response({"message":ret, "randstr":Randstr})

    def txrequest(self,appkey, params, m="GET"):
        url = "https://ssl.captcha.qq.com/ticket/verify"
        if m == "GET":
            f = urlopen("%s?%s" % (url, params))
        else:
            f = urlopen(url, params)

        content = f.read()
        res = json.loads(content)
        if not res:
            return False
        else:
            error_code = res["response"]
            if error_code == "1":
                return True
            else:
                # 记录日志
                loger.error("验证接口异常!%s:%s" % (res["response"], res["err_msg"]))
                return False
```

路由，代码：

```python
from django.urls import path
from rest_framework_jwt.views import obtain_jwt_token,refresh_jwt_token
from . import views
urlpatterns = [
    path("login/", obtain_jwt_token ),
    path("refresh/", refresh_jwt_token ),
    path("captcha/", views.CaptchaAPIView.as_view() ),
]
```

### 完善逻辑的验证码登录认证

我认为上面的逻辑是有问题的：后端进行验证码校验后，只是返回了校验结果。而前端已经知道验证码的通过状态了。如果有人使用爬虫等工具，得知了我们的登录接口，完全可以无视验证码登录。

所以，我针对这个问题，进行了修正，要客户端同时提交验证码信息和用户名密码才能登陆。只有验证码有效，才会校验用户名和密码，否则清空用户名密码，不做校验。

视图函数 `views.py`：

```python
import json
from urllib.parse import urlencode
from urllib.request import urlopen
from django.conf import settings
from rest_framework.generics import GenericAPIView
from rest_framework_jwt.views import ObtainJSONWebToken
from rest_framework_jwt.serializers import JSONWebTokenSerializer

import logging
loger = logging.getLogger("django")


# Create your views here.
class CaptchaAPIView(GenericAPIView):
    """
    用来验证验证码是否有效
    """
    def check_captcha(self):
        AppSecretKey = settings.TENCENT_CAPTCHA.get("App_Secret_Key")
        appid = settings.TENCENT_CAPTCHA.get("APPID")
        Ticket = self.request.data.get("ticket")
        Randstr = self.request.data.get("randstr")
        UserIP = self.request._request.META.get("REMOTE_ADDR")
        params = {
            "aid": appid,
            "AppSecretKey": AppSecretKey,
            "Ticket": Ticket,
            "Randstr": Randstr,
            "UserIP": UserIP
        }
        params = urlencode(params)

        ret = self.txrequest(AppSecretKey, params)
        return ret

    def txrequest(self,appkey, params, m="GET"):
        url = "https://ssl.captcha.qq.com/ticket/verify"
        if m == "GET":
            f = urlopen("%s?%s" % (url, params))
        else:
            f = urlopen(url, params)

        content = f.read()
        res = json.loads(content)
        if not res:
            return False
        else:
            error_code = res["response"]
            if error_code == "1":
                return True
            else:
                # 记录日志
                loger.error("验证接口异常!%s:%s" % (res["response"], res["err_msg"]))
                return False


class LoginCaptchaView(CaptchaAPIView, ObtainJSONWebToken):
    def get_serializer_class(self):
        ret = super().check_captcha()
        # 若验证码无效，清空用户名和密码信息，不允许登录
        if not ret:
            self.request.data['username'] = None
            self.request.data['password'] = None
        return JSONWebTokenSerializer
```

`urls.py`：

```python
from django.urls import path
from rest_framework_jwt.views import obtain_jwt_token,refresh_jwt_token
from . import views

urlpatterns = [
    path("login/", views.LoginCaptchaView.as_view() ),
    path("refresh/", refresh_jwt_token ),
]
```

如果接入了腾讯防水墙验证码，在 urlopen 里面会出现网络报错，可以尝试在页面顶部加上 ssl 证书的配置。关闭 ssl 证书的验证

```python
import ssl
ssl._create_default_https_context = ssl._create_unverified_context
```

