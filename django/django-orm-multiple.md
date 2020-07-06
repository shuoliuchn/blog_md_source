# Django ORM 多表增删改查

[TOC]

## 创建关系字段

ORM 通过使用字段的方式创建多表关系。常用的多表关系有：

- ForeignKey 外键，一对多关系字段
- OneToOneField 一对一关系字段
- ManyToManyField 多对多关系字段

直接看 `models.py` 代码：

```python
class Author(models.Model):
    name=models.CharField(max_length=32)
    age=models.IntegerField()
    # 一对一
    ad=models.OneToOneField(to="AuthorDetail", to_field="id", on_delete=models.CASCADE)
    
    # to_field="id"  可以不写,默认找主键
    # to="AuthorDetail",  to=可以不用写
    # on_delete=models.CASCADE  默认是级联删除,想做级联更新,直接去数据库修改表结构
    # on_delete=models.SET_NULL  也可以设置成删除置空

class AuthorDetail(models.Model):
    birthday=models.DateField()
    telephone=models.CharField(max_length=16)
    addr=models.CharField(max_length=64)


class Publish(models.Model):
    name=models.CharField(max_length=32)
    city=models.CharField(max_length=32)

class Book(models.Model):
    title = models.CharField(max_length=32)
    publishDate=models.DateField()
    price=models.DecimalField(max_digits=5, decimal_places=2)
    # 外键和多对多
    publishs=models.ForeignKey(to="Publish", to_field="id", on_delete=models.CASCADE)
    authors=models.ManyToManyField(to='Author')
```

基本的建表语句与单表操作一致。多个表关系有一对一、一对多和多对多，分别可以用下面这三种方式创建：

```python
# 一对一，外键 + 唯一
ad=models.OneToOneField(to="AuthorDetail", to_field="id", on_delete=models.CASCADE)

# 一对多，外键
publishs=models.ForeignKey(to="Publish", to_field="id", on_delete=models.CASCADE)

# 多对多，创建第三张表
authors=models.ManyToManyField(to='Author')
```

操作一对一或一对多的关联表的时候，我们可以通过类属性或者表字段的形式来进行操作，字段名通常是 `类属性名 + 下划线 + 外键名`：

- 类属性：`ad`、`publishs`
- 表字段：`ad_id`、`publishs_id`

对于多对多的关系，可以直接操作新生成的第三张表，表名为 `应用名 + 下划线 + 当前表名 + 下划线 + 属性名` ，也可以通过类属性和表字段来操作第三张表：

- 类属性：`authors`
- 表名：`app01_book_authors`
- 表字段:   `id`、`author_id`、`book_id`

其具体用法会在下面详细讨论。

## 多表增删改查

### 增加

#### 一对一

如前所述，一对一增加数据可以通过使用类属性或表字段的方式添加与另一个表相关联的字段：

```python 
models.AuthorDetail.objects.create(
    birthday='2018-11-11',
    telephone='15122220000',
    addr='北京',
)

models.Author.objects.create(
    name='金龙',
    age=2,
    ad=models.AuthorDetail.objects.get(id=1),    # 类属性
)

models.Author.objects.create(
    name='金龙2',
    age=2,
    ad_id=2,    # 表字段
)
```

#### 一对多

增加的话，一对多跟一对一的道理是一样的，可以通过类属性或表字段添加：

```python
models.Book.objects.create(
    title='白洁',
    publishDate='2011-01-01',
    price=200,
    publishs=models.Publish.objects.get(id=1)    # 类属性
)

models.Book.objects.create(
    title='白洁第二部',
    publishDate='2011-01-01',
    price=300,
    publishs_id=1    # 表字段
)
```

#### 多对多

一本书可能会有多个作者，一个作者也可能写很多本书。比如，编号为 1 的书是编号为 1 和 2 的两个作者所著，那么在作者与书籍的关系表中，我们就应该创建书1对作者 1 和书 1 对作者 2 两条关系，就像这样：

```bash
+----+---------+-----------+
| id | book_id | author_id |
+----+---------+-----------+
|  1 |       1 |         1 |
|  2 |       1 |         2 |
+----+---------+-----------+
```

具体的操作用两种方式，一种是通过对象添加，另一种是通过值直接添加

##### 通过 object 对象添加

通过找到作者的 object 对象，使用 add 方法添加：

```python
book_obj = models.Book.objects.get(id=1)
author1 = models.Author.objects.get(id=1)
author2 = models.Author.objects.get(id=2)
book_obj.authors.add(author1, author2)
# book_obj.authors.add(*[author1, author2])
```

##### 通过主键 id 添加

通过作者 id，直接添加书籍的作者信息：

```python
book_obj = models.Book.objects.get(id=1)
book_obj.authors.add(1, 2)
# book_obj.authors.add(*[1, 2])
```

### 删除

#### 一对一和一对多

一对一和一对多的删除都是找到 object 对象之后，直接 delete 删除：

```python
models.Author.objects.get(id=1).delete()
```

#### 多对多

多对多的删除需要使用 query 对象进行操作：

```python
book_obj = models.Book.objects.get(id=1)    # 找到id为1的书
book_obj.authors.remove(3)  #删除id为1的书与id为3的作者之间的关系
book_obj.authors.remove(2, 3) # 删除id为1的书与id为2和3的作者之间的关系
book_obj.authors.clear()  # 清空id为1的书的所有作者信息 
```

### 修改

#### 一对一和一对多

修改 update，一对多和一对一操作时和单表的是一样的：

```python
models.Book.objects.filter(id=1).update(
    title='白洁新版',
    # publishs=models.Publish.objects.get(id=3),
    publishs_id=3
)
```

#### 多对多

```python
book_obj.authors.set(['4',])  # 先清空再添加 clear+add  
```

### 查询

#### 基于对象的跨表查询

##### 一对一

###### 正向查询

查询金龙 2 这个作者的家庭住址

```python
author_obj = models.Author.objects.get(name='金龙2')
# author_obj.ad -- 直接拿到authordetail表中的那个条记录对象
print(author_obj.ad.addr)
```

###### 反向查询

查询电话号码以 151 开头的那个作者的姓名

```python
authordetail_obj = models.AuthorDetail.objects.filter(telephone__startswith='151').first()
print(authordetail_obj.author.name)
```

##### 一对多

###### 正向查询

白洁新版这本书是哪个出版社出版的

```python
book_obj = models.Book.objects.filter(title='白洁新版').first()
print(book_obj.publishs.name)
```

###### 反向查询

东京出版社出版了哪些书

```python
pub_obj = models.Publish.objects.get(name='东京出版社')
books = pub_obj.book_set.all().values('title')
# 东京出版社出版的名称中还有白洁的那些书
books = pub_obj.book_set.filter(title__contains='白洁').values('title')
print(books)
```

##### 多对多

###### 正向查询

白洁新版这本书的作者都有谁

```python
book_obj = models.Book.objects.filter(title='白洁新版').first()
authors = book_obj.authors.all().values('name')
print(authors)
```

###### 反向查询

查询一下金龙 2 写了哪些书

```python
jinlong2_obj = models.Author.objects.get(name='金龙2')
ret = jinlong2_obj.book_set.all().values('title')
print(ret)
```

##### 跨表查询总结

- 正向查询靠属性
- 如果是一对一，反向查询靠小写类名
- 一对多和多对多，反向查询靠小写类名_set

## 基于双下划线的跨表查询（join）

### 一对一

#### 正向联表靠属性

查询旭东的家庭住址

```python
ret = models.Author.objects.filter(name='旭东').values('ad__addr')
print(ret)
```

如果用 SQL 语句表示，应该是这个样子的：

```mysql
select app01_authordetail.addr from app01_author inner join app01_authordetail on app01_author.ad_id = app01_authordetail.id where app01_author.name='旭东';
```

#### 反向连表类名小写

```python
ret = models.AuthorDetail.objects.filter(author__name='旭东').values('addr')
print(ret)
```

### 一对多

查询东京出版社出版了哪些书。

如果直接使用 SQL 语句，应该写成：

```mysql
select app01_book.title from app01_publish inner join app01_book on app01_publish.id = app01_book.publishs_id where app01_publish.name='东京出版社';
```

使用 ORM，则写成这样：

```python
ret = models.Publish.objects.filter(name='东京出版社').values('book__title')
print(ret)
ret = models.Book.objects.filter(publishs__name='东京出版社').values('title')
print(ret)
```

### 多对多

查询一下金龙 2 写了哪些书

```python
ret = models.Book.objects.filter(authors__name='金龙2').values('title')
print(ret)

ret = models.Author.objects.filter(name='金龙2').values('book__title')
print(ret)
```

## 聚合查询

aggregate 聚合查询，结果是普通字典，queryset 的结束符。

```python
from django.db.models import Avg,Max,Min,Count,Sum

obj = models.Book.objects.all().aggregate(a=Max('price')) #{'price__avg': 200.0}
print(obj)
Book.objects.aggregate(Avg('price'), Max('price'), Min('price')) 
```

## 分组查询

分组查询，写成 SQL 语句应该是：

```MySQL
group by app01_book.publishs_id
```

例如：找出每个出版社出版的书的最高价格

### 先取值后分组

```python
ret = models.Book.objects.values('publishs_id').annotate(m=Max('price'))
print(ret)
```

总结：values写在annotate前面，意思是以values括号内的字段作为分组的依据。annotate里面是你要做的统计结果。这样，返回结果为queryset类型数据，里面是字典：

```python
{'publishs_id':1,'m':100}
```

### 先分组后取值

```python
ret = models.Publish.objects.annotate(m=Max('book__price')).values('m', 'name')
print(ret)
```

总结：annotate直接写在了objects后面，意思是按照前面表的所有的数据（默认是id值）作为分组依据。结果返回的是前面这个表的所有models对象（model对象中包含了每个对象自己的统计结果），再通过values来取值。取值时可以直接写字段和统计结果的别名，也是queryset类型，里面是字典：

```python
{'m': 100, 'name': '东京出版社'}
```

查询每个作者的姓名以及出版的书的最高价格：

```python
ret = models.Book.objects.values('authors__name', 'authors__id').annotate(m=Max('price'))  # group by authors__name,authors__id
print(ret)

ret = models.Author.objects.annotate(m=Max('book__price')).values('name', 'm')
print(ret)
```

## F 查询

导入方式：

```python
from django.db.models import F
```

F 查询结果是本表中两个字段的比较之后的符合条件的结果集。

查询一下点赞数大于评论数的所有书籍。

从前，我们也可以通过使用循环来找到：

```python
list1 = []
books = models.Book.objects.all()
for i in books:
    if i.dianzan > i.comment:
        list1.append(i)
```

如果使用F查询，代码会更加简洁直观：

```python
ret = models.Book.objects.filter(dianzan__gt=F('comment')).values('title')

ret = models.Book.objects.filter(dianzan__lt=F('comment')).values('title')
print(ret)
```

本表字段进行四则运算：

```python
models.Book.objects.all().update(
    price=F('price') + 20  # 支持四则运算
)
```

## Q 查询

导入方式：

```python
from django.db.models import Q
```

Q 查询用来进行逻辑运算，Q 的连接符：

```mysql
& -- and
| -- or
~ -- not，取反
```

查询一下点赞大于 300 或者价钱小于 300 的书

```python
ret = models.Book.objects.filter(Q(dianzan__gt=300)|~Q(price__lt=500),xx='oo').values('title')

ret = models.Book.objects.filter(Q(dianzan__gt=300)).values('title')

ret = models.Book.objects.filter(Q(Q(dianzan__gt=300)|~Q(price__lt=500))&Q(xx='oo')).values('title')    # Q查询能够进行各种复杂条件的拼接
print(ret)
```

多表查询练习示例：

```python
    # 1 查询每个作者的姓名以及出版的书的最高价格
    ret = models.Author.objects.values('id','name').annotate(m=Max('book__price'))
    
    print(ret)

    # 2 查询作者id大于2作者的姓名以及出版的书的最高价格
    ret = models.Author.objects.filter(id__gt=2).values('name').annotate(a=Max('book__price'))
    print(ret)


    # 3 查询作者id大于2或者作者年龄大于等于3岁的女作者的姓名以及出版的书的最高价格
    ret = models.Author.objects.filter(Q(id__gt=2)|Q(Q(age__gte=3)&Q(sex='女'))).values('name').annotate(a=Max('book__price'))
    print(ret)

    # 4 查询每个作者出版的书的最高价格 的平均值
    ret = models.Book.objects.values('authors__name').annotate(m=Max('price')).aggregate(a=Avg('m'))
    print(ret)

    # 5 每个作者出版的所有书的最高价格以及最高价格的那本书的名称
    ret = models.Book.objects.values('authors__name').annotate(m=Max('price'))
    queryset({'authors__name':'xx','m':100},{'authors__name':'xx','m':100},)
```

