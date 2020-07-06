## 模型类序列化器 ModelSerializer

### 演示

针对前面所学习的序列化器代码，我们来对比一下。对于接下来的代码我们新建一个子应用来展示：

```bash
python manage.py startapp mod
```

注册子应用：

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'rest_framework', # 把drf框架注册到django项目中

    'students', # 注册子应用
    'sers',     # 演示序列化
    'unsers',     # 演示反序列化
    'mod',     # 模型类序列化器
]
```

注册路由：

```python
# 子应用路由
from django.urls import path
from . import views
urlpatterns = [
]


# 主应用的的总路由
urlpatterns = [
    path('admin/', admin.site.urls),
    path("student/",include("students.urls")),
    path("sers/",include("sers.urls")),
    path("un/",include("unsers.urls")),
    path("mod/",include("mod.urls")),
]

```

同一个模型类，事实上既可以用于在序列化器序列化阶段，也可以用于反序列化阶段。

编写序列化器的时候，有部分字段在业务实现过程中只能用于其中一个阶段。

- 字段 id 属于主键，只能在序列化阶段，提供给用户，用户没有办法上传提交给我们。ID 字段无法用于反序列化阶段。类似于 id 这样的字段，可以使用 read_only，显示 id 只会被序列化阶段被使用。

- 字段 password，属于用户隐私信息，只能在反序列化器接收用户的修改/添加，但是绝对不能在序列化阶段提供给外界查看。类似于password 这样的字段，可以使用 write_only，显示 password 只会被反序列化阶段被使用。

序列化器代码：

```python
from rest_framework import serializers

class BookInfoSerializer(serializers.Serializer):
    """图书信息"""
    id = serializers.IntegerField(read_only=True, label="ID", help_text="ID")
    title = serializers.CharField(max_length=20, label="标题", help_text="标题")
    pub_date = serializers.DateField(required=True, label="发布日期", help_text="发布日期")
    image = serializers.ImageField(required=False, allow_null=True, max_length=3 * 1024 * 1024, label="图书封面", help_text="图书封面")
    price = serializers.DecimalField(max_digits=8, decimal_places=2, required=True, label="价格", help_text="价格")
    read = serializers.IntegerField(min_value=0, default=0, label="阅读量", help_text="阅读量")
    comment = serializers.IntegerField(min_value=0, default=0, label="评论量", help_text="评论量")
```

视图代码：

```python
from django.views import View
from unsers.models import BookInfo
from .serializers import BookInfoSerializer
from django.http.response import JsonResponse
class BookInfoView(View):
    def get(self,request):
        """提供所有图书信息"""
        book_list = BookInfo.objects.all()
        serializer = BookInfoSerializer(instance=book_list, many=True)
        return JsonResponse(serializer.data, safe=False)

    def get2(self,request):
        """添加一本图书"""
        data = {
            "title": "一本很厚的书",
            "pub_date": "2020-02-19",
            "price": 100,
            "read": 300,
            "comment": 10,
        }
        serializer = BookInfoSerializer(data=data)
        serializer.is_valid(raise_exception=True)
        print(serializer.validated_data)

        return JsonResponse({"message":"ok"})
```

路由代码：

```python
from django.urls import path
from . import views
urlpatterns = [
    path("all/", views.BookInfoView.as_view() ),
]
```

访问 `http://127.0.0.1:8000/mod/all/`，然后把 get 改为 get1, 把 get2 改为 get 重新访问。

虽然针对上面序列化和反序列化，我们可只声明一个序列化器，但是序列化器和模型还是存在很高的相似度。

所以我们可以在序列化器字段和模型字段公共字段较多的时候，基于 ModelSerializer 来声明创建序列化器。

如果我们想要使用序列化器对应的是 Django 的模型类，DRF 为我们提供了 ModelSerializer 模型类序列化器来帮助我们快速创建一个 Serializer 类。

ModelSerializer 是之前序列坏器基类 Serializer 的子类，功能大部分相同，但 ModelSerializer 还提供了：

- 基于模型类自动生成系列化器的字段
- 基于模型类自动为 Serializer 生成 validators，比如 unique_together（多个字段唯一）
- 包含默认的 `create()` 和 `update()` 的实现

### 定义

创建一个 BookInfoModelSerializer 模型序列化器：

```python
class BookInfoModelSerializer(serializers.ModelSerializer):
    """图书数据序列化器"""
        # 从什么模型继承过来
        model = BookInfo
        # 继承那些字段过来
        fields = "__all__"    # 表示继承所有字段
```

- model 指明参照哪个模型类
- fields 指明为模型类的哪些字段生成

我们可以在 `python manage.py` shell 中查看自动生成的 BookInfoSerializer 的具体实现

```python
from rest_framework import serializers

class BookInfoSerializer(serializers.Serializer):
    """图书信息"""
    id = serializers.IntegerField(read_only=True, label="ID", help_text="ID")
    title = serializers.CharField(max_length=20, label="标题", help_text="标题")
    pub_date = serializers.DateField(required=True, label="发布日期", help_text="发布日期")
    image = serializers.ImageField(required=False, allow_null=True, max_length=3 * 1024 * 1024, label="图书封面", help_text="图书封面")
    price = serializers.DecimalField(max_digits=8, decimal_places=2, required=True, label="价格", help_text="价格")
    read = serializers.IntegerField(min_value=0, default=0, label="阅读量", help_text="阅读量")
    comment = serializers.IntegerField(min_value=0, default=0, label="评论量", help_text="评论量")


from unsers.models import BookInfo
class BookInfoModelSerializer(serializers.ModelSerializer):
    # 自定义字段

    # 继承ModelSerializer必须声明内部类Mata
    class Meta:
        # 从什么模型继承过来
        model = BookInfo
        # 继承那些字段过来
        # fields = "__all__" # 表示继承所有字段
        fields = ["id","title","pub_date","price","read","comment"]
        # 如果觉得模型转换的序列化器字段选项要补充说明,则可以extra_kwargs
        # 扩展字段选项
        extra_kwargs = {
            "read":{"min_value":0,"help_text":"阅读量",},
            "title":{"min_length": 2,},
        }

        # def validate(self,data):
            # """所有字段验证"""
            # pass

        # def validate_<字段>
            # pass
```

### 指定字段

1. 使用 fields 来明确字段，`__all__` 表明包含所有字段，也可以写明具体哪些字段，如：

   ```python
   class BookInfoModelSerializer(serializers.ModelSerializer):
       """图书数据序列化器"""
       class Meta:
           model = BookInfo
           fields = "__all__"
   ```

2. 使用 exclude 可以明确排除掉哪些字段：

   ```python
   class BookInfoModelSerializer(serializers.ModelSerializer):
       """图书数据序列化器"""
       class Meta:
           model = BookInfo
           exclude = ['image']
   ```

3. 显示指明字段，如：

   ```python
   class BookInfoModelSerializer(serializers.ModelSerializer):
       class Meta:
           model = BookInfo
           fields = ["id","title","pub_date","price","read","comment"]
   ```

4. 指明只读字段：

   可以通过**read_only_fields**指明只读字段，即仅用于序列化输出的字段

   ```python
   class BookInfoModelSerializer(serializers.ModelSerializer):
       """图书数据序列化器"""
       class Meta:
           model = BookInfo
           fields = ('id', 'title', 'pub_date'， 'read', 'comment')
           read_only_fields = ('id', 'read', 'comment')
   ```

### 添加额外参数

我们可以使用 extra_kwargs 参数为 ModelSerializer 添加或修改原有的选项参数：

```python
from rest_framework import serializers

from unsers.models import BookInfo
class BookInfoModelSerializer(serializers.ModelSerializer):
    # 自定义字段

    # 继承ModelSerializer必须声明内部类Mata
    class Meta:
        # 从什么模型继承过来
        model = BookInfo
        # 继承那些字段过来
        # fields = "__all__" # 表示继承所有字段
        fields = ["id","title","pub_date","price","read","comment"]
        # 如果觉得模型转换的序列化器字段选项要补充说明,则可以extra_kwargs
        # 扩展字段选项[用于替换/添加参数选项]
        extra_kwargs = {
            "read":{"min_value":0,"help_text":"阅读量",},
            "title":{"min_length": 2,},
        }
```

### 补充

- 主键会被自动识别，并设置为只读
- 模型序列化器内部已经写好了 create 和 update 方法，不需要我们自己手动去写

什么时候声明的序列化器需要继承序列化器基类 Serializer，什么时候继承模型序列化器类 ModelSerializer？

- 继承序列化器类 Serializer
  - 字段声明
  - 验证
  - 添加/保存数据功能
- 继承模型序列化器类 ModelSerializer
  - 字段声明（可选，看需要）
  - Meta 声明
  - 验证
  - 添加/保存数据功能（可选）

看表字段大小，看使用哪个更加节省代码。

