# 序列化器 Serializer

序列化器的作用：

1. 序列化，序列化器会把模型对象转换成字典，将来提供给视图经过 response 以后变成 JSON 字符串
2. 反序列化，把客户端发送过来的数据,经过视图调用序列化器以后变成 Python 字典，序列化器可以把字典转成模型
3. 反序列化，完成数据校验功能和操作数据库

## 定义序列化器

Django REST framework 中的 Serializer 使用类来定义，须继承自 `rest_framework.serializers.Serializer`。

首先，创建一个新的子应用 sers

```
python manage.py startapp sers
```

注册子应用到项目中，在 `settings.py` 中添加代码：

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'rest_framework',    # 把drf框架注册到django项目中

    'sers',    # 注册子应用
]
```

因为我们已有了一个数据库模型类 students/Student，我们直接在接下来的演示中使用这个模型。

```python
class Student(models.Model):
    # 模型字段
    name = models.CharField(max_length=100,verbose_name="姓名")
    sex = models.BooleanField(default=1,verbose_name="性别")
    age = models.IntegerField(verbose_name="年龄")
    class_number = models.CharField(max_length=5,verbose_name="班级编号")
    description = models.TextField(max_length=1000,verbose_name="个性签名")

    class Meta:
        db_table="tb_student"
        verbose_name = "学生"
        verbose_name_plural = verbose_name
```

我们想为这个模型类提供一个序列化器，可以命名为 `StudentSerializer`。

我们都会把序列化器代码保存到当前子应用下的 `serializers.py` 模块中，可以定义如下：

```python
from rest_framework import serializers

# 声明序列化器，所有的序列化器都要直接或者间接继承于 Serializer
# 其中，ModelSerializer是Serializer的子类，ModelSerializer在Serializer的基础上进行了代码简化
class StudentSerializer(serializers.Serializer):
    """学生信息序列化器"""
    # 1. 需要进行数据转换的字段
    id = serializers.IntegerField()
    name = serializers.CharField()
    age = serializers.IntegerField()
    sex = serializers.BooleanField()
    description = serializers.CharField()

    # 2. 如果序列化器集成的是ModelSerializer，则需要声明调用的模型信息

    # 3. 验证代码

    # 4. 编写添加和更新模型的代码
```

注意：serializer 不是只能为数据库模型类定义，也可以为非数据库模型类的数据定义。serializer 是独立于数据库之外的存在。

常用字段类型：

| 字段                    | 字段构造方式                                                 |
| ----------------------- | ------------------------------------------------------------ |
| BooleanField        | BooleanField()                                               |
| NullBooleanField    | NullBooleanField()                                           |
| CharField           | CharField(max_length=None, min_length=None, allow_blank=False, trim_whitespace=True) |
| EmailField          | EmailField(max_length=None, min_length=None, allow_blank=False) |
| RegexField          | RegexField(regex, max_length=None, min_length=None, allow_blank=False) |
| SlugField           | SlugField(max*length=50, min_length=None, allow_blank=False) 正则字段，验证正则模式 [a-zA-Z0-9*-]+ |
| URLField            | URLField(max_length=200, min_length=None, allow_blank=False) |
| UUIDField           | UUIDField(format='hex_verbose')  format:  1) `'hex_verbose'` 如`"5ce0e9a5-5ffa-654b-cee0-1238041fb31a"`  2） `'hex'` 如 `"5ce0e9a55ffa654bcee01238041fb31a"`  3）`'int'` - 如: `"123456789012312313134124512351145145114"`  4）`'urn'` 如: `"urn:uuid:5ce0e9a5-5ffa-654b-cee0-1238041fb31a"` |
| IPAddressField      | IPAddressField(protocol='both', unpack_ipv4=False, options) |
| IntegerField        | IntegerField(max_value=None, min_value=None)                 |
| FloatField          | FloatField(max_value=None, min_value=None)                   |
| DecimalField        | DecimalField(max_digits, decimal_places, coerce_to_string=None, max_value=None, min_value=None) max_digits: 最多位数 decimal_palces: 小数点位置 |
| DateTimeField       | DateTimeField(format=api_settings.DATETIME_FORMAT, input_formats=None) |
| DateField           | DateField(format=api_settings.DATE_FORMAT, input_formats=None) |
| TimeField           | TimeField(format=api_settings.TIME_FORMAT, input_formats=None) |
| DurationField       | DurationField()                                              |
| ChoiceField         | ChoiceField(choices) choices与Django的用法相同               |
| MultipleChoiceField | MultipleChoiceField(choices)                                 |
| FileField           | FileField(max_length=None, allow_empty_file=False, use_url=UPLOADED_FILES_USE_URL) |
| ImageField          | ImageField(max_length=None, allow_empty_file=False, use_url=UPLOADED_FILES_USE_URL) |
| ListField           | ListField(child=, min_length=None, max_length=None)          |
| DictField           | DictField(child=)                                            |

选项参数：

| 参数名称            | 作用             |
| ------------------- | ---------------- |
| max_length      | 最大长度         |
| min_lenght      | 最小长度         |
| allow_blank     | 是否允许为空     |
| trim_whitespace | 是否截断空白字符 |
| max_value       | 最小值           |
| min_value       | 最大值           |

通用参数：

| 参数名称               | 说明                                          |
| ---------------------- | --------------------------------------------- |
| read_only| 表明该字段仅用于序列化输出，默认False         |
| write_only         | 表明该字段仅用于反序列化输入，默认False       |
| required           | 表明该字段在反序列化时必须输入，默认True      |
| default            | 反序列化时使用的默认值                        |
| allow_null     | 表明该字段是否允许传入None，默认False         |
| validators     | 该字段使用的验证器                            |
| error_messages | 包含错误编号与错误信息的字典                  |
| label          | 用于HTML展示API页面时，显示的字段名称         |
| help_text              | 用于HTML展示API页面时，显示的字段帮助提示信息 |

## 创建 Serializer 对象

定义好 Serializer 类后，就可以创建 Serializer 对象了。

Serializer 的构造方法为：

```python
Serializer(instance=None, data=empty, **kwarg)
```

说明：

1. 用于序列化时，将模型类对象传入 instance 参数

2. 用于反序列化时，将要被反序列化的数据传入 data 参数

3. 除了 instance 和 data 参数外，在构造 Serializer 对象时，还可通过 context 参数额外添加数据，如

```python
serializer = AccountSerializer(account, context={'request': request})
```

通过context 参数附加的数据，可以通过 Serializer 对象的 `self.context` 属性获取。

1. 使用序列化器的时候一定要注意，序列化器声明了以后，不会自动执行，需要我们在视图中进行调用才可以
2. 序列化器无法直接接收数据，需要我们在视图中创建序列化器对象时把使用的数据传递过来
3. 序列化器的字段声明类似于我们前面使用过的表单系统
4. 开发 restful api 时，序列化器会帮我们把模型数据转换成字典
5. drf 提供的视图会帮我们把字典转换成 JSON，或者把客户端发送过来的数据转换字典



## 序列化器的使用

序列化器的使用分两个阶段：

1. 在客户端请求时，使用序列化器可以完成对数据的反序列化。
2. 在服务器响应时，使用序列化器可以完成对数据的序列化。