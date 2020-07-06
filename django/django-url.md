## Django URL 路由

### urls 路由

对于 Django 1.x 版本来说，路由是通过 url 方法来写的，且默认会按照正则进行匹配：

```python
from django.conf.urls import url
from django.contrib import admin
from app01 import views

urlpatterns = [
    # 循环进行匹配,一旦匹配成功,就会执行后面的函数,并且不再往下面进行匹配了
    url(r'^admin/', admin.site.urls),
    url(r'^home/', views.home),

    url(r'^books/(\d+)/', views.books), #url无名分组,也可以叫做无名参数
    url(r'^login/', views.login), #url无名分组,也可以叫做无名参数

    # url(r'^books/(?P<year>\d+)/(?P<month>\d+)/', views.books), #url有名分组,也可以叫做有名参数
    # books/2019/  -- 2019  {'year':2019} year=2019
]
```

#### url 写法

##### 无名分组

url 路由写法：

```python
url(r'^books/(\d+)/(\d+)/', views.books), # url无名分组，也可以叫做无名参数
```

视图函数写法：

```python
def books(request,y,n):pass  # y：拿到的是第一个参数，n：拿到的是第二个参数
```

传参方式是位置传参。

##### 有名分组

url 路由写法：

```python
url(r'^books/(?P<year>\d+)/(?P<month>\d+)/', views.books)
```

视图函数写法：

```python
def books(request,year,month):pass  # year和month拿到的正则有名分组匹配到的那个名字相同的参数
```

传参方式是关键字传参，不考虑参数顺序，但是名称一定要和正则的相同

```python
def books(request,y,n=None):pass	 # n=None就是默认值，当url正则匹配不到数据时，使用默认值，匹配到了的话，使用匹配结果	
```

注意：捕获的参数永远都是字符串

#### Django 2.x 版本 url 写法

在 Django 2.x 版本中，路由分为两种：普通路由和正则路由。普通路由使用 path，正则路由使用 re_path。导入方式为 `django.urls`，而非 `django.conf.url`。其他的用法与 Django 1.x 是相同的，就不再赘述。其大致写法为：

```python
from . import views
from rest_framework.routers import DefaultRouter
from django.urls import path, re_path

urlpatterns = [
    path('allgoods/', views.AllShopView.as_view()),
    path('singlegoods/', views.SingleShopView.as_view()),
    path('goods/', views.GoodsGenericView.as_view()),
    re_path('^goods/(?P<pk>\d+)/', views.Goods2GenericView.as_view()),
    path('gen/goods/', views.GoodsGenericViewSet.as_view({
        'get': 'list',
        'post': 'create',
    })),
    re_path('^gen/goods/(?P<pk>\d+)/', views.GoodsGenericViewSet.as_view({
        'get': 'retrieve',
        'put': 'update',
        'delete': 'destroy'
    }))
]
```

### url 斜杠

在写 url 配置时，前置导航斜杠不需要写：

```python
url(r'^home/', views.home)
```

后面的斜杠是根据 Django 的配置来的。如果在settings配置文件中我们设置了

```python
APPEND_SLASH = False
```

那么浏览器发送来的请求如果没有带着后面的斜杠，也是可以正常请求的。但是如果没有这个配置或者配置成 True 的话，Django 要求浏览器必须带着路径后面的斜杠来进行访问。如果你输入路径的时候没有加 `/`，那么 `Django` 让你的浏览器发一个重定向请求带上 `/`。

### url 别名和反向解析

给路径取别名，当路径需要发生改变时，不必修改视图函数和代码中的引用方式。

urls 文件中的写法：

```python
url(r'^login/v2/', views.login,name='xx'),
```

视图中反向解析：

```python
from django.urls import reverse
# 也可以直接从 django.shortcuts 中导入reverse。从源码来看，这两个reverse是相同的。

def login(request):
    print(reverse('xx'))  #/login/v2/
    if request.method == 'GET':
    return render(request,'login.html')
else:
    uname = request.POST.get('uname')
    pwd = request.POST.get('pwd')
    if uname == 'chao' and pwd == '123':
        return HttpResponse('ok')
	else:
		return redirect(reverse('xx'))  #使用反向解析
```

html 模板渲染时反向解析的语法：

```jinja2
{% url 别名 %}
```

在页面中的写法就是：

```html
<form action="{% url 'xx' %}" method="post">    <!-- 这里是反向解析 -->
    {% csrf_token %}    <!-- csrf_token 用于防御跨站请求伪造-->
    用户名:<input type="text" name="uname">
    密码:<input type="text" name="pwd">
    <input type="submit">
</form>
```

### include 路由分发

实际应用中，一个项目中可能会有很多应用。如果把这些应用的路由全都放到项目的 `urls.py` 文件中，那将是一个灾难——项目的 `urls.py` 文件将会变得异常臃肿杂乱，可读性极低，且不利于管理。

为了解决这个问题，我们可以在每个应用中也都配备自己的 url.py 路由文件。项目的总路由负责把请求分发给这些应用，这就是路由分发的作用。

其用法步骤如下：

1. 项目文件夹下的 `urls.py` 文件中写上以下内容：

   ```python
   from django.conf.urls import url,include
   urlpatterns = [
       url(r'^admin/', admin.site.urls),
       url(r'^app01/', include('app01.urls',namespace='app01')),
       url(r'^app02/', include('app02.urls',namespace='app02')),
       #http://127.0.0.1:8000/index/
   ]
   ```

2. 在各个 app 应用文件夹下面创建 `urls.py` 文件，在 `urls.py` 文件中写自己应用的各个路径，比如 `app01` 和 `app02`：

   ```python
   app01的urls.py配置：
       from django.conf.urls import url, include
       from django.contrib import admin
       from app01 import views
       urlpatterns = [
           url(r'^index/', views.index,name='index'),
       ]
   app02的urls.py配置：
       from django.conf.urls import url, include
       from django.contrib import admin
       from app02 import views
       urlpatterns = [
           url(r'^index/', views.index,name='index'),
       ]
   ```

3. 在各个应用中写各自的视图函数，当需要进行路径分发时，使用这样的格式：

   ```python
   return redirect('app01:index')
   ```

### url 命名空间

基本写法：

```python
url(r'^app01/', include('app01.urls',namespace='app01')),
```

`views.py` 文件中写法：

```python
from django.urls import reverse
def index(request):
    print('app01反向解析:', reverse('app01:index'))
    # app01反向解析: /app02/index/
    return HttpResponse('app01-index')
```

`html` 中写法：

```jinja2
{% url 'app01:login' %}
```

在 Django 2 版本中，光这样写也不成，会报错，还要在应用的 `app01/urls.py` 子路由中加上声明应用名的属性，例如：

```python
from django.urls import path
from . import views

app_name = 'app01'    # 声明应用名

urlpatterns = [
    path('login/', views.Login.as_view(), name='login'),
]
```

### 带参 url 反向解析

url 别名反向解析时如果需要参数，HTML 代码中可以在别名后直接加上参数：

```jinja2
{% url '别名' 3 %}
```

实际上在模板渲染时，路径被渲染为 `/index/3/` 的形式。所以，在路由分发时，我们可以使用正则传参的方式，把参数传递给视图函数：

```python
url(r'^index/(\d+)/', views.index, name='index');
```

如果在 views 视图反向解析时需要传入参数，我们只需增加 arg 参数即可：

```python
reverse('index',args=(3,))    # /index/3/
```

### 携带数据按钮

编辑按钮携带数据，主要是通过 get 请求和路径参数的方式：

```html
<a href="{% url 'book_edit' %}?book_id={{ book.id }}" class="btn btn-warning btn-sm">编辑</a>
<a href="/book/edit/{{ book.id }}/" class="btn btn-warning btn-sm">编辑</a>
<a href="{% url 'book_edit' book.id %}" class="btn btn-warning btn-sm">编辑</a>
```