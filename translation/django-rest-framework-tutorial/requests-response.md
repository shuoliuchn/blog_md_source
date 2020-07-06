# 教程 2：请求 Request 和响应 Response

从此刻开始，我们才真正涉及 REST framework 的核心部分。让我们介绍几个极其重要的结构组件（building blocks）。

## 请求对象 Request

引入一个 `Request` 对象扩展了常规的 `HttpRequest`，提供更灵活的请求解析。`Request` 对象的核心功能是 `request.data` 属性，跟 `request.POST` 有点像，但是对于操作 Web API 来说更有用。

```python
request.POST  # 仅处理表单数据。仅对 'POST' 方法有效。
request.data  # 处理任意数据。'POST'、'PUT' 和 'PATCH' 方法都能用
```

## 响应对象 Response

REST framework 也提供了一个 `Response` 对象。它是 `TemplateResponse` 类型，接收为渲染的内容，通过使用内容协议来确定正确的内容类型（content type）返回给客户端。

```python
return Response(data)  # 渲染为客户端请求的内容类型。
```

## 状态码

在视图中使用数字化的状态码未必总会被明显阅读到，而且即便你得到一个错误的状态码也很容易忽视。REST framework 在 `status` 模块中给每个状态码都提供了更加明确的标识符，比如 `HTTP_400_BAD_REQUEST`。始终使用这些而不是数字标识符无疑是明智之举。

## API 视图封装 wrapper

REST framework 提供两个用于编写 API 视图的封装。

1. `@api_view` 装饰器用于处理函数视图（function based views）。
2. `APIView` 类用于处理类视图（class-based views）。

这些封装器（wrapper）提供了几个功能，比如确保在你在视图中接收到 `Request` 实例对象，并且给 `Response` 对象增加语句，从而内容协议能够被执行。

封装器也提供了一些行为，比如在恰当的时候返回 `405 Method Not Allowed` 响应，也会处理任何在获取到异常输入的 `request.data` 时引发的 `ParseError` 解析异常。

## 把它们全都拼凑起来

好了，让我们继续，开始用这些新组件稍微重构一下我们的视图（views）。

```python
from rest_framework import status
from rest_framework.decorators import api_view
from rest_framework.response import Response
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer


@api_view(['GET', 'POST'])
def snippet_list(request):
    """
    列举所有代码段，或者创建一个新的代码段。
    """
    if request.method == 'GET':
        snippets = Snippet.objects.all()
        serializer = SnippetSerializer(snippets, many=True)
        return Response(serializer.data)

    elif request.method == 'POST':
        serializer = SnippetSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

我们的实例视图是对之前视图的一个优化。它更加简明一点，而且现在的代码同我们过去操作的 Forms API 非常相似了。我们也在使用名称化的状态码，这使得响应的含义更加显而易见。

这里是针对一个单独的代码段的视图，在 `views.py` 模块中。

```python
@api_view(['GET', 'PUT', 'DELETE'])
def snippet_detail(request, pk):
    """
    获取、更新或者删除一个代码段。
    """
    try:
        snippet = Snippet.objects.get(pk=pk)
    except Snippet.DoesNotExist:
        return Response(status=status.HTTP_404_NOT_FOUND)

    if request.method == 'GET':
        serializer = SnippetSerializer(snippet)
        return Response(serializer.data)

    elif request.method == 'PUT':
        serializer = SnippetSerializer(snippet, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == 'DELETE':
        snippet.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

这应该都很熟悉了——这跟常规的 Django 视图没什么不同。

注意我们不再将我们的请求和响应与得到的内容类型明确绑定。`request.data` 不但可以处理获得的 `json` 请求，也能够处理其他格式。类似地，我们返回带着数据的响应对象，而让 REST framework 帮我们把响应渲染成正选的内容类型。

## 给我们的 URL 添加可选的格式后缀

针对我们的响应不再硬性关联到单个内容类型这一情况，让我们为 API 端点添加对格式后缀的支持。使用格式后缀给我们明确指明格式的 URL，这意味着我们的 API 将能够处理 http://example.com/api/items/4.json 这样的 URL。

让我们以给两个视图都加上一个 `format` 关键字参数开始，就像这样。

```python
def snippet_list(request, format=None):
```

和

```python
def snippet_detail(request, pk, format=None):
```

现在，稍微更新一下 `snippets/urls.py` 文件，在已经存在的 URL 后面追加一系列的 `format_suffix_patterns`。

```python
from django.urls import path
from rest_framework.urlpatterns import format_suffix_patterns
from snippets import views

urlpatterns = [
    path('snippets/', views.snippet_list),
    path('snippets/<int:pk>', views.snippet_detail),
]

urlpatterns = format_suffix_patterns(urlpatterns)
```

我们不必加入这些额外的 url 模式，不过这给我们提供一个简单整洁的方式查看一个特定的格式。

## 它看起来如何？

继续，从命令行测试 API，就像我们在 [教程第 1 部分](serialization.md) 做的那样。一切都似曾相识，不过如果发送无效的请求，我们已经能够得到一些更好的错误处理。

我们可以得到所有代码段的列表，就像之前那样。

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

我们既可以通过使用请求头的方式控制我们想要拿回的响应格式：

```bash
http http://127.0.0.1:8000/snippets/ Accept:application/json  # 请求 JSON
http http://127.0.0.1:8000/snippets/ Accept:text/html         # 请求 HTML
```

也可以通过末尾加上格式后缀：

```bash
http http://127.0.0.1:8000/snippets.json  # JSON 后缀
http http://127.0.0.1:8000/snippets.api   # 可浏览器查看的 API 后缀
```

类似地，通过使用 `Content-Type` 请求头，我们可以控制我们发送求情的格式。

```python
# POST 方法发送表单数据
http --form POST http://127.0.0.1:8000/snippets/ code="print(123)"

{
  "id": 3,
  "title": "",
  "code": "print(123)",
  "linenos": false,
  "language": "python",
  "style": "friendly"
}

# POST 方法发送 JSON 数据
http --json POST http://127.0.0.1:8000/snippets/ code="print(456)"

{
    "id": 4,
    "title": "",
    "code": "print(456)",
    "linenos": false,
    "language": "python",
    "style": "friendly"
}
```

如果你在上面的 `http` 请求中加上一个 `--debug` 开关，你将能在请求头中看到请求类型。

现在去浏览器中打开 API，访问 http://127.0.0.1:8000/snippets/。

### 可浏览器访问性

因为 API 是基于客户端的请求来选择响应的内容类型，所以当浏览器访问资源时，它将默认返回一个 HTML 格式的资源语句。这允许 API 返回一个完全可浏览器访问的 HTML 语句。

得到一个可浏览器访问的 API 是一个巨大的可用性胜利，这使得开发和使用你的 API 更加容易。这也极大地降低了其他开发者研究和使用你的 API 的门槛。

参见 [可浏览器访问的 api](https://www.django-rest-framework.org/topics/browsable-api/) 话题获取更多关于可浏览器访问的 API 特性和如何定制它的信息。

## 接下来做什么？

在 [教程的第 3 部分](class-based-view.md)，我们将会开始使用类视图（class-based views），并且管窥通用视图是如何减少我们要写的代码量的。