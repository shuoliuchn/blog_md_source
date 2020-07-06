## ORM 执行原生 SQL 语句（了解）

众所周知，Django 的 ORM 效率是比较低的。偶尔或许会有需求，要使用原生 SQL 语句提高数据库的查询效率。

### 方式 1 object 对象的 raw 方法

```python
ret = models.Book.objects.raw('select * from app01_book;')
for i in ret:
    print(i.title)
print(ret)
```

### 方式 2 Django 自带的连接通道（配置的 pymysql）

```python
from django.db import connection
import pymysql
conn = pymysq.connect()
cursor = connection.cursor()
cursor.execute('select * from app01_book;')
print(cursor.fetchall())
```

### 方式 3 pymysql

```python
conn = pymysql.connect(
    host='127.0.0.1',
    port=3306,
    user='root',
    password='123',
    database='orm02',
    charset='utf8',
)
cursor = conn.cursor(pymysql.cursors.DictCursor)
cursor.execute('select * from app01_book;')
print(cursor.fetchall())
```