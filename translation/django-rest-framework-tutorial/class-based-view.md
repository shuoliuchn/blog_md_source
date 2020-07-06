# 教程 3：类视图 Class-based View

除了使用函数视图（function based view）外，我们也可以用类视图（class-based view）编写我们的 API 视图。我们将看到，这是一个十分强大的模式，它让我们能够重复使用共同的功能，并且让我们的代码保持 [DRY](https://en.wikipedia.org/wiki/Don't_repeat_yourself)。

## 使用类视图重写我们的 API

我们将要把根视图重写为类试图形式。所有这些所设计的仅仅是对 `views.py` 进行的一丁点重构。

```python
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from django.http import Http404
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status


class SnippetList(APIView):
    """
    列举所有的代码段，或者创建一个新的代码段。
    """
    def get(self, request, format=None):
        snippets = Snippet.objects.all()
        serializer = SnippetSerializer(snippets, many=True)
        return Response(serializer.data)

    def post(self, request, format=None):
        serializer = SnippetSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

到目前为止，一切都好。这看起来和前面的情形是一样的，不过我们把不同的 HTTP 请求方法很好地区分开了。我们也需要更新 `views.py` 中的实例视图。

```python
class SnippetDetail(APIView):
    """
    查看、更新或者删除一条代码段实例。
    """
    def get_object(self, pk):
        try:
            return Snippet.objects.get(pk=pk)
        except Snippet.DoesNotExist:
            raise Http404

    def get(self, request, pk, format=None):
        snippet = self.get_object(pk)
        serializer = SnippetSerializer(snippet)
        return Response(serializer.data)

    def put(self, request, pk, format=None):
        snippet = self.get_object(pk)
        serializer = SnippetSerializer(snippet, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk, format=None):
        snippet = self.get_object(pk)
        snippet.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

这看起来不错。再一次，现在它仍然和函数试图很相似。

我们也需要稍微重构一些我们的 `snippets/urls.py`，因为我们正在使用类视图。

```python
from django.urls import path
from rest_framework.urlpatterns import format_suffix_patterns
from snippets import views

urlpatterns = [
    path('snippets/', views.SnippetList.as_view()),
    path('snippets/<int:pk>/', views.SnippetDetail.as_view()),
]

urlpatterns = format_suffix_patterns(urlpatterns)
```

好了，完事了。如果你运行开发服务器的话，那么一切都会和往常一样运作。

## 使用扩展视图类 mixin

使用类视图最大的成功之一在于它使得我们重用方法变得非常容易。

到目前为止，我们使用到的增/查/改/删操作在我们创建的任何一种模型 API 视图下都十分相似。这些共同的操作被封装为 REST framework 的扩展视图类 mixin。

让我们康康通过使用扩展视图类 mixin 如何撰写视图代码。再次，这里是我们的 `views.py` 模块。

```python
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from rest_framework import mixins
from rest_framework import generics

class SnippetList(mixins.ListModelMixin,
                  mixins.CreateModelMixin,
                  generics.GenericAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer

    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)
```

我们得花一小段时间测试一下这里究竟发生了什么。我们使用通用视图类 `GenericAPIView` 来搭建我们的视图，并且添加了 `ListModelMixin` 和 `CreateModelMixin` 。

基类提供了核心功能，扩展试图类 mixin 提供了 `.list()` 和 `.create()` 功能。然后我们明确地将 `get` 和 `post` 方法同最接近的功能（action）绑定。废话就先说到这里。

```python
class SnippetDetail(mixins.RetrieveModelMixin,
                    mixins.UpdateModelMixin,
                    mixins.DestroyModelMixin,
                    generics.GenericAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer

    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)

    def put(self, request, *args, **kwargs):
        return self.update(request, *args, **kwargs)

    def delete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)
```

十分相似。我们再次使用通用视图基类提供核心的功能，引入扩展视图类 mixin 提供 `.retrieve()`、`.update()` 和 `.destroy()` 功能。

## 使用通用试图子类 generic class-based view

使用扩展视图类，我们编写视图用到的代码要比从前少一些了，不过我们可以更进一步简化代码。REST framework 提供一系列扩展了的通用视图类，我们可以用它们进一步削减 `views.py` 模块中的代码。

```python
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from rest_framework import generics


class SnippetList(generics.ListCreateAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer


class SnippetDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer
```

哇，这真的好简洁。我们获得了大量的自由，我们的代码看起来舒适、整洁、地道的 Django 味。

接下来，我们将前往 [教程的第 4 部分](authentication-permission.md)，在那里我们将领略如何处理我们 API 的认证和权限。