# TypeError

## __init__() takes 1 positional argument but 2 were given

### 错误1.1

报错信息：

```python
Internal Server Error: /import/old/
Traceback (most recent call last):
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\handlers\exception.py", line 34, in inner
    response = get_response(request)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\handlers\base.py", line 115, in _get_response
    response = self.process_exception_by_middleware(e, request)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\handlers\base.py", line 113, in _get_response
    response = wrapped_callback(request, *callback_args, **callback_kwargs)
TypeError: __init__() takes 1 positional argument but 2 were given
```

错误代码：

```python
path('import/old/', views.BulkImportOld, name='bulk_import_old'),
```

错误原因：

CBV 写路由代码时，没有调用 `as_view()` 方法。

解决方法：

在路甬钟使用上 `as_view()` 方法。

```python
path('import/old/', views.BulkImportOld.as_view(), name='bulk_import_old'),
```

