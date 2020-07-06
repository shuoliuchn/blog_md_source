# 教程 4：认证 Authentication 和权限 Permission

现在我们的 API 对何人能编辑或删除我们的代码段没有任何的限制。我们想要一些更高级的功能，为了确保：

- 代码段总会和创建者关联起来。
- 只有授权认证了的用户可以创建代码段。
- 只有代码段的创建者才能更新或删除它。

- 未授权的请求可以获得完全的只读访问。

## 给我们的模型增加信息

我们要稍微修改一下我们的 `Snippet` 模型类。首先，让我们添加几个字段。这些字段中的一个将被用来指代创建代码段的用户。其他字段将被用来存储高亮的 HTM 语句代码。

在 `models.py` 里，把下面两个字段添加到 `Snippet` 模型类中。

```python
owner = models.ForeignKey('auth.User', related_name='snippets', on_delete=models.CASCADE)
highlighted = models.TextField()
```

我们也需要确保当模型保存完毕后，我们使用 `pygments` 代码高亮库，把高亮字段迁移。

我们需要额外导入一些包：

```python
from pygments.lexers import get_lexer_by_name
from pygments.formatters.html import HtmlFormatter
from pygments import highlight
```

现在我们可以在我们的模型类中加入一个 `.save()` 方法：

```python
def save(self, *args, **kwargs):
    """
    使用 `pygments` 库来创建代码段的高亮 HTML 语句。
    """
    lexer = get_lexer_by_name(self.language)
    linenos = 'table' if self.linenos else False
    options = {'title': self.title} if self.title else {}
    formatter = HtmlFormatter(style=self.style, linenos=linenos,
                              full=True, **options)
    self.highlighted = highlight(self.code, lexer, formatter)
    super(Snippet, self).save(*args, **kwargs)
```

做完上面这些事，我们就需要更新我们的数据库表。通常要做到这件事我们需要创建一个数据库迁移。但是出于本教程的目的，直接把数据库删了，然后重新来过。

```bash
rm -f db.sqlite3
rm -r snippets/migrations
python manage.py makemigrations snippets
python manage.py migrate
```

你或许也要创建几个不同的用户，用来测试 API。做这件事最快的方式就是使用 `createsuperuser` 命令。

```bash
python manage.py createsuperuser
```

## 给我们的用户 User 模型加点端点 Endpoint

既然我们已经有了一些能够使用的用户，我们最好把这些用户的语句添加到我们的 API。创建一个新的序列化器很简单的，只需在 `serializers.py` 里添加：

```python
from django.contrib.auth.models import User

class UserSerializer(serializers.ModelSerializer):
    snippets = serializers.PrimaryKeyRelatedField(many=True, queryset=Snippet.objects.all())

    class Meta:
        model = User
        fields = ['id', 'username', 'snippets']
```

因为 `snippets` 对于用户 User 模型而言是一个**反向**关联（User 是 snippets 的外键，译注）关系，当使用模型序列化器的时候，默认是不会加进来的。所以我们需要给它增加一个明确的字段。

我们还需要给 `views.py` 增加几个视图。对于用户 user 语句，我们只需让它们成为只读视图就好，所以我们使用的是 `ListAPIView` 和 `RetrieveAPIView` 这两个通用视图类的子类来构建视图。

```python
from django.contrib.auth.models import User


class UserList(generics.ListAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer


class UserDetail(generics.RetrieveAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
```

不要忘了还要导入 `UserSerializer` 类

```python
from snippets.serializers import UserSerializer
```

最后，我们还需要通过在路由配置中引入的方式，把这些视图添加到 API 中。把下面的代码添加到 `snippets/urls.py` 中的 urlpatterns 里面。

```python
path('users/', views.UserList.as_view()),
path('users/<int:pk>/', views.UserDetail.as_view()),
```

## 将 Snippets 和 Users 关联

现在，如果我们创建一个代码段，创建代码段的用户是无法和代码段实例对象关联起来的。用户 user 没有作为序列化语句的一部分发送过来，而是作为传过来的请求的一个属性。

我们解决这个问题的方法是在我们的 snippet 视图中重写一个 `.perform_create()` 方法，这允许我们修改实例保存的管理方式，并且处理隐含在传入的请求或请求 URL 中的任何信息。

在 `SnippetList` 视图类中，增加下面的方法：

```python
def perform_create(self, serializer):
    serializer.save(owner=self.request.user)
```

我们序列化器中的 `create()` 方法现在在传入校验过的请求数据同时，还将被传入一个额外的 `'owner'` 字段。

## 更新我们的序列化器

既然代码段已经和创建它们的用户关联起来了，让我们更新我们的序列化器 `SnippetSerializer` 类来表示这个关系。在 `serializers.py` 中序列化器的定义部分增加如下字段：

```python
owner = serializers.ReadOnlyField(source='owner.username')
```

**注意**：别忘了还要在 `Meta` 类中 fields 列表里加上 `'owner',`。

这个字段的作用很有趣。`source` 参数用来指定使用哪个属性来迁移一个字段，而且可以指向任何一个序列化的对象中的属性。它也可以接受像上面使用的那种用点 `.` 分隔标记语句，这样就可以关联给定的属性，这里用到的方法跟 Django 的模板语言很像。

我们增加的字段是不可输入的 `ReadOnlyField` 类，参照其他可输入的字段，比如 `CharField`、`BooleanField` 等……不可输入的 `ReadOnlyField` 总是只读的，主要应用于已经序列化了的语句，而不会被用来在反序列化时更新模型的实例对象。这里我们也可以使用 `CharField(read_only=True)`。

## 给视图添加需要的权限

既然代码段已经和用户关联起来了，我们想要确保只有授权了的用户才能创建、更新和删除代码段。

REST framework 提供了很多的权限类，我们可以用它们来限制谁能够访问一个给定的视图。在当前情况下，我们想要的是 `IsAuthenticatedOrReadOnly`，它会确保授权了的请求能够得到读写访问，未授权的请求得到只读访问。

首先，在视图中导入下面的模块

```python
from rest_framework import permissions
```

然后，给 `SnippetList` 和 `SnippetDetail` 这两个视图类**都**加上下面的属性。

```python
permission_classes = [permissions.IsAuthenticatedOrReadOnly]
```

## 给可浏览器访问的 API 加上登录功能

如果现在你打开浏览器访问可浏览器访问的 API，你会发现你已经不能创建新的代码段了。如果想要这样做的话，我们需要能够作为用户登录。

通过编辑我们项目级别的 `urls.py` 文件中的 URL 配置，我们可以给可浏览器访问的 API 增加可用的登录视图。

在文件的最顶端，导入下面的模块：

```python
from django.conf.urls import include
```

同时，在文件的末尾，把可浏览器访问的 API 的登录和注销视图加入到 urlpatterns 中。

```python
urlpatterns += [
    path('api-auth/', include('rest_framework.urls')),
]
```

urlpatterns 中的 `'api-auth/'` 部分事实上可以是你想用的任何 URL。

现在如果你再次打开浏览器并刷新页面，你会看见页面的右上方有一个“Login”链接。如果你使用早些时候创建的用户登录，你将能够再次创建代码段。

一旦你创建了足够多的代码段，去 `http://127.0.0.1:8000/users/` 的最后面看看。你会发现在语句里面，每个用户的 `snippets` 字段中，都有一个装有与每个作者关联的代码段的 id 组成的列表。

## 对象级别的权限

我们真正想要的效果是，每个用户都能看到全部代码段，但要确保仅仅代码段的创建者能够更新或删除那一条代码段。

要实现这个功能，我们需要创建一个自定义权限。

在 snippets 应用中，创建一个新文件，`permissions.py`

```python
from rest_framework import permissions


class IsOwnerOrReadOnly(permissions.BasePermission):
    """
    自定义权限，仅允许对象的所有者编辑它。
    """
    def has_object_permission(self, request, view, obj):
        # 读取权限对所有请求开放，
        # 所以我们完全允许 GET、HEAD 或 OPTIONS 请求。
        if request.method in permissions.SAFE_METHODS:
            return True

        # 写入权限仅对代码段的所有者开放。
        return obj.owner == request.user
```

现在，我们可以把自定义的权限添加到我们的代码段实例中。编辑 `SnippetDetail` 视图类的 `permission_classes` 属性：

```python
permission_classes = [permissions.IsAuthenticatedOrReadOnly,
                      IsOwnerOrReadOnly]
```

不要忘了导入 `IsOwnerOrReadOnly` 类。

```python
from snippets.permissions import IsOwnerOrReadOnly
```

现在，如果你再次打开浏览器，你会发现对某个代码段实例的 `DELETE` 和 `PUT` 选项按钮仅仅会在使用创建改代码段的用户登陆时才会出现。

## 通过 API 认证

因为此时我们的 API 有一系列的权限，如果我们想要修改某个代码段的话，我们就需要认证我们的请求。我们尚未设置任何的 [认证类](https://www.django-rest-framework.org/api-guide/authentication/)，所以默认的就是当前应用的 `SessionAuthentication` 和 `BasicAuthentication`。

当我们使用浏览器和 API 交互的时候，我们可以登录，然后浏览器的 session 就会给请求提供相应的认证。

如果我们以编程的方式和 API 交互，我们需要给每一个请求明确提供认证证书。

如果我们尝试着在没有认证的情况下创建一个代码段，我们会接收到错误响应：

```python
http POST http://127.0.0.1:8000/snippets/ code="print(123)"

{
    "detail": "Authentication credentials were not provided."
}
```

如果把我们早些时候创建的用户名和密码带上，我们将能够发起成功的请求。

```python
http -a admin:password123 POST http://127.0.0.1:8000/snippets/ code="print(789)"

{
    "id": 1,
    "owner": "admin",
    "title": "foo",
    "code": "print(789)",
    "linenos": false,
    "language": "python",
    "style": "friendly"
}
```

## 总结

现在我们已经给我们的 Web API 加上了一系列很精细的权限，并且给用户建立了端点，并且给它们创建的代码设置了权限。

在 [教程的第 5 部分](relationship-hyperlinked-api.md) 我们将领略如何通过给我们的高亮代码段创建一个 HTML 重点把所有东西捆绑在一起，进而通过系统中的关系的超链接提高我们 API 的关联性。
