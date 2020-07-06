## 视图集 ViewSet

我们使用普通类视图，是无法在一个视图中出现两个以上同名方法的，那样会发生方法的覆盖。但是有时候，我们需要这样做。比如，发送 get 请求是要获取数据。我们获取单个数据和多个数据可以通过传参和不传参的方式进行。但是这就导致我们需要两个get 方法。正是这个原因，我们在 APIView、GenericAPIView、视图扩展类和 GenericAPI 视图子类的例子中，全部都用了两个视图类。有没有办法能让我们将这两个类合二为一呢？因为开发过程中，我们希望完成同一组任务的代码都封装在同一个类中。

DRF 的作者考虑到，路由的不同所以导致了 2 个类的出现。为什么不同我们就需要定义 2 个类呢？因为 Django 设计视图类时，是基于 http 的请求不同而要求我们必须使用 http 请求作为类方法名来调用（as_view）。这样的设计导致了一个类如果出现 2 个以上 get 方法时，就会出现覆盖的情况。所以 DRF 的作者重写了 as_view 方法，允许 as_view 通过传递参数来调用不同的视图方法：

```python
path(url地址, views.视图类.as_view({"get":"list","post":"create"})),
re_path(url地址/pk, views.视图类.as_view({"get":"retrieve"})),
```

注意，上面这个 as_view 是 DRF 作者声明的。我们需要调用到它。使用 APIView 是不可以的，需要另一个类
 ViewSet。这个类，我们称之为视图集。

接下来，我们就基于 ViewSet 来学习怎么在一个类里面编写 5 个基本 api 接口

使用视图集 ViewSet，可以将一系列逻辑相关的动作放到一个类中：

- `list()` 提供一组数据
- `retrieve()` 提供单个数据
- `create()` 创建数据
- `update()` 保存数据
- `destory()` 删除数据

ViewSet 视图集类不再实现 `get()`、`post()` 等方法，而是实现动作 action 如 `list()` 、`create()` 等。

视图集只在使用 `as_view()` 方法的时候，才会将 action 动作与具体请求方式对应上。如：

```python
class BookInfoViewSet(viewsets.ViewSet):

    def list(self, request):
        books = BookInfo.objects.all()
        serializer = BookInfoSerializer(books, many=True)
        return Response(serializer.data)

    def retrieve(self, request, pk=None):
        try:
            books = BookInfo.objects.get(id=pk)
        except BookInfo.DoesNotExist:
            return Response(status=status.HTTP_404_NOT_FOUND)
        serializer = BookInfoSerializer(books)
        return Response(serializer.data)
```

在设置路由时，我们可以如下操作：

```python
urlpatterns = [
    url(r'^books/$', BookInfoViewSet.as_view({'get':'list'}),
    url(r'^books/(?P<pk>\d+)/$', BookInfoViewSet.as_view({'get': 'retrieve'})
]
```

### 常用视图集父类

#### ViewSet

继承自 `APIView` 与 `ViewSetMixin`，作用也与 APIView 基本类似，提供了身份认证、权限校验、流量管理等。

ViewSet 主要通过继承 ViewSetMixin 来实现在调用 `as_view()` 时传入字典（如`{'get':'list'}`）的映射处理工作。

在 ViewSet 中，没有提供任何动作 action 方法，需要我们自己实现 action 方法。

使用 ViewSet 完成 5 个基本 API 接口代码为：

```python
from rest_framework.viewsets import ViewSet
class StudentViewSet(ViewSet):
    def get_all(self,request):
        # 用于获取客户端访问路由时, 指向视图方法名
        print(self.action) # get_all
        # # 用于获取客户端访问路由时, 使用的http请求动作
        print(self.request.method) # GET

        student_list = Student.objects.all()
        serializer = StudentModelSerializer(instance=student_list, many=True)
        return Response(serializer.data)

    def add_one(self,request):
        serializer = StudentModelSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data, status=status.HTTP_201_CREATED)

    def get_one(self,request,pk):
        # 1. 根据pk获取模型对象
        student = Student.objects.get(pk=pk)
        # 2. 序列化器转换数据
        serializer = StudentModelSerializer(instance=student)
        # 3. 响应数据
        return Response(serializer.data)

    def put(self,request,pk):
        # 1. 通过pk查询学生信息
        student = Student.objects.get(pk=pk)

        # 3. 调用序列化器对客户端发送过来的数据进行验证
        serializer = StudentModelSerializer(instance=student, data=request.data)
        serializer.is_valid(raise_exception=True)
        # 4. 保存数据
        serializer.save()

        # 5. 返回结果
        return Response(serializer.data, status=status.HTTP_201_CREATED)

    def delete(self, request, pk):
        # 1. 通过pk查询学生信息
        Student.objects.get(pk=pk).delete()
        return Response({"message":"ok"}, status=status.HTTP_204_NO_CONTENT)
```

路由配置为：

```python
urlpatterns = [
    # 基本视图集
    path("set/students/", views.StudentViewSet.as_view({
        "get":"get_all",
        "post":"add_one"
    })),
    re_path("^set/students/(?P<pk>\d+)/$", views.StudentViewSet.as_view({
        "get": "get_one",
        "put": "put",
        "delete":"delete",
    })),
]
```

嗯~ o(*￣▽￣*)o，辛辛苦苦三十年，一夜回到解放前。白忙活了，兜了个大圈子，上面的代码又回到了 APIView 世代。这可咋整！

#### GenericViewSet

不必着急，一步一步来。我们顺着之前的思路，继续让代码通用化，然后变得更加简洁。

通过使用 GenericViewSet，我们的代码通用性更高了：

```python
from rest_framework.viewsets import GenericViewSet
class StudentGenericViewSet(GenericViewSet):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer
    def get_all(self,request):
        queryset = self.get_queryset()
        serializer = self.get_serializer(instance=queryset, many=True)
        return Response(serializer.data)

    def add_one(self,request):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data, status=status.HTTP_201_CREATED)

    def get_one(self,request,pk):
        instance = self.get_object()
        serializer = self.get_serializer(instance=instance)
        return Response(serializer.data)

    def put(self,request,pk):
        instance = self.get_object()
        serializer = self.get_serializer(instance=instance, data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data, status=status.HTTP_201_CREATED)

    def delete(self, request, pk):
        self.get_object().delete()
        return Response("", status=status.HTTP_204_NO_CONTENT)
```

路由配置：

```python
urlpatterns = [
    # 通用视图集
    path("genset/students/", views.StudentGenericViewSet.as_view({
        "get": "get_all",
        "post": "add_one"
    })),
    re_path("^genset/students/(?P<pk>\d+)/$", views.StudentGenericViewSet.as_view({
        "get": "get_one",
        "put": "put",
        "delete": "delete",
    })),
]
```

#### GenericViewSet 搭配扩展视图类 Mixin 使用

单独使用通用视图集并没有很好地简化代码。不过如果我们把 5 个接口的方法名分别改成 list、retrieve、create、update、destory ，事情将有很大改观——这些方法与前面讲过的 Mixin 扩展类提供的方法同名。所以我们可以通过继承 Mixin 扩展类来复用这些方法而无需自己编写。但是 Mixin 扩展类依赖 GenericAPIView，所以还需要继承 GenericAPIView。

GenericViewSet 就帮助我们完成了这样的继承工作，继承自 GenericAPIView 与 ViewSetMixin，在实现了调用 `as_view()` 时传入字典（如`{'get':'list'}`）的映射处理工作的同时，还提供了 GenericAPIView 提供的基础方法，可以直接搭配 Mixin 扩展类使用。

于是，5 个基本 API 接口可以简化成这样：

```python
from rest_framework.viewsets import GenericViewSet
from rest_framework.mixins import CreateModelMixin,UpdateModelMixin,ListModelMixin,DestroyModelMixin,RetrieveModelMixin
class StudentMixinsViewSet(GenericViewSet,CreateModelMixin,UpdateModelMixin,ListModelMixin,DestroyModelMixin,RetrieveModelMixin):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer
```

url 的定义

同一个视图类中，不同的视图方法加载多个不同的序列化器一般都是视图集在使用这个功能。

```python
urlpatterns = [
    # 通用视图集和Mixins视图扩展类
    path("minset/students/", views.StudentMixinsViewSet.as_view({
        "get": "list",
        "post": "create"
    })),
    re_path("^minset/students/(?P<pk>\d+)/$", views.StudentMixinsViewSet.as_view({
        "get": "retrieve",
        "put": "update",
        "delete": "destroy",
    })),
]
```

代码已经大为改观。

#### ModelViewSet

虽然搭配使用 GenericViewSet 和扩展视图类 Mixin 已经让代码变得很短，但是要继承的类太多太繁琐。好在 DRF 为我们封装了 ModelViewSet。ModelViewSet 模型视图集，就是继承了所有的视图扩展类以及通用视图集。

ModelViewSet 继承自 GenericViewSet，同时也继承了 ListModelMixin、RetrieveModelMixin、CreateModelMixin、UpdateModelMixin 和 DestoryModelMixin。

这样，使用 ModelViewSet 写 5 个基本代码的接口就简化为：

```python
from rest_framework.viewsets import ModelViewSet,ReadOnlyModelViewSet
class StudentModelViewSet(ModelViewSet):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer
```

仅需 4 行代码，即可圆满完成我们需要的任务。

路由配置和前面是基本一致的：

```python
urlpatterns = [
    # 模型视图集
    path("model/students/", views.StudentModelViewSet.as_view({
        "get": "list",
        "post": "create"
    })),
    re_path("^model/students/(?P<pk>\d+)/$", views.StudentModelViewSet.as_view({
        "get": "retrieve",
        "put": "update",
        "delete": "destroy",
    })),
]
```

#### ReadOnlyModelViewSet

ReadOnlyModelViewSet 只读模型视图集，用于实现获取一条或多条数据，用法和 ModelViewSet 一致，就不特别举例说明了。

继承自 GenericViewSet，同时也继承了 ListModelMixin 和 RetrieveModelMixin。

### 视图集中定义附加 action 动作

在视图集中，除了上述默认的方法动作外，还可以添加自定义动作。

举例：

```python
from rest_framework.viewsets import ModelViewSet,ReadOnlyModelViewSet
class StudentModelViewSet(ModelViewSet):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer

    def login(self,request):
        """学生登录功能"""
        return Response({"message":"登录成功"})
```

url 的定义

```python
urlpatterns = [
    path("students8/", views.StudentModelViewSet.as_view({"get": "list", "post": "create"})),
    re_path("students8/(?P<pk>\d+)/",
            views.StudentModelViewSet.as_view({"get": "retrieve", "put": "update", "delete": "destroy"})),

    path("stu/login/",views.StudentModelViewSet.as_view({"get":"login"}))
]
```

### action 属性

在视图集中，我们可以通过 action 对象属性来获取当前请求视图集时的 action 动作是哪个。

例如：

```python
from rest_framework.viewsets import ModelViewSet
from students.models import Student
from .serializers import StudentModelSerializer
from rest_framework.response import Response
class StudentModelViewSet(ModelViewSet):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer

    def get_new_5(self,request):
        """获取最近添加的5个学生信息"""
        # 操作数据库
        print(self.action) # 获取本次请求的视图方法名
```

通过路由访问到当前方法中，可以看到本次的 action 就是请求的方法名

### 通过 action 装饰器声明视图集中新定义类方法

如果想要在视图集中自定义类方法，我们还是可以路由类生成路由信息的，不过我们需要使用 `rest_framework.decorators.action` 装饰器对自定义的类方法进行装饰。

以 action 装饰器装饰的方法名会作为 action 动作名，与 list、retrieve 等同。

action 装饰器可以接收两个参数：

- methods：声明该 action 对应的请求方式，列表传递

- detail：声明该 action 的路径是否与单一资源对应，即是否是需要加上 pk 值传参，例如：

  ```
  xxx/<pk>/action方法名/
  ```

  - True 表示路径格式是 `xxx/<pk>/action方法名/`
  - False 表示路径格式是 `xxx/action方法名/`

举例：

```python
from rest_framework.viewsets import ModelViewSet
from .serializers import StudentModelSerializer,StudentListModelSerializer
from rest_framework.generics import GenericAPIView
from rest_framework.decorators import action
class Student2ModelViewSet(ModelViewSet):
    queryset = Student.objects.all()
    def get_serializer_class(self):
        print(self.action)
        """可以通过判断逻辑加载不同的序列化器类"""
        if self.action == "list":
            return StudentListModelSerializer
        else:
            return StudentModelSerializer

    """
    给视图集添加自定义方法，使用action装饰器可以让路由类自动生成对应的路由地址
    @action(methods,detail,url_path,url_name)

    参数1 methods  列表，表示允许客户端通过那些http请求访问当前视图方法，例如：["get"]
    参数2 detail   布尔值，表示是否需要在生成路由的地址，给url地址生成pk值参数
    参数3 url_path 字符串，表示生成路由时，指向当前视图方法的路由地址
    参数4 utl_name 字符串，表示生成路由时，指向当前视图方法的路由别名
    例如，获取学生年龄最小的2个人
    """
    @action(methods=["get","post"], detail=True,url_path="min",url_name="min5")
    def get_min_5(self,request):
        return Response("2个最小的学生信息")
```

要注意的是，使用 action 装饰器必须要同时具备两个条件：[视图集](drf-view-set.md) 和 [Router 路由](drf-router.md)。由路由器自动为此视图集自定义action方法形成的路由会是如下内容：

```python
demo/ ^router/students/$ [name='sera-student-list']
demo/ ^router/students/(?P<pk>[^/.]+)/$ [name='sera-student-detail']
demo/ ^router/students/(?P<pk>[^/.]+)/min/$ [name='sera-student-min5']
```

