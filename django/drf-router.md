## 路由 Router

视图集极大地简化了我们写视图的代码，但是路由中还是有较多的重复代码，可供我们优化。回忆一下我们在视图集中写的 URL 路由代码：

```python
# 同一个视图类中，不同的视图方法加载多个不同的序列化器一般都是视图集在使用这个功能。
urlpatterns = [
    path("sera/students/", views.Student2ModelViewSet.as_view({
        "get": "list",
        "post": "create"
    })),
    re_path("^sera/students/(?P<pk>\d+)/$", views.Student2ModelViewSet.as_view({
        "get": "retrieve",
        "put": "update",
        "delete": "destroy",
    })),
]
```

上面 Student2ModelViewSet，我们没必要在路由中编写。对于已经继承了 Viewset 的视图集，我们工作中一般使用路由类自动生成路由信息即可。

对于视图集 ViewSet，我们除了可以自己手动在路由 `url.py` 中设置当前客户端的 http 请求方式与动作 action 之间的对应关系外，在drf中还可以使用Routers路由类来自动帮我们快速生成视图集的路由列表。

REST framework 提供了两个 router 路由类，这两个路由类几乎没区别。后面我们讲下他们的小区分。

- **SimpleRouter**
- **DefaultRouter**

### 使用方法

路由的使用分三个步骤：

1. 引入路由类并初始化创建router对象

   ```python
   from rest_framework.routers import DefaultRouter, SimpleRouter
   router = SimpleRouter()
   ```

   DefaultRouter 会比 SimpleRouter 多出一个调试界面，列出当前子应用下所有的路由类的视图集接口地址。除此之外，大家用法一致。

2. 注册视图集到路由类中，例如：

   ```python
   router.register("router/students", views.Student2ModelViewSet, basename="sera-student")
   ```

   `register(prefix, viewset, base_name)` 参数解释：

   - prefix 该视图集的访问地址路由前缀

   - viewset 视图集类，不要加 as_view！！！

   - basename 路由别名的前缀，一般把 URL 地址前缀复制过来，然后遇到斜杠 `/` 改成横杠 `-`

     如上述代码会形成的路由如下：

     ```python
     students/ ^router/students/$ [name='students-list']
     students/ ^router/students/(?P<pk>[^/.]+)/$ [name='students-detail']
     ```

3. 把路由类生成的路由信息合并到路由列表 urlpatterns 中。因为 django 只认定 urlpatterns 这个变量名：

   有两种方式：

   ```python
   urlpatterns = [
       ...
   ]
   urlpatterns += router.urls
   ```

   或

   ```python
   urlpatterns = [
       ...
       url(r'^', include(router.urls))
   ]
   ```

使用路由类给视图集生成路由地址，视图集代码无需改动，保持原样即可：

```python
from rest_framework.viewsets import ModelViewSet,ReadOnlyModelViewSet
class StudentModelViewSet(ModelViewSet):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer

    def login(self,request):
        """学生登录功能"""
        print(self.action)
        return Response({"message":"登录成功"})
```

路由代码：

```python
from django.urls import path, re_path
from . import views
urlpatterns = [
    ...
]

"""使用drf提供路由类router给视图集生成路由列表"""
# 实例化路由类
# drf提供一共提供了两个路由类给我们使用，他们用法一致，功能几乎一样
# 在这个例子中，浏览器访问127.0.0.1:8000/students时，
# SimpleRouter会出现报错大黄页，DefaultRouter会出现调试页面
from rest_framework.routers import DefaultRouter
router = DefaultRouter()

# 注册视图集
# router.register("路由前缀",视图集类,别名前缀)
router.register("router/students", views.Student2ModelViewSet,basename="sera-student")

# 把生成的路由列表追加到urlpatterns
urlpatterns += router.urls
```

面的代码就成功生成了路由地址（增/删/改/查一条/查多条的功能），但是不会自动我们在视图集自定义方法的路由。

如果我们需要给自定义方法生成路由，则需要进行 action 动作的声明，比如 [通过 action 装饰器声明视图集中新定义类方法](drf-view-set.md#通过 action 装饰器声明视图集中新定义类方法)。

### 路由 router 形成 URL 的方式

#### SimpleRouter

![SimpleRouter](drf-router.assets/SimpleRouter.png)

#### DefaultRouter

![DefaultRouter](drf-router.assets/DefaultRouter.png)

DefaultRouter 与 SimpleRouter 的区别是，DefaultRouter 会多附带一个默认的 API 根视图，返回一个包含所有列表视图的超链接响应数据。