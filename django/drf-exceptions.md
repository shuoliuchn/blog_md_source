## 异常处理 Exceptions

### 自定义异常

REST framework 提供了异常处理，我们可以自定义异常处理函数。例如我们想在要创建一个自定义异常函数，

这个函数，我们保存到当前子应用 opt 中（注意，开发时，我们会找个独立的公共目录来保存这种公共的函数/工具/类库。）

```python
from rest_framework.views import exception_handler

def custom_exception_handler(exc, context):
    # 先调用REST framework默认的异常处理方法获得标准错误响应对象
    response = exception_handler(exc, context)

    # 在此处补充自定义的异常处理
    if response is None:
        response.data['status_code'] = response.status_code

    return response
```

在配置文件中声明自定义的异常处理

```python
REST_FRAMEWORK = {
    'EXCEPTION_HANDLER': 'my_project.my_app.utils.custom_exception_handler'
}
```

如果未声明，会采用默认的方式，在 `rest_frame/settings.py` 中修改如下

```python
REST_FRAMEWORK = {
    'EXCEPTION_HANDLER': 'rest_framework.views.exception_handler'
}
```

例如：

补充上处理关于数据库的异常

```python
from rest_framework.views import exception_handler
from django.db import DatabaseError
from rest_framework.response import Response
from rest_framework import status

def custom_exception_handle(exc, context):
    """
    自定义异常处理函数
    :param exc: exception 本次异常对象
    :param context: 本次异常发生时的上下文环境[context]
    :return: 返回响应对象
    """
    # 先让resr_framework来调用异常处理函数
    response = exception_handler(exc, context)

    if response is None:
        """response的值为None,有2种情况：
        1. 程序正常执行，没有发生异常
        2. 程序抛出异常，但是drf没有识别
        """
        view = context['view']
        if isinstance(exc, DatabaseError):
            """判断是否为数据库异常"""
            # 把异常记录起来
            # context["view"] # 异常发生时的视图类
            print('[%s]: %s' % (view, exc))
            response = Response({'detail': '服务器内部错误'}, status=status.HTTP_507_INSUFFICIENT_STORAGE)

    return response
```

### REST framework 定义的异常

- APIException 所有异常的父类
- ParseError 解析错误
- AuthenticationFailed 认证失败
- NotAuthenticated 尚未认证
- PermissionDenied 权限决绝
- NotFound 未找到
- MethodNotAllowed 请求方式不支持
- NotAcceptable 要获取的数据格式不支持
- Throttled 超过限流次数
- ValidationError 校验失败

也就是说，很多的没有在上面列出来的异常，就需要我们在自定义异常中自己处理了。