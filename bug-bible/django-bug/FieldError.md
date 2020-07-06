# FieldError

## django.core.exceptions.FieldError: Unknown field(s) (tag_name) specified for CategoriesModel

### 错误1.1

报错信息：

```python
Traceback (most recent call last):
  File "C:/Users/Sure/PyProject/神器/hexo_migrator/manage.py", line 21, in <module>
    main()
  File "C:/Users/Sure/PyProject/神器/hexo_migrator/manage.py", line 17, in main
    execute_from_command_line(sys.argv)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\management\__init__.py", line 381, in execute_from_command_line
    utility.execute()
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\management\__init__.py", line 375, in execute
    self.fetch_command(subcommand).run_from_argv(self.argv)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\management\base.py", line 323, in run_from_argv
    self.execute(*args, **cmd_options)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\management\commands\runserver.py", line 60, in execute
    super().execute(*args, **options)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\management\base.py", line 364, in execute
    output = self.handle(*args, **options)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\management\commands\runserver.py", line 95, in handle
    self.run(**options)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\management\commands\runserver.py", line 102, in run
    autoreload.run_with_reloader(self.inner_run, **options)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\utils\autoreload.py", line 579, in run_with_reloader
    start_django(reloader, main_func, *args, **kwargs)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\utils\autoreload.py", line 564, in start_django
    reloader.run(django_main_thread)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\utils\autoreload.py", line 272, in run
    get_resolver().urlconf_module
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\utils\functional.py", line 80, in __get__
    res = instance.__dict__[self.name] = self.func(instance)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\urls\resolvers.py", line 564, in urlconf_module
    return import_module(self.urlconf_name)
  File "c:\python36\lib\importlib\__init__.py", line 126, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 994, in _gcd_import
  File "<frozen importlib._bootstrap>", line 971, in _find_and_load
Exception in thread Thread-1:
Traceback (most recent call last):
  File "c:\python36\lib\threading.py", line 916, in _bootstrap_inner
    self.run()
  File "c:\python36\lib\threading.py", line 864, in run
    self._target(*self._args, **self._kwargs)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\utils\autoreload.py", line 54, in wrapper
    fn(*args, **kwargs)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\management\commands\runserver.py", line 117, in inner_run
    self.check(display_num_errors=True)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\management\base.py", line 390, in check
    include_deployment_checks=include_deployment_checks,
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\management\base.py", line 377, in _run_checks
    return checks.run_checks(**kwargs)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\checks\registry.py", line 72, in run_checks
    new_errors = check(app_configs=app_configs)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\checks\urls.py", line 13, in check_url_config
    return check_resolver(resolver)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\checks\urls.py", line 23, in check_resolver
    return check_method()
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\urls\resolvers.py", line 398, in check
    for pattern in self.url_patterns:
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\utils\functional.py", line 80, in __get__
    res = instance.__dict__[self.name] = self.func(instance)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\urls\resolvers.py", line 571, in url_patterns
    patterns = getattr(self.urlconf_module, "urlpatterns", self.urlconf_module)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\utils\functional.py", line 80, in __get__
    res = instance.__dict__[self.name] = self.func(instance)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\urls\resolvers.py", line 564, in urlconf_module
    return import_module(self.urlconf_name)
  File "c:\python36\lib\importlib\__init__.py", line 126, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 994, in _gcd_import
  File "<frozen importlib._bootstrap>", line 971, in _find_and_load
  File "<frozen importlib._bootstrap>", line 955, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 665, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 678, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "C:\Users\Sure\PyProject\神器\hexo_migrator\hexo_migrator\urls.py", line 21, in <module>
    path('', include('web.urls', namespace='web')),
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\urls\conf.py", line 34, in include
    urlconf_module = import_module(urlconf_module)
  File "c:\python36\lib\importlib\__init__.py", line 126, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 994, in _gcd_import
  File "<frozen importlib._bootstrap>", line 971, in _find_and_load
  File "<frozen importlib._bootstrap>", line 955, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 665, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 678, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "C:\Users\Sure\PyProject\神器\hexo_migrator\web\urls.py", line 2, in <module>
    from . import views
  File "C:\Users\Sure\PyProject\神器\hexo_migrator\web\views.py", line 6, in <module>
    from . import myforms, models
  File "C:\Users\Sure\PyProject\神器\hexo_migrator\web\myforms.py", line 64, in <module>
    class TagsModelForm(forms.ModelForm):
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\forms\models.py", line 266, in __new__
    raise FieldError(message)
django.core.exceptions.FieldError: Unknown field(s) (tag_name) specified for CategoriesModel

  File "<frozen importlib._bootstrap>", line 955, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 665, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 678, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "C:\Users\Sure\PyProject\神器\hexo_migrator\hexo_migrator\urls.py", line 21, in <module>
    path('', include('web.urls', namespace='web')),
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\urls\conf.py", line 34, in include
    urlconf_module = import_module(urlconf_module)
  File "c:\python36\lib\importlib\__init__.py", line 126, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 994, in _gcd_import
  File "<frozen importlib._bootstrap>", line 971, in _find_and_load
  File "<frozen importlib._bootstrap>", line 955, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 665, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 678, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "C:\Users\Sure\PyProject\神器\hexo_migrator\web\urls.py", line 2, in <module>
    from . import views
  File "C:\Users\Sure\PyProject\神器\hexo_migrator\web\views.py", line 6, in <module>
    from . import myforms, models
  File "C:\Users\Sure\PyProject\神器\hexo_migrator\web\myforms.py", line 64, in <module>
    class TagsModelForm(forms.ModelForm):
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\forms\models.py", line 266, in __new__
    raise FieldError(message)
django.core.exceptions.FieldError: Unknown field(s) (tag_name) specified for CategoriesModel
```

错误代码：

```python
class TagsModelForm(forms.ModelForm):
    class Meta:
        fields = ['tag_name']
        model = models.CategoriesModel
```

错误原因：

复制粘贴，导致模型名字忘记修改。应该是 TagsModel，写成了 CategoriesModel.

解决方法：

把模型名改回来。

```python
class TagsModelForm(forms.ModelForm):
    class Meta:
        fields = ['tag_name']
        model = models.TagsMode
```

