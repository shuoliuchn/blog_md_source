## 基本视图类 APIView

Django REST framwork 提供的视图的主要作用：

- 控制序列化器的执行（检验、保存、转换数据）
- 控制数据库查询的执行

REST framework 提供了众多的通用视图基类与扩展类，以简化视图的编写。

APIView 是 REST framework 提供的所有视图的基类，继承自Django的`View`父类。

APIView 与 View 的不同之处在于：

- 传入到视图方法中的是 REST framework 的 Request 对象，而不是 Django 的 HttpRequeset 对象；
- 视图方法可以返回 REST framework 的 Response 对象，视图会为响应数据设置（render）符合前端要求的格式；
- 任何 APIException 异常都会被捕获到，并且处理成合适的响应信息；
- 在进行 `dispatch()` 分发前，会对请求进行身份认证、权限检查、流量控制。

支持定义的类属性

- authentication_classes 列表或元组，身份认证类
- permissoin_classes 列表或元组，权限检查类
- throttle_classes 列表或元祖，流量控制类

APIView 的导入方式：

```
rest_framework.views.APIView
```

在 APIView 中仍以常规的类视图定义方法来实现 `get()` 、`post()` 或者其他请求方式的方法。

举例：

```python
"""APIView是drf里面提供的所有视图类的父类
   APIView提供的功能/属性/方法是最少的,所以使用APIView基本类似我们使用django的View
"""
"""
5个基本API接口
GET   /students/ 获取多个学生信息 
POST  /students/ 添加一个学生信息

GET    /students/<pk>/  获取一个学生信息 
PUT    /students/<pk>/  修改一个学生信息
DELETE /students/<pk>/  删除一个学生信息
"""
from rest_framework.views import APIView
from students.models import Student
from .serializers import StudentModelSerializer
from rest_framework.response import Response
from rest_framework import status

class StudentAPIView(APIView):
    def get(self,request):
        # 1. 获取学生信息的数据模型
        student_list = Student.objects.all()
        # 2. 调用序列化器
        serializer = StudentModelSerializer(instance=student_list, many=True)
        # 3. 返回数据
        return Response(serializer.data)

    def post(self,request):
        # 1. 调用序列化器对用户提交的数据进行验证
        serializer = StudentModelSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        # 2. 调用序列化器进行数据库操作
        serializer.save() # save()方法返回的是添加成功以后的模型对象
        # 3. 返回新增数据
        return Response(serializer.data, status=status.HTTP_201_CREATED)


class Student2APIView(APIView):
    def get(self,request,pk):
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
        # 1. 通过pk查询并删除学生信息
        Student.objects.get(pk=pk).delete()
        return Response({"message":"ok"}, status=status.HTTP_204_NO_CONTENT)
```

路由配置：

```python
urlpatterns = [
    # APIView
    path("students/", views.StudentAPIView.as_view()),
    re_path("^students/(?P<pk>\d+)/$", views.Student2APIView.as_view()),
]
```

