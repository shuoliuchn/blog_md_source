## Django 的表单 form 组件

### form 组件使用方法

form 组件主要有三个用途：

1. 生成页面可用的 HTML 标签
2. 对用户提交的数据进行校验
3. 保留上次输入内容

使用步骤：

1. 在应用文件夹下创建一个文件，名字随意，比如 `myforms.py`。

2. 创建 form 类，继承自 `django.forms.Form`：

   ```python
   from django import forms
   
   class LoginForm(forms.Form):
       # username = request.POST.get('username')
       # if '--' in username:
       #     raise
       # {'username':'xxxx','password':''}
       username = forms.CharField(
           label='用户名:',
           required=True,    # 不能为空，默认可不写
           max_length=7,    # 长度不能超过7个字符
           min_length=2,    # 最短不能低于2个字符
           # initial='张三', #初始值
           widget=forms.TextInput(attrs={'class':'c1','placeholder':'请输入用户名'}),
           error_messages={
               'required':'不能为空',
               'max_length':'太长了,难受!',
               'min_length':'太短了,更难受!',
           },
       )
       password = forms.CharField(
           required=True,
           label='密码:',
           widget=forms.PasswordInput(attrs={'class':'c1','placeholder':'请输入密码'},render_value=True), # render_value=True让密码输入的数据保留
       )
       sex = forms.ChoiceField(
           choices=[(1, '男'),(2, '女')],
           widget=forms.RadioSelect(attrs={'xx':'none'}),
       )
   ```

3. 在 views 中实例化这个类对象，并交给前端 html 页面：

   ```python
   def login(request):
       if request.method == 'GET':
           form_obj = LoginForm()
   
           return render(request,'login.html', {'form_obj':form_obj})
       else:
           form_obj = LoginForm(request.POST)
           # 准备校验，也就是检查不符合格式的部分，并且保存到列表中，写成伪代码为：
           # form_obj
           # username:alexxxxxx -- form_obj.username.errors.append('太长了!!')
           # username:alexxxxxx -- form_obj.username.errors.append('包含了--!feifa zifu')
           # password  form_obj.password.errors.append('太长了!!')
           print(status)
           return render(request,'login.html', {'form_obj':form_obj})
   ```

4. 进行数据格式校验：

   ```python
   form_obj = LoginForm(request.POST)
   status = form_obj.is_valid() # 开始校验，第三步的伪代码Django已经封装好，直接使用即可
   print(status)
   
   return render(request,'login.html', {'form_obj':form_obj})
   ```

5. 在前端页面中可以写成这样使用表单信息：

   ```html
   <form action="" method="post" novalidate>
       {% csrf_token %}
       <div>
           <label for="{{ form_obj.username.id_for_label }}">{{ form_obj.username.label }}</label>
           {{ form_obj.username }}
           <span>{{ form_obj.username.errors.0 }}</span>
       </div>
       <div>
           <label for="{{ form_obj.password.id_for_label }}">{{ form_obj.password.label }}</label>
           {{ form_obj.password }}
           <span>{{ form_obj.password.errors.0 }}</span>
       </div>
       <input type="submit">
       
       <div>
           <label for="{{ form_obj.sex.id_for_label }}">{{ form_obj.sex.label }}</label>
           {{ form_obj.sex }}
      </div>
   </form>
   ```

### 常用字段

#### CharField 普通文本

不要被名字骗到，后面的密码输入，还有其他各种输入框，基本都是在这个 CharField 的基础上通过插件来搞的。

不过 CharFIield 最原本的用途，还是用来输入普通文本：

```python
username = forms.CharField(    # 也可写成forms.field.CharField而这等价，后面的字段也一样
    label='用户名:',
    initial="张三"    # 设置默认值
    required=True,    # 不能为空，默认可不写
    max_length=7,    # 长度不能超过7个字符
    min_length=2,    # 最短不能低于2个字符
    # initial='张三', #初始值
    widget=forms.TextInput(attrs={'class':'c1','placeholder':'请输入用户名'}),
    error_messages={
        'required':'不能为空',
        'max_length':'太长了,难受!',
        'min_length':'太短了,更难受!',
    },
```

#### 密码输入

form 中是没有密码输入的字段的，需要配合 CharField 来写，在插件中设置为密码输入。密码字段和其他字段不一样，默认在前端输入数据错误的时候，点击提交之后，不保存的原来数据。可以通过设置 `render_value=True` 让这个字段在前端保留用户输入的数据：

```python
password = forms.CharField(
    required=True,
    min_length=6,
    label='密码:',
    widget=forms.PasswordInput(attrs={'class':'c1','placeholder':'请输入密码'},render_value=True), # render_value=True让密码输入的数据保留
)
```

#### radioSelect 单选

注意这个不是 CharField，而是 ChoiceField：

```python
sex = forms.ChoiceField(
    choices=[(1, '男'),(2, '女')],
    widget=forms.RadioSelect(attrs={'xx':'none'}),
)
```

#### 单选 Select 下拉框

注意，单选框用的是 ChoiceField，并且里面的插件是 Select，不然验证的时候会报 `Select a valid choice` 的错误。

```python
hobby = forms.ChoiceField(
    choices=((1, "篮球"), (2, "足球"), (3, "双色球"), ),
    label="爱好",
    initial=3,
    widget=forms.widgets.Select()
)
```

#### 多选 Select 下拉框

多选框的时候用 MultipleChoiceField，并且里面的插件用的是 SelectMultiple，不然验证的时候会报错。

```python
hobby = forms.MultipleChoiceField(
    choices=((1, "篮球"), (2, "足球"), (3, "双色球"), ),
    label="爱好",
    initial=[1, 3],
    widget=forms.widgets.SelectMultiple()
)
```

#### 单选 checkbox

```python
keep = forms.ChoiceField(
    label="是否记住密码",
    initial="checked",
    widget=forms.widgets.CheckboxInput()
)
```

#### 多选 checkbox

```python
hobby = forms.MultipleChoiceField(
    choices=((1, "篮球"), (2, "足球"), (3, "双色球"),),
    label="爱好",
    initial=[1, 3],
    widget=forms.widgets.CheckboxSelectMultiple()
)
```

#### 时间输入 date

必须指定 type，不然不能渲染成选择时间的 input 框：

```python
date = forms.DateField(widget=forms.widgets.TextInput(attrs={'type': 'date'}))
```

#### 从数据库中动态载入 choice 字段

在使用选择标签时，需要 choices 的选项配置往往要从数据库中获取。但是如果按照往常一样静态字段，写死了，获取的值无法实时更新。这时就需要重写构造方法从而实现 choice 实时更新。

方式一，从数据库中查找，将结果作为 choices 的值传入：

```python
from django.forms import Form
from django.forms import widgets
from django.forms import fields

 
class MyForm(Form):
 
    user = fields.ChoiceField(
        # choices=((1, '上海'), (2, '北京'),),
        initial=2,
        widget=widgets.Select
    )
 
    def __init__(self, *args, **kwargs):
        super(MyForm, self).__init__(*args, **kwargs)   # 注意重写init方法的时候，*args和**kwargs一定要给人家写上，不然会出问题，并且验证总是不能通过，还不显示报错信息
        # self.fields['user'].choices = ((1, '上海'), (2, '北京'),)
        # 或
        self.fields['user'].choices = models.Classes.objects.all().values_list('id','caption')
```

方法二，将选项作为模型对象，整个传入：

如果用这种方式，别忘了 model 模型表中，要写上 NNEWType 的 `__str__` 方法，不然选择框里面将会是一个个的 object 对象。

```python
from django import forms
from django.forms import fields

class FInfo(forms.Form):

    authors = forms.ModelMultipleChoiceField(queryset=models.NNewType.objects.all())  # 多选
    #或者下面这种方式，通过forms里面的models中提供的方法也是一样的。
    authors = forms.models.ModelMultipleChoiceField(queryset=models.NNewType.objects.all())  # 多选
    authors = forms.models.ModelChoiceField(queryset=models.NNewType.objects.all())  # 单选

    #或者
    authors = forms.ModelChoiceField(queryset=models.Publisth.objects.all(),widget=forms.Select()) # 单选
    authors = forms.ModelMultipleChoiceField(
        queryset=models.Author.objects.all(),
        widget = forms.Select(attrs={'class': 'form-control'}
                                     ))
```

### form 所有的内置字段

注：有些字段因不常用，其属性以 `...` 表示暂不介绍。

```python
Field
    required=True,               是否允许为空
    widget=None,                 HTML插件
    label=None,                  用于生成Label标签或显示内容
    initial=None,                初始值
    help_text='',                帮助信息(在标签旁边显示)
    error_messages=None,         错误信息 {'required': '不能为空', 'invalid': '格式错误'}
    validators=[],               自定义验证规则
    localize=False,              是否支持本地化
    disabled=False,              是否可以编辑
    label_suffix=None            Label内容后缀
 
 
CharField(Field)
    max_length=None,             最大长度
    min_length=None,             最小长度
    strip=True                   是否移除用户输入空白
 
IntegerField(Field)
    max_value=None,              最大值
    min_value=None,              最小值
 
FloatField(IntegerField)
    ...
 
DecimalField(IntegerField)
    max_value=None,              最大值
    min_value=None,              最小值
    max_digits=None,             总长度
    decimal_places=None,         小数位长度
 
BaseTemporalField(Field)
    input_formats=None          时间格式化   
 
DateField(BaseTemporalField)    格式：2015-09-01
TimeField(BaseTemporalField)    格式：11:12
DateTimeField(BaseTemporalField)格式：2015-09-01 11:12
 
DurationField(Field)            时间间隔：%d %H:%M:%S.%f
    ...
 
RegexField(CharField)
    regex,                      自定制正则表达式
    max_length=None,            最大长度
    min_length=None,            最小长度
    error_message=None,         忽略，错误信息使用 error_messages={'invalid': '...'}
 
EmailField(CharField)
    ...
 
FileField(Field)
    allow_empty_file=False     是否允许空文件
 
ImageField(FileField)
    ...
    注：需要PIL模块，pip3 install Pillow
    以上两个字典使用时，需要注意两点：
        - form表单中 enctype="multipart/form-data"
        - view函数中 obj = MyForm(request.POST, request.FILES)
 
URLField(Field)
    ...

BooleanField(Field)
    ...
 
NullBooleanField(BooleanField)
    ...
 
ChoiceField(Field)
    ...
    choices=(),                选项，如：choices = ((0,'上海'),(1,'北京'),)
    required=True,             是否必填
    widget=None,               插件，默认select插件
    label=None,                Label内容
    initial=None,              初始值
    help_text='',              帮助提示
 
 
ModelChoiceField(ChoiceField)
    ...                        django.forms.models.ModelChoiceField
    queryset,                  # 查询数据库中的数据
    empty_label="---------",   # 默认空显示内容
    to_field_name=None,        # HTML中value的值对应的字段
    limit_choices_to=None      # ModelForm中对queryset二次筛选
     
ModelMultipleChoiceField(ModelChoiceField)
    ...                        django.forms.models.ModelMultipleChoiceField

TypedChoiceField(ChoiceField)
    coerce = lambda val: val   对选中的值进行一次转换
    empty_value= ''            空值的默认值

MultipleChoiceField(ChoiceField)
    ...

TypedMultipleChoiceField(MultipleChoiceField)
    coerce = lambda val: val   对选中的每一个值进行一次转换
    empty_value= ''            空值的默认值

ComboField(Field)
    fields=()                  使用多个验证，如下：即验证最大长度20，又验证邮箱格式
                               fields.ComboField(fields=[fields.CharField(max_length=20), fields.EmailField(),])
 
MultiValueField(Field)
    PS: 抽象类，子类中可以实现聚合多个字典去匹配一个值，要配合MultiWidget使用
 
SplitDateTimeField(MultiValueField)
    input_date_formats=None,   格式列表：['%Y--%m--%d', '%m%d/%Y', '%m/%d/%y']
    input_time_formats=None    格式列表：['%H:%M:%S', '%H:%M:%S.%f', '%H:%M']

FilePathField(ChoiceField)     文件选项，目录下文件显示在页面中
    path,                      文件夹路径
    match=None,                正则匹配
    recursive=False,           递归下面的文件夹
    allow_files=True,          允许文件
    allow_folders=False,       允许文件夹
    required=True,
    widget=None,
    label=None,
    initial=None,
    help_text='',

GenericIPAddressField
    protocol='both',           both,ipv4,ipv6支持的IP格式
    unpack_ipv4=False          解析ipv4地址，如果是::ffff:192.0.2.1时候，可解析为192.0.2.1， PS：protocol必须为both才能启用
 
SlugField(CharField)           数字，字母，下划线，减号（连字符）
    ...

UUIDField(CharField)           uuid类型
```

### 数据校验

我们可以字段中的参数对用户输入的数据进行初步校验。但有些时候，我们需要更复杂的校验模式，就需要自定义校验规则。

数据校验流程：

1. 循环所有的自己 form 类中的所有字段
2. 进行字段中 `charfield()` 类实例化时传入的校验规则（包括 validators 的规则）进行校验，然后进行该字段局部钩子的校验。然后进行下一次循环，也就是校验下一个字段。完成之后，`self.cleaned_data` 里面有了每个字段的数据
3. 执行全局钩子

#### validators

通过创建的校验函数，在声明 form 字段时使用 validators 属性引入。其基本用法为：

```python
import re
from django import forms
from django.core.exceptions import ValidationError
from django.core.validators import RegexValidator    # 正则校验器

# 这个函数的作用等价于：RegexValidator(r'^(13[0-9]|15[012356789])[0-9]{8}$', '手机号码格式错误')
def mobile_validate(value):
    mobile_re = re.compile(r'^(13[0-9]|15[012356789])[0-9]{8}$')
    if not mobile_re.match(value):
        raise ValidationError('手机号码格式错误')

class LoginForm(forms.Form):
    # 野蛮校验法：
    # username = request.POST.get('username')
    # if '--' in username:
    #     raise
    # {'username':'xxxx','password':''}
    phone = forms.CharField(
        label='用户名:',
        required=True,    # 不能为空
        min_length=11,    # 最短不能低于11个字符
        max_length=11,
        # 应用validator，注意是个列表
        validators=[mobile_validate, RegexValidator(r'.*--.*', '请输入正确手机号')],
        widget=forms.TextInput(attrs={'class': 'c1', 'placeholder': '请输入手机号'}),
        error_messages={
            'required':'不能为空',
            'max_length':'太长了！',
            'min_length':'太短了！',
        },
    )
```

#### 局部钩子

局部钩子用来校验特定的某个字段，之前的校验规则还在，给你提供了一个添加一些校验功能的钩子。其基本用法为：

```python
import re
from django import forms
from django.core.exceptions import ValidationError
from django.core.validators import RegexValidator


class LoginForm(forms.Form):
    username = forms.CharField(
        label='用户名:',
        required=True,
        min_length=2,
        widget=forms.TextInput(attrs={'class':'c1','placeholder':'请输入用户名'}),
        error_messages={
            'required':'不能为空',
            'max_length':'太长了,难受!',
            'min_length':'太短了,更难受!',
        },
    )
    
    def clean_username(self):
        value = self.cleaned_data.get('username')
        print(value,type(value))
        if '666' in value:  # 不能含有666
            raise ValidationError('光喊6是没用的!')
        else:
            return value
```

#### 全局钩子

如果我们要校验两个或多个有关联的数据，就需要用到全局钩子，待其他校验操作完成后，进行数据校验。

我们在Fom类中定义 `clean()` 方法，就能够实现对字段进行全局校验。字段全部验证完，局部钩子也全部执行完之后，执行这个全局钩子校验：

```python
from django.shortcuts import render
import re
from django.core.exceptions import ValidationError
from django.core.validators import RegexValidator
from django import forms

def mobile_validate(value):
    mobile_re = re.compile(r'^(13[0-9]|15[012356789])[0-9]{8}$')
    if not mobile_re.match(value):
        raise ValidationError('手机号码格式错误')

class LoginForm(forms.Form):

    password = forms.CharField(
        required=True,
        label='密码:',
        widget=forms.PasswordInput(attrs={'class': 'c1', 'placeholder': '请输入密码'}, render_value=True), # render_value=True让密码输入的数据保留

    )

    confirm_password = forms.CharField(
        required=True,
        label='确认密码:',
        widget=forms.PasswordInput(attrs={'class': 'c1', 'placeholder': '请输入密码'}, render_value=True), # render_value=True让密码输入的数据保留
    )

    # 全局钩子，用来比较密码与验证秘法是否一致
    def clean(self):
        p1 = self.cleaned_data.get('password')
        p2 = self.cleaned_data.get('confirm_password')
        if p1 == p2:
            return self.cleaned_data

        else:
            # raise ValidationError('两次密码不一致!!!你是熊华吗!!!')    # 全局错误，cleaned_data数据不变
            self.add_error('confirm_password','两次密码不一致,你是彭于晏吗!!!!')    # 指定字段错误，cleaned_data中不再有confirm_password
```

#### cleaned_data 和 errors 的简单讨论

经过验证，用户输入的数据将会分成两部分：合乎规则的和不合规则的。每次校验后，合乎规则的数据将会放在 cleaned_data 中，不合规则的数据会放在 errors 中。下一步的校验，将只校验 cleaned_data 中的数据。

cleaned_data 是完成前一步校验后的合格数据，是一个字典，键是每一个字段名，值是合格的数据，追根溯源是用户输入进来的。操作 cleaned_data 的方式也和操作字典相同（废话，人家本身就是字典）。

cleaned_data 是类属性，通过 `self.cleaned_data` 查找和使用。

errors 校验不合格的数据会放在 errors 里面，储存形式为字典：

```python
{'字段名': ['报错信息'], '__all__': ['全部报错信息']}
```

字段的属性规则和局部钩子产生的错误，通过捕获 ValidationError 获取。捕获到这个错误后，会将出错的字段从 cleaned_data 字典中移除，将其存放到 errors 字典中。用户输入的数据因为不合规范，所以不会保存，取而代之的是设置的报错信息。

如果是全局钩子报错，也是抛出的 ValidationError。如果没有进行其他操作，不会删除 cleaned_data 中的字段，而是会在 errors 字典中创建一个 `__all__` 关键字，将全局钩子的错误信息放到其中。在抛出异常之前，我们可以调用 self.add_error 方法，将错误指定给某个字段。调用这个方法会将该字段从 cleaned_data 中移除，并添加到 errors 字典中，值将会是报错信息。当然，如果后面还有抛出 ValidationError 的话，还是会在 errors 字典中加上 `__all__` 关键字放入全局报错信息的。所以在全局钩子中，两种方式没有必要同时使用。

#### 数据校验综合案例

```python
from django.shortcuts import render
import re
from django.core.exceptions import ValidationError
from django.core.validators import RegexValidator
from django import forms

def mobile_validate(value):
    mobile_re = re.compile(r'^(13[0-9]|15[012356789])[0-9]{8}$')
    if not mobile_re.match(value):
        raise ValidationError('手机号码格式错误')

class LoginForm(forms.Form):
    username = forms.CharField(
        label='用户名:',
        required=True,
        min_length=2,
        # validators=[mobile_validate,],
        widget=forms.TextInput(attrs={'class': 'c1', 'placeholder': '请输入用户名'}),
        error_messages={
            'required':'不能为空',
            'max_length':'太长了,难受!',
            'min_length':'太短了,更难受!',
        },
    )

    password = forms.CharField(
        required=True,
        label='密码:',
        widget=forms.PasswordInput(attrs={'class': 'c1', 'placeholder': '请输入密码'}, render_value=True),    # render_value=True让密码输入的数据保留

    )

    confirm_password = forms.CharField(
        required=True,
        label='确认密码:',
        widget=forms.PasswordInput(attrs={'class': 'c1', 'placeholder': '请输入密码'}, render_value=True),

    )

    # 局部钩子
    def clean_username(self):

        value = self.cleaned_data.get('username')
        print(value,type(value))
        if '666' in value:  # 不能含有666
            raise ValidationError('光喊6是没用的!')
        else:
            return value

    # 全局钩子
    def clean(self):
        p1 = self.cleaned_data.get('password')
        p2 = self.cleaned_data.get('confirm_password')
        if p1 == p2:
            return self.cleaned_data
        else:
            # raise ValidationError('两次密码不一致!!!你是熊华吗!!!')
            self.add_error('confirm_password', '两次密码不一致,你是彭于晏吗!!!!')
```

### HTML 写法

#### form_obj

直接在模板渲染语法中写 form_obj 可以直接以表格的形式生成所有的表单标签，但因为格式不易操作，所以几乎不这么直接用：

```jinja2
{{ form_obj }}
```

#### as_p

自动生成所有带 label 标签的 input 标签，也跟上面一样，不常用。用法：

```jinja2
{{ form_obj.as_p }}
```

#### 字段名

生成字段名对应的 input 标签：

```jinja2
{{ form_obj.字段名 }}
```

#### label

生成字段名标签，也就是字段中的 label 属性对应的值，若不写 label 属性，默认是字段名：

```jinja2
{{ form_obj.字段名.label }}
```

#### id_for_label

生成的 input 标签的 id，用来给 label 分配 input 标签：

```html
<label for="{{ form_obj.字段名.id_for_label }}">{{ form_obj.字段名.label }}</label>
```

#### errors

errors 是生成错误的集合，本质上是字典，但是重写了 `__str__` 方法，在页面上会把所有错误以无序列表的形式显现，但我们一般不会把错误这么打印出来，我们一般会针对每个字段打印：

``` jinja2
{{ form_obj.errors }}    <!-- 输出所有错误出来 -->
{{ form_obj.字段名.errors }}    <!-- 输出指定字段的错误，是个列表 -->
{{ form_obj.字段名.errors.0 }}    <!-- 实际上我们常这样，只拿第一个错误，没必要全拿出来 -->
```

#### 综合使用

```html
<div>
    <label for="{{ form_obj.username.id_for_label }}">{{ form_obj.username.label }}</label>
    {{ form_obj.username }}
    <span>{{ form_obj.username.errors.0 }}</span>
</div>
```

### 批量增加样式（比如 Bootstrap 样式）

```python
class LoginForm(forms.Form):
    username = forms.CharField(
        min_length=8,
        label="用户名",
        initial="张三",
        error_messages={
            "required": "不能为空",
            "invalid": "格式错误",
            "min_length": "用户名最短8位"
        }

    def __init__(self, *args, **kwargs):
        super(LoginForm, self).__init__(*args, **kwargs)
        for field in iter(self.fields):
            self.fields[field].widget.attrs.update({
                'class': 'form-control',    # Bootstrap 表单样式
            })
```