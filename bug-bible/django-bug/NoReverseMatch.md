# NoReverseMatch

## django.urls.exceptions.NoReverseMatch: Reverse for 'categories_edit' with arguments '('',)' not found. 1 pattern(s) tried: ['categories/edit/(?P<pk>\\d+)/']

### 错误1.1

报错信息：

```python
Internal Server Error: /categories/list/
Traceback (most recent call last):
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\handlers\exception.py", line 34, in inner
    response = get_response(request)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\handlers\base.py", line 115, in _get_response
    response = self.process_exception_by_middleware(e, request)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\core\handlers\base.py", line 113, in _get_response
    response = wrapped_callback(request, *callback_args, **callback_kwargs)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\views\generic\base.py", line 71, in view
    return self.dispatch(request, *args, **kwargs)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\views\generic\base.py", line 97, in dispatch
    return handler(request, *args, **kwargs)
  File "C:\Users\Sure\PyProject\神器\hexo_migrator\web\views.py", line 88, in get
    return render(request, 'categories_list.html', {'categories_list': categories_list, 'tags_list': tags_list})
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\shortcuts.py", line 36, in render
    content = loader.render_to_string(template_name, context, request, using=using)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\loader.py", line 62, in render_to_string
    return template.render(context, request)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\backends\django.py", line 61, in render
    return self.template.render(context)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\base.py", line 171, in render
    return self._render(context)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\base.py", line 163, in _render
    return self.nodelist.render(context)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\base.py", line 937, in render
    bit = node.render_annotated(context)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\base.py", line 904, in render_annotated
    return self.render(context)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\loader_tags.py", line 150, in render
    return compiled_parent._render(context)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\base.py", line 163, in _render
    return self.nodelist.render(context)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\base.py", line 937, in render
    bit = node.render_annotated(context)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\base.py", line 904, in render_annotated
    return self.render(context)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\loader_tags.py", line 62, in render
    result = block.nodelist.render(context)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\base.py", line 937, in render
    bit = node.render_annotated(context)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\base.py", line 904, in render_annotated
    return self.render(context)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\defaulttags.py", line 209, in render
    nodelist.append(node.render_annotated(context))
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\base.py", line 904, in render_annotated
    return self.render(context)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\template\defaulttags.py", line 443, in render
    url = reverse(view_name, args=args, kwargs=kwargs, current_app=current_app)
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\urls\base.py", line 90, in reverse
    return iri_to_uri(resolver._reverse_with_prefix(view, prefix, *args, **kwargs))
  File "C:\Users\Sure\Envs\hexo\lib\site-packages\django\urls\resolvers.py", line 660, in _reverse_with_prefix
    raise NoReverseMatch(msg)
django.urls.exceptions.NoReverseMatch: Reverse for 'categories_edit' with arguments '('',)' not found. 1 pattern(s) tried: ['categories/edit/(?P<pk>\\d+)/']
```

错误代码：

```django
{% for categories in categories_list %}
<tr>
    <td>{{ categories.wrapper_folder }}</td>
    <td>{{ categories.category_name }}</td>
    <td>
        <a href="{% url 'web:categories_edit' tags.pk %}" class="btn btn-info btn-xs">
            <i class="glyphicon glyphicon-edit"></i>
        </a>
    </td>
</tr>
{% endfor %}
```

错误原因：

复制粘贴，导致变量名忘记修改。应该是 categories，写成了 tags，导致无法正常渲染。

解决方法：

把变量名改回来

```django
{% for categories in categories_list %}
<tr>
    <td>{{ categories.wrapper_folder }}</td>
    <td>{{ categories.category_name }}</td>
    <td>
        <a href="{% url 'web:categories_edit' categories.pk %}" class="btn btn-info btn-xs">
            <i class="glyphicon glyphicon-edit"></i>
        </a>
    </td>
</tr>
{% endfor %}
```

