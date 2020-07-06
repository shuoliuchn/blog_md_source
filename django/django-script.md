## Django 外部脚本调用 models 数据库

如果要在 Django 没有运行的时候，从外部调用 ORM 的 models 数据库，直接导包是不行的。因为 model 的运行需要有 Django 环境。这时，我们就需要把 Django 的环境配置到系统中。

配置的代码可以在 manage.py 文件中找到。

示例代码：

```python
import os

if __name__ == '__main__':
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "orm02.settings")
    import django
    django.setup()
    
	from app01 import models
    ret = models.Book.objects.all().values('title')
    print(ret)
```