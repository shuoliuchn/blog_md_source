## 通用视图类 GenericAPIView

通用视图类主要作用就是把视图中的独特的代码抽取出来，让视图方法中的代码更加通用，方便把通用代码进行简写。

```
rest_framework.generics.GenericAPIView
```

GenericAPIView 继承自 APIView，主要增加了操作序列化器和数据库查询的方法，作用是为下面 Mixin 扩展类的执行提供方法支持。通常在使用时，可搭配一个或多个 Mixin 扩展类。

提供的关于序列化器使用的属性与方法

- 属性：

  - serializer_class 指明视图使用的序列化器

- 方法：

  - `get_serializer_class(self)`

    GenericAPIView 的 get_serializer_class 主要的作用就是允许我们通过代码调整，进行判断加载不同的序列化器类到视图类中。get_serializer_class 本身就是属于提供给 GenericAPIView 获取序列化器类名的。

    当出现一个视图类中调用多个序列化器时，那么可以通过条件判断在 get_serializer_class 方法中通过返回不同的序列化器类名就可以让视图方法执行不同的序列化器对象。

    返回序列化器类，默认返回 `serializer_class`，可以重写，例如：
  
    ```python
    from rest_framework.viewsets import ModelViewSet
    from .serializers import StudentModelSerializer,StudentListModelSerializer
    from rest_framework.generics import GenericAPIView
  class Student2ModelViewSet(ModelViewSet):
        queryset = Student.objects.all()
      def get_serializer_class(self):
            print(self.action)
          """可以通过判断逻辑加载不同的序列化器类"""
            if self.action == "list":
              return StudentListModelSerializer
            else:
                return StudentModelSerializer
    ```
  
    序列化器代码：
  
    ```python
    from rest_framework import serializers
    from students.models import Student
    class StudentModelSerializer(serializers.ModelSerializer):
        """详情页学生序列化器"""
        class Meta:
            model = Student
            fields = "__all__"
    
    class StudentListModelSerializer(serializers.ModelSerializer):
        """列表页学生序列化器"""
        class Meta:
            model = Student
            fields = ["id","name","class_number"]
    ```
  
    路由代码：
  
    ```python
    path("sera/students/", views.Student2ModelViewSet.as_view({
        "get": "list",
        "post": "create"
    })),
    re_path("^sera/students/(?P<pk>\d+)/$", views.Student2ModelViewSet.as_view({
        "get": "retrieve",
        "put": "update",
        "delete": "destroy",
    })),
    ```
  
  - `get_serializer(self, *args, **kwargs)`
  
    返回序列化器对象，主要用来提供给 Mixin 扩展类使用，如果我们在视图中想要获取序列化器对象，也可以直接调用此方法。
  
    注意，该方法在提供序列化器对象的时候，会向序列化器对象的 context 属性补充三个数据：request、format、view，这三个数据对象可以在定义序列化器时使用。
  
    - request 当前视图的请求对象
    - view 当前请求的类视图对象
    - format 当前请求期望返回的数据格式

提供的关于数据库查询的属性与方法

- 属性：

  - queryset 指明使用的数据查询集

- 方法：

  - `get_queryset(self)`

    返回视图使用的查询集，主要用来提供给Mixin扩展类使用，是列表视图与详情视图获取数据的基础，默认返回`queryset`属性，可以重写，例如：

    ```python
    def get_queryset(self):
        user = self.request.user
        return user.accounts.all()
    ```

  - `get_object(self)`

    返回详情视图所需的模型类数据对象，主要用来提供给 Mixin 扩展类使用。

    在试图中可以调用该方法获取详情信息的模型类对象。

    若详情访问的模型类对象不存在，会返回 404。

    该方法会默认使用 APIView 提供的 check_object_permissions 方法检查当前对象是否有权限被访问。

    举例：

    ```python
    # re_path(r'^books/(?P<pk>\d+)/$', views.BookDetailView.as_view()),
    class BookDetailView(GenericAPIView):
        queryset = BookInfo.objects.all()
        serializer_class = BookInfoSerializer
    
        def get(self, request, pk):
            book = self.get_object() # get_object()方法根据pk参数查找queryset中的数据对象
            serializer = self.get_serializer(book)
            return Response(serializer.data)
    ```

其他可以设置的属性

- pagination_class 指明分页控制类
- filter_backends 指明过滤控制后端

使用 GenericAPIView 提供数据接口，让代码变得更加通用：

```python
from rest_framework.generics import GenericAPIView
from .serializers import StudentModelSerializer

class StudentsGenricAPIView(GenericAPIView):
    # 指定当前视图类中的视图方法使用到的数据结果集[必填]
    queryset = Student.objects.all()
    # 指定当前类中的视图方法的序列化器类名[选填]
    serializer_class = StudentModelSerializer

    def get(self,request):
        instance_list = self.get_queryset()
        serializer = self.get_serializer(instance=instance_list, many=True)
        return Response(serializer.data)

    def post(self,request):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data, status=status.HTTP_201_CREATED)

class Students2GenricAPIView(GenericAPIView):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer
    # 在使用GenericAPIView视图获取或操作单个数据时,视图方法中的代表主键的参数最好是pk
    def get(self,request,pk):
        # instance = self.get_queryset().get(pk=pk)
        instance = self.get_object() # 获取queryset数据结果集中符合pk值的数据
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
    # GenricAPIView
    path("gen/students/", views.StudentsGenricAPIView.as_view()),
    re_path("^gen/students/(?P<pk>\d+)/$", views.Students2GenricAPIView.as_view()),
]
```

