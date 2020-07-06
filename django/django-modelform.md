## Django ModelForm 模型表单组件

我相信你已经发现了，form 类的写法和模型类十分相似，连字段名字都很相近。事实上，通常在 Django 项目中，我们编写的大部分都是与 Django 的模型紧密映射的表单。 举个例子，你也许会有个 Book 模型，并且你还想创建一个 form 表单用来添加和编辑书籍信息到这个模型中。 在这种情况下，在 form 表单中定义字段将是冗余的，因为我们已经在模型中定义了那些字段。

基于这个原因，Django 提供一个辅助类来让我们可以从 Django 的模型创建 Form，这就是 ModelForm。

### 最简版本的 ModelForm 类

如果不需要额外指定属性，不需要额外校验字段数据，不需要指定要显示的数据，也就是把 Model 中的数据全部显示出来，不需要指定别名，或者在 Model 中已经指定了 verbose_name……那么你只需要 5 行代码就可以完成一个最精简的 ModelForm 类：

```python
from . import models
class BookModelForm(forms.ModelForm):
    class Meta:
        model = models.Book
        fields = "__all__"
```

### 集合常用功能的 ModelForm 类

当然，我们通常不会直接使用最简版本的 ModelForm 类。那种写法虽然简单，但是功能有限。我们很多时候都要针对需求，对表单数据进行一些处理。这时候，我们就需要使用一些方法来实现。

这里的例子是一个集合了各种常用功能的 ModelForm 类，能应对 90% 以上的需求。用的时候，只需要替换上自己数据库模型的字段，然后把没有用的功能删掉即可。

```python
from django import forms
from django.core.validators import RegexValidator    # 正则校验器
from . import models

def six_validate(value):
    if '666' in value:
        raise ValidationError('不许喊 6')

# 在视图函数中，定义一个类，比如就叫BookModelForm，这个类要继承ModelForm，在这个类中再写一个原类Meta（规定写法，并注意首字母是大写的）
class BookModelForm(forms.ModelForm):
    # 自己定义的属性优先级高,会覆盖modelform翻译出来的属性
    title = forms.CharField(
        label='书籍名称',
        min_length=1,
        max_length=32,
        validators=[six_validate, RegexValidator(r'^a', '不能以 a 开头')],
        widget=forms.TextInput(attrs={'class': 'form-control'})
        error_messages={
            'required': '不能为空',
            'min_length': '太短了，你也好意思!',
        }
    )
    class Meta:
        model = models.Book   # 对应的Model中的类
        fields = ['title', 'authors', 'price', 'publish_date', 'publishs']    # 字段，如果是'__all__'，就是表示列出所有的字段
        exclude = None    # 排除的字段，None为没有，若有，仿照fields写成列表
        # error_messages 用法：
        labels = {
            'title': '书名',
            'price': '价格',
            'publish_date': '出版日期',
            'publishs': '出版社',
            'authors': '作者',
        }
        error_messages = {
            'title': {
                'required': '书名不能为空',
                'max_length': '太长了',
            },
            'price': {
                'required': '价格不能为空',
            }
        }
        # widgets用法，比如把输入用户名的input框给为Textarea
        widgets = {
        "press": forms.Textarea(attrs={"class": "c1"})    # 还可以自定义属性
        }
        # labels，自定义在前端显示的名字，可以在model模型中用verbose_name指定
        labels= {
        "name": "用户名",
        }
    # 批量增加样式
    def __init__(self,*args,**kwargs):    # 这里可以在实例化时传参进来
        super().__init__(*args,**kwargs)
        for field in self.fields.values():
             ield.widget.attrs.update({'class':'form-control xx oo'})
	# init方法和局部钩子还有全局钩子写法和form一模一样
```

### class Meta 下常用参数

```python
model = models.Book    # 对应的Model中的类
fields = "__all__"    # 字段，如果是__all__,就是表示列出所有的字段，列表
exclude = None    # 排除的字段，列表
labels = None    # 提示信息，字典，键是字段名，值是提示的信息
help_texts = None    # 帮助提示信息，字典，键值同上
widgets = None    # 自定义插件，字典，例如：{"press": forms.Textarea(attrs={"class": "c1"})}
error_messages = None    # 自定义错误信息，字典套字典，如下所示：
error_messages = {
    'title': {'required':'不能为空'}    # 每个字段的所有的错误都可以写
}
```

### 经典增改查视图

views 中，使用下面的代码即可实现对于 Book 的增、改、查功能：

```python
class BookAddEditView(View):

    def get(self,request,book_id):
        old_book_obj = models.Book.objects.get(id=book_id)
        form_obj = myforms.BookModelForm(instance=old_book_obj)
        return render(request,'book_edit.html',{'form_obj':form_obj})

    def post(self,request,book_id):
        old_book_obj = models.Book.objects.filter(id=book_id).first
        form_obj = myforms.BookModelForm(request.POST,instance=old_book_obj)
        if form_obj.is_valid():
            ret = form_obj.save()    # 不加instance --create
            					     # 加上instance关键字,就是update操作
            print('>>>',ret)
            return redirect('book_list')
        else:
            return render(request, 'book_edit.html', {'form_obj': form_obj})
```

### html 经典写法

```html
<form action="" method="post" novalidate>
    {% csrf_token %}
    {% for field in form_obj %}
    <div class="form-group {% if field.errors.0 %}has-error{% endif %}">
        <label for="{{ field.id_for_label }}">{{ field.label }}</label>
        {{ field }}
        <span class="help-block">{{ field.errors.0 }}</span>
    </div>
    {% endfor %}
    <button type="submit" class="btn btn-success pull-right">提交</button>
</form>
```