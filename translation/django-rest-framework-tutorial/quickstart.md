# 快速入门

我们将创建一个简单的 API，允许管理员用户查看和编辑系统中的用户和组。

## 创建项目

创建一个新的名为 `tutorial` 的项目，然后开启一个名为 `quickstart` 的新应用。

```bash
# 创建项目目录
mkdir tutorial
cd tutorial

# 创建一个虚拟环境，把我们的依赖包限制在局部
python3 -m venv env
source env/bin/activate  # 在 Windows 系统中要使用 `env\Scripts\activate`

# 给虚拟环境安装 Django 和 Django REST framework
pip install django
pip install djangorestframework

# 创建一个新的单应用项目
django-admin startproject tutorial .  # 别忘了加最后的这个 '.' 符号
cd tutorial
django-admin startapp quickstart
cd ..
```

项目布局应该是这个样子的：

```bash
$ pwd
<some path>/tutorial
$ find .
.
./manage.py
./tutorial
./tutorial/__init__.py
./tutorial/quickstart
./tutorial/quickstart/__init__.py
./tutorial/quickstart/admin.py
./tutorial/quickstart/apps.py
./tutorial/quickstart/migrations
./tutorial/quickstart/migrations/__init__.py
./tutorial/quickstart/models.py
./tutorial/quickstart/tests.py
./tutorial/quickstart/views.py
./tutorial/settings.py
./tutorial/urls.py
./tutorial/wsgi.py
```

或许直接把应用创建在项目文件夹中看起来很奇葩。使用项目的名称空间能够避免和外部模块的冲突（一个超出快速入门范围的话题）。

现在，初次同步你的数据库：

```bash
python manage.py migrate
```

我们还要创建一个名为 `admin`，密码为 `password123` 的初始用户。在接下来的例子中，我们将被授权为这个用户。

```python
python manage.py createsuperuser --email admin@example.com --username admin
```

一旦你完成配置数据库并创建好初始用户，已经准备好继续前进，那么就打开应用目录，我们要开始敲代码了……

## 序列化器 Serializers

首先我们要定义一些序列化器。让我们创建一个名为 `tutorial/quickstart/serializers.py` 的模块，我们要用它来做数据表示。

```python
from django.contrib.auth.models import User, Group
from rest_framework import serializers


class UserSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = User
        fields = ['url', 'username', 'email', 'groups']


class GroupSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Group
        fields = ['url', 'name']
```

注意，在这里我们使用 `HyperlinkedModelSerializer` 建立超链接关系。你也可以使用主键和各种各样其他的关系。但是超链接很符合 RESTful 设计规范。

## 视图 Views

没错，我们接下来最好写一些视图函数。打开 `tutorial/quickstart/views.py`，然后开始打字吧。

```python
from django.contrib.auth.models import User, Group
from rest_framework import viewsets
from tutorial.quickstart.serializers import UserSerializer, GroupSerializer


class UserViewSet(viewsets.ModelViewSet):
    """
    API endpoint that allows users to be viewed or edited.
    """
    queryset = User.objects.all().order_by('-date_joined')
    serializer_class = UserSerializer


class GroupViewSet(viewsets.ModelViewSet):
    """
    API endpoint that allows groups to be viewed or edited.
    """
    queryset = Group.objects.all()
    serializer_class = GroupSerializer
```

我们把所有常用的都打包起来，封装成所谓 `ViewSets` （视图集）的类，而不是写很多视图函数。

如果需要的话，我们很容易把这些东西拆分成独立的视图。但是使用视图集能够保持代码逻辑简洁明了。

## 路由 URLs

好了，现在让我们连接 API 的 URL 路由。向 `tutorial/urls.py` 冲呀……

```python
from django.urls import include, path
from rest_framework import routers
from tutorial.quickstart import views

router = routers.DefaultRouter()
router.register(r'users', views.UserViewSet)
router.register(r'groups', views.GroupViewSet)

# 使用自动的 URL 路由连接我们的 API。
# 然后，我们把可浏览器访问的 API 分配给登录 URL。
urlpatterns = [
    path('', include(router.urls)),
    path('api-auth/', include('rest_framework.urls', namespace='rest_framework'))
]
```

因我我们用的是视图集（viewsets），而不是视图（views），我们可以自动生成我们 API 的 URL，仅需通过简单地使用路由（router）类注册视图集即可。

再一次，如果我们需要进一步操控 API URL，我们可以简单地降级，使用常规的类视图（class-based views），清楚写明 URL 配置。

最好，我们引入默认的登录登出视图来使用可浏览器访问的 API。这是可选的，但是如果你的 API 需要授权，而你又想要使用可浏览器访问的 API，这还是蛮有用的。

## 分页 Pagination

分页允许你控制每页返回多少个项目。要启用这个功能只需把下面的代码添加到  `tutorial/settings.py`

```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10
}
```

## 设置 Settings

把 `'rest_framework'` 添加到 `INSTALLED_APPS` 里面。设置模块在 `tutorial/settings.py`

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

好了，完事了。

------

## 测试我们的 API

现在我们已经准备好测试刚刚建立起来的 API 了。让我们从命令行引燃服务吧。

```bash
python manage.py runserver
```

我们现在可以进入我们的 API，既可以来自命令行，使用工具诸如 `curl`……

```bash
bash: curl -H 'Accept: application/json; indent=4' -u admin:password123 http://127.0.0.1:8000/users/
{
    "count": 2,
    "next": null,
    "previous": null,
    "results": [
        {
            "email": "admin@example.com",
            "groups": [],
            "url": "http://127.0.0.1:8000/users/1/",
            "username": "admin"
        },
        {
            "email": "tom@example.com",
            "groups": [                ],
            "url": "http://127.0.0.1:8000/users/2/",
            "username": "tom"
        }
    ]
}
```

抑或使用 [httpie](https://github.com/jakubroztocil/httpie#installation)，命令行工具……

```bash
bash: http -a admin:password123 http://127.0.0.1:8000/users/

HTTP/1.1 200 OK
...
{
    "count": 2,
    "next": null,
    "previous": null,
    "results": [
        {
            "email": "admin@example.com",
            "groups": [],
            "url": "http://localhost:8000/users/1/",
            "username": "paul"
        },
        {
            "email": "tom@example.com",
            "groups": [                ],
            "url": "http://127.0.0.1:8000/users/2/",
            "username": "tom"
        }
    ]
}
```

或者直接通过浏览器，通过访问 URL `http://127.0.0.1:8000/users/`……

![Quick start image](quickstart.assets/quickstart.png)

如果你正在鼓捣浏览器，确保登录到右上角的控制台中。

棒极了，挺简单！

如果你想更深入了解 REST framework 是如何相互协调运作的，继续往下读 [教程](serialization.md)，或者开始浏览 [API 指南](https://www.django-rest-framework.org/tutorial/#api-guide)。