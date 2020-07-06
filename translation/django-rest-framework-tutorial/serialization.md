# 教程 1：序列化 Serialization

## 介绍

这篇教程包括创建一个简单的粘贴代码突出显示 Web API。在此过程中，会介绍各种不同的构造成 REST framework 的组件，让你全面了解这一切是如何协调在一起的。

这篇教程有一定的深度，所以在开始前，你最好吃一块曲奇饼干，喝一杯你最喜欢的饮料。如果你只想要快速概览，那么你不该来这，你应该转头去 [快速入门](quickstart.md) 这篇文档。

> **注意**：这篇教程的代码可以在 GitHub 的 [encode/rest-framework-tutorial](https://github.com/encode/rest-framework-tutorial) 仓库拿到。项目的成品已经发布到网上，可以进行沙盒测试，[点击这里获取](https://restframework.herokuapp.com/)。


## 搭建一个新环境

在进行其他操作之前，我们要使用 [venv](https://docs.python.org/3/library/venv.html) 创建一个新的虚拟环境。这将确保我们的包配置同我们正在着手的其他项目隔离开来。

```bash
python3 -m venv env
source env/bin/activate
```

既然已经进入了一个虚拟环境，我们就可以安装依赖包了。

```bash
pip install django
pip install djangorestframework
pip install pygments  # 我们要用这个实现代码高亮
```

**注意：**任何时候，如果你想退出虚拟环境，直接输入命令 `deactivate`。更多信息参见 [venv documentation](https://docs.python.org/3/library/venv.html)。

## 准备开始

好了，我们已经准备好开始敲代码了。在此之前，让我们创建一个供我们操作的新项目。

```bash
cd ~
django-admin startproject tutorial
cd tutorial
```

一旦创建完成，我们可以创建一个应用，我们将用它创造一个简单的 Web API。

```bash
python manage.py startapp snippets
```

我们需要把我们新建的 `snippets` 应用和 `rest_framework` 应用添加到 `INSTALLED_APPS`。让我们编辑 `tutorial/settings.py` 文件：

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'snippets.apps.SnippetsConfig',
]
```

好了，我们已经准备开始摇滚了。

## 创建一个操作用的模型 model

出于这篇教程的目的，我们要开始创建一个简单的 `Snippet` 模型，用来存储代码段。请编辑 `snippets/models.py` 文件。注意：好的编程练习应包括注释。我们把它给略去了从而更专注于代码本身。你可以在这篇教程代码的仓库版找到这些注释。

```python
from django.db import models
from pygments.lexers import get_all_lexers
from pygments.styles import get_all_styles

LEXERS = [item for item in get_all_lexers() if item[1]]
LANGUAGE_CHOICES = sorted([(item[1][0], item[0]) for item in LEXERS])
STYLE_CHOICES = sorted([(item, item) for item in get_all_styles()])


class Snippet(models.Model):
    created = models.DateTimeField(auto_now_add=True)
    title = models.CharField(max_length=100, blank=True, default='')
    code = models.TextField()
    linenos = models.BooleanField(default=False)
    language = models.CharField(choices=LANGUAGE_CHOICES, default='python', max_length=100)
    style = models.CharField(choices=STYLE_CHOICES, default='friendly', max_length=100)

    class Meta:
        ordering = ['created']
```

我们也需要为我们这一小段代码进行初始化数据迁移，并且首次同步到数据库中。

```bash
python manage.py makemigrations snippets
python manage.py migrate
```

## 创建一个序列化器类 Serializer

我们开始创建我们的 Web API 要做的第一件事是提供一个序列化和反序列化代码段实例为诸如 `json` 表达式的方法。我们可能通过类似与 Django 的表单操作一样声明序列化器来完成这个功能。在 `snippets` 路径中创建一个名为 `serializers.py` 的文件，并在里面写入下面这些代码。

```python
from rest_framework import serializers
from snippets.models import Snippet, LANGUAGE_CHOICES, STYLE_CHOICES


class SnippetSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(required=False, allow_blank=True, max_length=100)
    code = serializers.CharField(style={'base_template': 'textarea.html'})
    linenos = serializers.BooleanField(required=False)
    language = serializers.ChoiceField(choices=LANGUAGE_CHOICES, default='python')
    style = serializers.ChoiceField(choices=STYLE_CHOICES, default='friendly')

    def create(self, validated_data):
        """
        创建并返回一个新的 `Snippet` 实例，需要传入通过验证的数据
        """
        return Snippet.objects.create(**validated_data)

    def update(self, instance, validated_data):
        """
        更新并返回一个已存在的 `Snippet` 实例，需要传入通过验证的数据
        """
        instance.title = validated_data.get('title', instance.title)
        instance.code = validated_data.get('code', instance.code)
        instance.linenos = validated_data.get('linenos', instance.linenos)
        instance.language = validated_data.get('language', instance.language)
        instance.style = validated_data.get('style', instance.style)
        instance.save()
        return instance
```

序列化器类的第一部分定义了需要进行序列化/反序列化的字段。`create()` 和 `update()` 方法定义了当调用方法 `serializer.save()` 时，处理完毕的实例时如何被创建或修改的。

序列化器类和 Django 的 `Form` 类非常相似，包括在各种字段中使用类似的校验标识（flags），比如 `required`、`max_length` 和 `default`。

在特定的情况下，字段标识也可以控制序列化器应该如何展示，比如转换为 HTML。上面的 `{'base_template': 'textarea.html'}` 标识和在 Django `Form` 类中使用 `widget=widgets.Textarea` 是等价的。这在控制可浏览器访问的 API 应该如何显示时是非常有用，我们过会儿就能在这篇教程里面看到。

我们事实上也可以通过使用 `ModelSerializer` 类来节省一些时间，我们一会儿会看到，不过现在我们要保持我们的序列化器定义清晰明确。

## 使用序列化器 Serializer

在我们前进之前，我们要熟悉使用我们新的序列化器类。让我们钻进 Django shell 中一探究竟。

```bash
python manage.py shell
```

好了，一旦我们完成几个导入，让我们再创建要用到的代码段（下面这些代码都要在上面创建的 Django shell 命令行中输入，若嫌麻烦，复制粘贴即可——译者注）。

```python
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from rest_framework.renderers import JSONRenderer
from rest_framework.parsers import JSONParser

snippet = Snippet(code='foo = "bar"\n')
snippet.save()

snippet = Snippet(code='print("hello, world")\n')
snippet.save()
```

现在我们已经有几个代码段实例来供我们玩弄了。让我们看看序列化这里面的一个实例。

```python
serializer = SnippetSerializer(snippet)
serializer.data
# {'id': 2, 'title': '', 'code': 'print("hello, world")\n', 'linenos': False, 'language': 'python', 'style': 'friendly'}
```

至此，我们已经把模型对象翻译成了 Python 原生的数据类型。要完成序列化过程，我们把数据转换成 `json`。

```python
content = JSONRenderer().render(serializer.data)
content
# b'{"id": 2, "title": "", "code": "print(\\"hello, world\\")\\n", "linenos": false, "language": "python", "style": "friendly"}'
```

反序列化是类似的。首先我们把一个数据流转换成 Python 原生数据类型…

```
import io

stream = io.BytesIO(content)
data = JSONParser().parse(stream)
```

...然后我们把这些把这些原生数据类型复原为五脏俱全的 object 对象。

```python
serializer = SnippetSerializer(data=data)
serializer.is_valid()
# True
serializer.validated_data
# OrderedDict([('title', ''), ('code', 'print("hello, world")\n'), ('linenos', False), ('language', 'python'), ('style', 'friendly')])
serializer.save()
# <Snippet: Snippet object>
```

注意 API 的操作和表单操作是多么的相似。这种相似性会在我们开始写视图函数用到序列化器时变得尤为明显。

除了模型对象，我们也可以序列化数据集（querysets）。要完成这件事，我们只需简单地在序列化器参数里面加上一个 `many=True` 标识。

```python
serializer = SnippetSerializer(Snippet.objects.all(), many=True)
serializer.data
# [OrderedDict([('id', 1), ('title', ''), ('code', 'foo = "bar"\n'), ('linenos', False), ('language', 'python'), ('style', 'friendly')]), OrderedDict([('id', 2), ('title', ''), ('code', 'print("hello, world")\n'), ('linenos', False), ('language', 'python'), ('style', 'friendly')]), OrderedDict([('id', 3), ('title', ''), ('code', 'print("hello, world")'), ('linenos', False), ('language', 'python'), ('style', 'friendly')])]
```

## 使用模型序列化器 ModelSerializer

我们的 `SnippetSerializer` 类复制了很多 `Snippet` 模型中已经存在的信息。如果我们能让我们的代码更加简洁就太好了。

与 Django 提供 `Form` 类的同时也提供 `ModelForm` 类一样的道理，REST framework 既有 `Serializer` 类，也有 `ModelSerializer` 类。

让我们看看使用 `ModelSerializer` 类重构我们的序列化器。再次打开 `snippets/serializers.py` 文件，用下面的代码替换掉 `SnippetSerializer` 类。

```python
class SnippetSerializer(serializers.ModelSerializer):
    class Meta:
        model = Snippet
        fields = ['id', 'title', 'code', 'linenos', 'language', 'style']
```

序列化器有一个好的特性是你可以通过打印表达式的方法查看一个序列化器示例中的所有字段细节。使用命令 `python manage.py shell` 打开 Django shell，然后试试下面的命令：

```python
from snippets.serializers import SnippetSerializer
serializer = SnippetSerializer()
print(repr(serializer))
# SnippetSerializer():
#    id = IntegerField(label='ID', read_only=True)
#    title = CharField(allow_blank=True, max_length=100, required=False)
#    code = CharField(style={'base_template': 'textarea.html'})
#    linenos = BooleanField(required=False)
#    language = ChoiceField(choices=[('Clipper', 'FoxPro'), ('Cucumber', 'Gherkin'), ('RobotFramework', 'RobotFramework'), ('abap', 'ABAP'), ('ada', 'Ada')...
#    style = ChoiceField(choices=[('autumn', 'autumn'), ('borland', 'borland'), ('bw', 'bw'), ('colorful', 'colorful')...
```

一定要知道 `ModelSerializer` 类没有做任何特殊的魔法，他们只不过是给创建序列化类简化了一条捷径：

- 自动检测字段集。
- 简单默认提供 `create()` 和 `update()` 方法。

## 使用我们的序列化器编写常规的 Django 视图 views

让我们看看使用我们新的序列化器类如果编写 API 视图函数。此刻开始，我们将不再使用任何其他的 REST framework 特性，我们只是按照常规 Django 视图的写法编写视图。

编辑 `snippets/views.py` 文件，加入下面的代码。

```python
from django.http import HttpResponse, JsonResponse
from django.views.decorators.csrf import csrf_exempt
from rest_framework.parsers import JSONParser
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
```

我们 API 的根将会是一个视图，支持列举出全部存在的代码段，或者创建一个新的代码段。

```python
@csrf_exempt
def snippet_list(request):
    """
    列举出所有的代码段，或者创建一个新的代码段。
    """
    if request.method == 'GET':
        snippets = Snippet.objects.all()
        serializer = SnippetSerializer(snippets, many=True)
        return JsonResponse(serializer.data, safe=False)

    elif request.method == 'POST':
        data = JSONParser().parse(request)
        serializer = SnippetSerializer(data=data)
        if serializer.is_valid():
            serializer.save()
            return JsonResponse(serializer.data, status=201)
        return JsonResponse(serializer.errors, status=400)
```

注意因为我们想要客户端能够发送 POST 请求到这个视图，且不需要提供 CSRF token，所以我们把这个视图使用 `csrf_exempt` 装饰标记。通常你并不会这么做，而且实际上 REST framework 视图会使用比这个更明智的做法。但是对于我们此时的需求来说，像上面这样已经足够了。

我们也需要一个视图对应一个独立的代码段，可以被用来查看、更新或者删除这个代码段。

```python
@csrf_exempt
def snippet_detail(request, pk):
    """
    查看、更新或删除一个代码段。
    """
    try:
        snippet = Snippet.objects.get(pk=pk)
    except Snippet.DoesNotExist:
        return HttpResponse(status=404)

    if request.method == 'GET':
        serializer = SnippetSerializer(snippet)
        return JsonResponse(serializer.data)

    elif request.method == 'PUT':
        data = JSONParser().parse(request)
        serializer = SnippetSerializer(snippet, data=data)
        if serializer.is_valid():
            serializer.save()
            return JsonResponse(serializer.data)
        return JsonResponse(serializer.errors, status=400)

    elif request.method == 'DELETE':
        snippet.delete()
        return HttpResponse(status=204)
```

最后我们需要把这些视图函数连接起来。创建 `snippets/urls.py` 文件：

```python
from django.urls import path
from snippets import views

urlpatterns = [
    path('snippets/', views.snippet_list),
    path('snippets/<int:pk>/', views.snippet_detail),
]
```

我们也需要接通跟路由配置，在 `tutorial/urls.py` 文件中，引入我们 snippet 应用的 URL。

```python
from django.urls import path, include

urlpatterns = [
    path('', include('snippets.urls')),
]
```

此刻不必在意一些我们没有妥善处理的边界条件。如果我们发送不合标准的 `json`，或者如果一个请求需要我们视图中尚未处理的方法，那么我们会遭遇一个 500 "server error" 响应。即便如此，对于现在已经够用了。

## 测试我们第一个 Web API 尝试

现在我们可以启动一个样本服务器，给我们的 snippets 应用提供服务。

退出 Django shell…

```python
quit()
```

…开始 Django 的开发服务器。

```bash
python manage.py runserver

Validating models...

0 errors found
Django version 1.11, using settings 'tutorial.settings'
Development server is running at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

在另一个终端窗口，我们可以测试这个服务器。

我们可以使用 [curl](https://curl.haxx.se/) 或者 [httpie](https://github.com/jakubroztocil/httpie#installation) 测试我们的 API。Httpie 是用 Python 编写的一个用户友好的 http 客户端。让我们安装它把。

你可以使用 pip 安装 httpie：

```bash
pip install httpie
```

终于，我们可以得出所有的代码段列表：

```python
http http://127.0.0.1:8000/snippets/

HTTP/1.1 200 OK
...
[
  {
    "id": 1,
    "title": "",
    "code": "foo = \"bar\"\n",
    "linenos": false,
    "language": "python",
    "style": "friendly"
  },
  {
    "id": 2,
    "title": "",
    "code": "print(\"hello, world\")\n",
    "linenos": false,
    "language": "python",
    "style": "friendly"
  }
]
```

或者我们可以通过指定它的 id 得到一条特定的代码段：

```python
http http://127.0.0.1:8000/snippets/2/

HTTP/1.1 200 OK
...
{
  "id": 2,
  "title": "",
  "code": "print(\"hello, world\")\n",
  "linenos": false,
  "language": "python",
  "style": "friendly"
}
```

类似地，你可以使用浏览器访问这些 URL 得到相同的 json 显示。

## 我们现在到哪了

到目前为止我们一切都好，我们得到了一个和常规 Django 的 Form API 很相似的序列化器 API，还有一些常规的 Django 视图。

在这里，我们的 API 视图没有做任何特别特殊的事情，除了提供 `json` 响应服务。而且还有一些边缘条件的异常处理问题我们想要清理掉。不过它终究还是一个能用的 Web API。

我们将在 [教程的第 2 部分](requests-response.md) 看到如何优化我们的代码。

