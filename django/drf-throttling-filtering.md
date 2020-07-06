## 限流 Throttling 和过滤 Filtering

### 限流 Throttling

访问限流，也称作访问限速。可以对接口访问的频次进行限制，以减轻服务器压力，或者实现特定的业务。

一般用于付费购买次数、投票等场景使用。

#### 使用

可以在配置文件中，使用`DEFAULT_THROTTLE_CLASSES` 和 `DEFAULT_THROTTLE_RATES`进行全局配置，

```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': (
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle'
    ),
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',
        'user': '1000/day'
    }
}
```

`DEFAULT_THROTTLE_RATES` 可以使用 `second`, `minute`, `hour` 或`day`来指明周期。

也可以在具体视图中通过throttle_classess属性来配置，如

```python
from rest_framework.throttling import UserRateThrottle
from rest_framework.views import APIView

class ExampleView(APIView):
    throttle_classes = (UserRateThrottle,)
    ...
```

#### 可选限流类

1. AnonRateThrottle

   限制所有匿名未认证用户，使用 IP 区分用户，很少用。

   使用 `DEFAULT_THROTTLE_RATES['anon']`  来设置频次

2. UserRateThrottle

   限制认证用户，使用 User id 来区分，很少用。

   使用 `DEFAULT_THROTTLE_RATES['user']` 来设置频次

3. ScopedRateThrottle

   限制用户对于每个视图的访问频次，使用 ip 或 user id。

   例如：

   ```python
   class ContactListView(APIView):
       throttle_scope = 'contacts'
       ...
   
   class ContactDetailView(APIView):
       throttle_scope = 'contacts'
       ...
   
   class UploadView(APIView):
       throttle_scope = 'uploads'
       ...
   REST_FRAMEWORK = {
       'DEFAULT_THROTTLE_CLASSES': (
           'rest_framework.throttling.ScopedRateThrottle',
       ),
       'DEFAULT_THROTTLE_RATES': {
           'contacts': '1000/day',
           'uploads': '20/day'
       }
   }
   ```

#### 实例

全局配置中设置访问频率：

```python
'DEFAULT_THROTTLE_RATES': {
    'anon': '3/minute',  # 匿名用户
    'user': '10/minute',  # 普通用户
    'example': '5/minute', # 视图定制
},
```

视图写法：

```python
from rest_framework.authentication import SessionAuthentication
from rest_framework.permissions import IsAuthenticated
from rest_framework.generics import RetrieveAPIView
from rest_framework.throttling import UserRateThrottle

class StudentAPIView(RetrieveAPIView):
    queryset = Student.objects.all()
    serializer_class = StudentSerializer
    authentication_classes = [SessionAuthentication]
    permission_classes = [IsAuthenticated]
    # throttle_classes = (UserRateThrottle,)
    throttle_scope = 'example'
```

### 过滤 Filtering

过滤组件，局部配置。工作中不能使用全局配置，全局配置无法和排序一起配置使用。

对于列表数据可能需要根据字段进行过滤，我们可以通过添加 django-fitlter 扩展来增强支持。

```bash
pip install django-filter
```

在配置文件中增加过滤后端的设置：

```python
INSTALLED_APPS = [
    ...
    'django_filters',  # 需要注册应用，
]

REST_FRAMEWORK = {
    ...
    'DEFAULT_FILTER_BACKENDS': ('django_filters.rest_framework.DjangoFilterBackend',)
}
```

在视图类中添加类属性 filter_fields，指定可以过滤的字段

```python
class StudentListView(ListAPIView):
    # filter_backends = [DjangoFilterBackend,OrderingFilter]
    queryset = Student.objects.all()
    serializer_class = StudentSerializer
    filter_fields = ('age', 'sex')

# 127.0.0.1:8000/four/students/?sex=1
```

