## GenericAPIView 的视图子类

基于视图扩展类和 GenricAPIView 完成的 5 个基本接口，我们发现这几个接口代码就还有许多可以优化的地方。类似视图类中的各个视图方法，其实也可以通用的。
在 drf 中，就提供了 GenericAPIView 视图子类给我们进行简写。

### CreateAPIView

提供 post 方法

继承自：GenericAPIView、CreateModelMixin

### ListAPIView

提供 get 方法

继承自：GenericAPIView、ListModelMixin

### RetrieveAPIView

提供 get 方法

继承自: GenericAPIView、RetrieveModelMixin

### DestoryAPIView

提供 delete 方法

继承自：GenericAPIView、DestoryModelMixin

### UpdateAPIView

提供 put 和 patch 方法

继承自：GenericAPIView、UpdateModelMixin

### RetrieveUpdateAPIView

提供 get、put、patch方法

继承自： GenericAPIView、RetrieveModelMixin、UpdateModelMixin

### RetrieveUpdateDestoryAPIView

提供 get、put、patch、delete方法

继承自：GenericAPIView、RetrieveModelMixin、UpdateModelMixin、DestoryModelMixin

### 代码示例

使用 GenericAPIView 视图子类写代码就十分简单了：

```python
from rest_framework.generics import \
    ListAPIView,\
    CreateAPIView,\
    RetrieveAPIView,\
    UpdateAPIView,\
    DestroyAPIView

class StudentSonAPIView(ListAPIView,CreateAPIView):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer

class Student2SonAPIView(RetrieveAPIView,UpdateAPIView,DestroyAPIView):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer
```

url 路由配置：

```python
urlpatterns = [
    # 视图子类
    path("son/students/", views.StudentSonAPIView.as_view()),
    re_path("^son/students/(?P<pk>\d+)/$", views.Student2SonAPIView.as_view()),
]
```

