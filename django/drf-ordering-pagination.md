## 排序 Ordering 和分页 Pagination

### 排序

对于列表数据，REST framework 提供了 OrderingFilter 过滤器来帮助我们快速指明数据按照指定字段进行排序。

使用方法：

在类视图中设置 filter_backends，使用 `rest_framework.filters.OrderingFilter` 过滤器，REST framework 会在请求的查询字符串参数中检查是否包含了ordering 参数，如果包含了ordering 参数，则按照 ordering 参数指明的排序字段对数据集进行排序。

前端可以传递的 ordering 参数的可选字段值需要在 ordering_fields 中指明。

示例：

```python
from rest_framework.viewsets import ModelViewSet
from shop.models import Shop
from .serializers import ShopModelSerializer
from rest_framework.filters import OrderingFilter


class GoodsModelViewSet(ModelViewSet):
    queryset = Shop.objects.all()
    serializer_class = ShopModelSerializer
    filter_backends = [OrderingFilter]
    ordering_fields = ('id', 'age')
    
# 127.0.0.1:8000/books/?ordering=-age
# -id 表示针对id字段进行倒序排序
# id  表示针对id字段进行升序排序
```

如果需要在过滤以后再次进行排序，则需要两者结合！

排序也是可以在全局进行配置的，只需在 REST_FRAMEWORK 的配置中加入下面的设置：

```python
'DEFAULT_FILTER_BACKENDS': ('rest_framework.filters.OrderingFilter',),
```

全局配置下的过滤组件不能和排序组件一起使用，只支持局部配置的过滤组件和排序组件一起使用。

```python
from rest_framework.generics import ListAPIView
from students.models import Student
from .serializers import StudentModelSerializer
from django_filters.rest_framework import DjangoFilterBackend
class Student3ListView(ListAPIView):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer
    filter_fields = ('age', 'sex')
    # 因为局部配置会覆盖全局配置,所以需要重新把过滤组件核心类再次声明,
    # 否则过滤功能会失效
    filter_backends = [OrderingFilter,DjangoFilterBackend]
    ordering_fields = ('id', 'age')
```

### 分页 Pagination

REST framework 提供了分页的支持。

我们可以在配置文件中设置全局的分页方式，如：

```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS':  'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 100  # 每页数目
}
```

如果在配置 `settings.py` 文件中， 设置了全局分页，那么在 drf 中凡是调用了 ListModelMixin 的 `list()`，都会自动分页。如果项目中出现大量需要分页的数据，只有少数部分的分页，则可以在少部分的视图类中关闭分页功能。

```python
class 视图类(ListAPIView):
	pagination_class = None
```

也可通过自定义 Pagination 类，来为视图添加不同分页行为。分页器类通常可以保存为一个文件，如pagination.py：

```python
from rest_framework.pagination import PageNumberPagination
class StudentPageNumberPagination(PageNumberPagination):
    page_size = 2 # 默认de 每一页的数据量
    max_page_size = 2 # 允许客户端设置的每一页数据量的上限
    page_query_param = "pn" # 地址栏上面的页码参数
    page_size_query_param = "size" # 页码大小的参数[客户端可以通过size参数来指定每一页返回的数量]
```

在视图中通过 `pagination_clas` 属性来指明。

```python
from .pagination import GoodsPageNumberPagination

class GoodsModelViewSet(ModelViewSet):
    queryset = Shop.objects.all()
    serializer_class = ShopModelSerializer
    pagination_class = GoodsPageNumberPagination    # 这里的只不再是列表
```

### 可选分页器

#### PageNumberPagination

前端访问网址形式：

```http
GET http://127.0.0.1:8000/students/?page=4
```

可以在子类中定义的属性：

- page_size 每页数目
- page_query_param 前端发送的页数关键字名，默认为 "page"
- page_size_query_param 前端发送的每页数目关键字名，默认为 None
- max_page_size 前端最多能设置的每页数量

```python
# 声明分页的配置类
from rest_framework.pagination import PageNumberPagination
class StandardPageNumberPagination(PageNumberPagination):
    # 默认每一页显示的数据量
    page_size = 2
    # 允许客户端通过get参数来控制每一页的数据量
    page_size_query_param = "size"
    max_page_size = 10
    # 自定义页码的参数名
    page_query_param = "p"

class StudentAPIView(ListAPIView):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer
    pagination_class = StandardPageNumberPagination

# 127.0.0.1/four/students/?p=1&size=5
```

#### LimitOffsetPagination

前端访问网址形式：

```http
GET http://127.0.0.1/four/students/?limit=100&offset=100
```

可以在子类中定义的属性：

- default_limit 默认限制，默认值与 PAGE_SIZE 设置一致
- limit_query_param limit 参数名，默认 'limit'
- offset_query_param offset 参数名，默认 'offset'
- max_limit 最大 limit 限制，默认 None

```python
from rest_framework.pagination import LimitOffsetPagination
class StandardLimitOffsetPagination(LimitOffsetPagination):
    # 默认每一页查询的数据量,类似上面的page_size
    default_limit = 2
    limit_query_param = "size"
    offset_query_param = "start"

class StudentAPIView(ListAPIView):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer
    # 调用页码分页类
    # pagination_class = StandardPageNumberPagination
    # 调用查询偏移分页类
    pagination_class = StandardLimitOffsetPagination
```

