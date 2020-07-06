## 序列化

序列化，也是就服务端响应客户端的内容，是从数据库中读取数据出来。因为数据库中的内容往往都是准确的，所以不需要复杂的校验过程。相对于反序列化而言，序列化的操作是十分简单的。

序列化的步骤为：

1. 先查询出一个学生对象。从视图 `views.py` 中获取模型对象：

   ```python
   from students.models import Student
   
   student = Student.objects.get(id=3)
   ```

2. 构造序列化器对象：

   ```bash
   from .serializers import StudentSerializer
   
   serializer = StudentSerializer(instance=student)
   ```

3. 获取序列化数据，通过 data 属性可以获取序列化后的数据。

   ```python
   serializer.data
   # 其结果为：
   {'id': 4, 'name': '小张', 'age': 18, 'sex': True, 'description': '猴赛雷'}
   ```

   查询单条数据的完整视图代码：

   ```python
   from django.views import View
   from students.models import Student
   from .serializers import StudentSerializer
   from django.http.response import JsonResponse
   class StudentView(View):
       """使用序列化器序列化转换单个模型数据"""
       def get(self,request,pk):
           # 获取数据
           student = Student.objects.get(pk=pk)
           # 数据转换[序列化过程]
           serializer = StudentSerializer(instance=student)
           print(serializer.data)
           # 响应数据
           return JsonResponse(serializer.data)
   ```

4. 如果要被序列化的是包含多条数据的查询集 QuerySet，可以通过添加 `many=True` 参数补充说明，查询多条数据的完成代码：

   ```python
   """使用序列化器序列化转换多个模型数据"""
   def get(self,request):
       # 获取数据
       student_list = Student.objects.all()
   
       # 转换数据[序列化过程]
       # 如果转换多个模型对象数据，则需要加上many=True
       serializer = StudentSerializer(instance=student_list,many=True)
       print( serializer.data ) # 序列化器转换后的数据
   
       # 响应数据给客户端
       # 返回的json数据，如果是列表，则需要声明safe=False
       return JsonResponse(serializer.data,safe=False)
   
   
   # 访问结果：
   [OrderedDict([('id', 1), ('name', 'xiaoming'), ('age', 20), ('sex', True), ('description', '测试')]), OrderedDict([('id', 2), ('name', 'xiaohui'), ('age', 22), ('sex', True), ('description', '后面来的测试')]), OrderedDict([('id', 4), ('name', '小张'), ('age', 18), ('sex', True), ('description', '猴赛雷')])]
   ```

