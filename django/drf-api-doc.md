## 自动生成接口文档

REST framework 可以自动帮助我们生成接口文档。

接口文档以网页的方式呈现。

自动接口文档能生成的是继承自 `APIView` 及其子类的视图。

### 安装依赖

REST framewrok 生成接口文档需要 `coreapi` 库的支持。

```bash
pip install coreapi -i https://pypi.douban.com/simple
```

### 设置接口文档访问路径

在总路由中添加接口文档路径。

文档路由对应的视图配置为 `rest_framework.documentation.include_docs_urls`，

参数 `title` 为接口文档网站的标题。

```python
from rest_framework.documentation import include_docs_urls

urlpatterns = [
    ...
    path('docs/', include_docs_urls(title='站点页面标题',description="描述信息")),
]
```

在 `settings.py` 中配置接口文档。

```python
REST_FRAMEWORK = {
    # 。。。 其他选项
    # 接口文档
    'DEFAULT_SCHEMA_CLASS': 'rest_framework.schemas.AutoSchema',
}
```

### 文档描述说明的定义位置

单一方法的视图，可直接使用类视图的文档字符串，如

```python
class BookListView(generics.ListAPIView):
    """
    返回所有图书信息.
    """
```

包含多个方法的视图，在类视图的文档字符串中，分开方法定义，如

```python
class BookListCreateView(generics.ListCreateAPIView):
    """
    get:
    返回所有图书信息.

    post:
    新建图书.
    """
```

对于视图集 ViewSet，仍在类视图的文档字符串中封开定义，但是应使用 action 名称区分，如

```python
class BookInfoViewSet(mixins.ListModelMixin, mixins.RetrieveModelMixin, GenericViewSet):
    """
    list:
    返回图书列表数据

    retrieve:
    返回图书详情数据

    latest:
    返回最新的图书数据

    read:
    修改图书的阅读量
    """
```

### 访问接口文档网页

浏览器访问 `127.0.0.1:8000/docs/`，即可看到自动生成的接口文档。

![æ¥å£ææ¡£ç½é¡µ](drf-api-doc.assets/接口文档页面.png)

#### 两点说明：

1. 视图集 ViewSet 中的r etrieve 名称，在接口文档网站中叫做 read

2. 参数的 Description 需要在模型类或序列化器类的字段中以 help_text 选项定义，如：

```python
class Student(models.Model):
    ...
    age = models.IntegerField(default=0, verbose_name='年龄', help_text='年龄')
    ...
```

或

```python
class StudentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Student
        fields = "__all__"
        extra_kwargs = {
            'age': {
                'required': True,
                'help_text': '年龄'
            }
        }
```