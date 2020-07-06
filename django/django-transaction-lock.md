## ORM 事务和锁

### 锁

我们可以这样给代码加锁：

```python
models.Book.objects.select_for_update().filter(id=1)
```

### 事务

#### 方式 1 全局配置

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mxshop',
        'HOST': '127.0.0.1',
        'PORT': '3306',
        'USER': 'root',
        'PASSWORD': '123',
        "ATOMIC_REQUESTS": True,    # 全局开启事务，绑定的是http请求响应整个过程当中的sql
    }
}
```

#### 方式 2 视图函数加装饰器

```python
from django.db import transaction
@transaction.atomic
def viewfunc(request):
    # This code executes inside a transaction.
    do_stuff()
```

#### 方式 3 上下文加装饰器

```python
from django.db import transaction
def viewfunc(request):
    # This code executes in autocommit mode (Django's default).
    do_stuff()

    with transaction.atomic():    # 保存点
        # This code executes inside a transaction.
        do_more_stuff()

    do_other_stuff()
```