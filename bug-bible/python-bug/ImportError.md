# ImportError

## Couldn't import Django

### 错误1.1

报错信息：

```python
Traceback (most recent call last):
  File "manage.py", line 10, in main
    from django.core.management import execute_from_command_line
ModuleNotFoundError: No module named 'django'

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "manage.py", line 21, in <module>
    main()
  File "manage.py", line 16, in main
    ) from exc
ImportError: Couldn't import Django. Are you sure it's installed and available on your PYTHONPATH environment variable? Did you forget to activate a virtual environment?
```

错误代码：

```python
python3 manage.py startapp shopping_cart
```

错误原因：

django 是在虚拟环境中安装的，全局解释器环境中并没有。当创建新的 app 时，没有给 PyCharm 指定虚拟环境。因为找不到 django，所以报错。

解决方法：

给 PyCharm 指定正确的虚拟环境。