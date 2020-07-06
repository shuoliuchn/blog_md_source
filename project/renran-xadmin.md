# Xadmin 的安装、配置和使用

Xadmin 是 Django 的第三方扩展，可以使 Django 的 admin 站点使用更方便，功能更强大。

文档：https://xadmin.readthedocs.io/en/latest/index.html

## 安装

通过如下命令安装 Xadmin 的最新版：

```shell
pip install https://codeload.github.com/sshwsfc/xadmin/zip/django2
```

在配置文件中注册如下应用，注意三个都要加上：

```python
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
# 把apps目录设置环境变量中的导包路径
sys.path.append( os.path.join(BASE_DIR,"apps") )


INSTALLED_APPS = [
    ...
    'xadmin',
    'crispy_forms',
    'reversion',
    ...
]

# 修改使用中文界面
LANGUAGE_CODE = 'zh-Hans'

# 修改时区
TIME_ZONE = 'Asia/Shanghai'
```

Xadmin 有建立自己的数据库模型类，所以需要进行数据库迁移

```shell
python manage.py makemigrations
python manage.py migrate
```

在 renranapi 项目根目录下的总路由中添加 Xadmin 的路由信息

```python
import xadmin
xadmin.autodiscover()

# xversion模块自动注册需要版本控制的 Model
from xadmin.plugins import xversion
xversion.register_models()

urlpatterns = [
    path(r'xadmin/', xadmin.site.urls),
]
```

创建超级用户

```python
python manage.py createsuperuser
```

浏览器访问 http://api.renran.cn:8000/xadmin/ 即可

## 使用

- Xadmin 不再使用 Django 的 `admin.py`，而是需要编写代码在 `adminx.py` 文件中。
- Xadmin 的站点管理类不用继承`admin.ModelAdmin`，而是直接继承 `object` 即可。

例如：在子应用中创建 `adminx.py` 文件一定要注意，x 在最后。

#### 站点的全局配置

```python
import xadmin
from xadmin import views

class BaseSetting(object):
    """xadmin的基本配置"""
    enable_themes = True  # 开启主题切换功能
    use_bootswatch = True

xadmin.site.register(views.BaseAdminView, BaseSetting)

class GlobalSettings(object):
    """xadmin的全局配置"""
    site_title = "荏苒网"  # 设置站点标题
    site_footer = "荏苒网络有限公司"  # 设置站点的页脚
    menu_style = "accordion"  # 设置菜单折叠

xadmin.site.register(views.CommAdminView, GlobalSettings)
```



#### 站点 Model 管理

xadmin可以使用的页面样式控制基本与Django原生的admin一直。

- list_display 控制列表展示的字段

  ```
  list_display = ['id', 'btitle', 'bread', 'bcomment']
  ```

- search_fields 控制可以通过搜索框搜索的字段名称，xadmin使用的是模糊查询

  ```
  search_fields = ['id','btitle']
  ```

- list_filter 可以进行过滤操作的列，对于分类、性别、状态

  ```
  list_filter = ['is_delete']
  ```

- ordering 默认排序的字段

- readonly_fields 在编辑页面的只读字段

- exclude 在编辑页面隐藏的字段

- list_editable 在列表页可以快速直接编辑的字段

- show_detail_fields 在列表页提供快速显示详情信息

- refresh_times 指定列表页的定时刷新

  ```
  refresh_times = [5, 10,30,60]  # 设置允许后端管理人员按多长时间(秒)刷新页面
  ```

- list_export 控制列表页导出数据的可选格式

  ```
  list_export = ('xls', 'xml', 'json')   list_export设置为None来禁用数据导出功能
  list_export_fields = ('id', 'title', 'pub_date') # 允许导出的字段
  ```

- show_bookmarks 控制是否显示书签功能

  ```
  show_bookmarks = True
  ```

- data_charts 控制显示图表的样式

  ```python
  data_charts = {
          "order_amount": {
            'title': '图书发布日期表', 
            "x-field": "bpub_date", 
            "y-field": ('btitle',),
            "order": ('id',)
          },
      #    支持生成多个不同的图表
      #    "order_amount": {
      #      'title': '图书发布日期表', 
      #      "x-field": "bpub_date", 
      #      "y-field": ('btitle',),
      #      "order": ('id',)
      #    },
      }
  ```

  - title 控制图标名称
  - x-field 控制x轴字段
  - y-field 控制y轴字段，可以是多个值
  - order 控制默认排序



- model_icon 控制菜单的图标

  ```
  class BookInfoAdmin(object):
      model_icon = 'fa fa-gift'
  
  xadmin.site.register(models.BookInfo, BookInfodmin)
  ```

修改 admin 或者 xadmin 站点下的子应用成中文内容。

```python
# 在子应用的apps下面的配置中，新增一个属性verbose_name
from django.apps import AppConfig

class StudentsConfig(AppConfig):
    name = 'students'
    verbose_name = "学生管理"


# 然后在子应用的__init__.py里面新增一下代码：
default_app_config = "students.apps.StudentsConfig"
```

