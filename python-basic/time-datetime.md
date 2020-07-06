## time 和 datetime 模块

[TOC]

### `time` 模块

time 模块，也就是时间模块，用来进行一些与时间有关的操作。其使用方法为：

```python
import time
print(time.time())   # 时间戳 浮点数
print(time.sleep(2)) # 秒 进行睡眠
```

时间分类:

1. 时间戳     --- 用于进行计算
2. 结构化时间 --- 给程序员查看使用（命名元组）
3. 字符串时间 --- 给用户查看的

时间模块的基本方法有：

```python
t = time.time()         # 用于获取当前时间戳
print(time.localtime(t))   # 将时间戳转成结构化时间
t = time.localtime()     # 将时间戳转成结构化时间,不写默认是当前时间戳
print(time.mktime(t))    # 将结构化时间转成时间戳
t = time.localtime()
print(time.strftime("%Y-%m-%d %H:%M:%S",t))  # 将结构化时间转换成字符串时间
str_time = "2019-9-1 12:23:06"
print(time.strptime(str_time,"%Y-%m-%d %H:%M:%S")) # 将字符串时间转换成结构化时间
print(time.strftime("%Y-%m-%d %H:%M:%S",time.localtime()))    # 将结构化时间转化为字符串时间
str_time = "2019-9-1 12:23:06"
print(time.mktime(time.strptime(str_time,"%Y-%m-%d %H:%M:%S")))

print(time.localtime()[0])
print(time.localtime().tm_year)
print(time.localtime().tm_yday)
```

输出的结果为：

```python
time.struct_time(tm_year=2019, tm_mon=9, tm_mday=26, tm_hour=19, tm_min=19, tm_sec=21, tm_wday=3, tm_yday=269, tm_isdst=0)
1569496761.0
2019-09-26 19:19:21
time.struct_time(tm_year=2019, tm_mon=9, tm_mday=1, tm_hour=12, tm_min=23, tm_sec=6, tm_wday=6, tm_yday=244, tm_isdst=-1)
2019-09-26 19:19:21
1567311786.0
2019
2019
269
```

### `datetime` 模块

datetime 模块的作用和 time 模块类似，都是用来进行时间操作的，其基本操作为：

```python
from datetime import datetime
print(datetime.now())   # 获取到当前是时间,对象
print(type(datetime.now()))
print(datetime(2016, 11, 13, 12, 13, 14)) # 将指定时间转化为 datetime 对象

import time
print(datetime.fromtimestamp(time.time()))    # 时间戳转换成对象
print(datetime.timestamp(datetime.now()))    # 将对象转换成时间戳


print(datetime.strftime(datetime.now(),"%Y-%m-%d %H:%M:%S"))    # 将对象转换成字符串
print(datetime.strptime("2019/10/14","%Y/%m/%d"))    # 将字符串转换成对象

print(datetime.now() - datetime(9999,11,1,12,13,14))    # 用来计算当前时间相对于指定时间相差多久

from datetime import datetime,timedelta
print(datetime.now())
print(datetime.now() - timedelta(days=400))    # 用来计算400天前时间的 datetime 对象
print(datetime.now() - timedelta(days=500))
```

输出的结果为：

```python
2019-09-26 19:32:59.456316
<class 'datetime.datetime'>
2016-11-13 12:13:14
2019-09-26 19:32:59.457314
1569497579.457313
2019-09-26 19:32:59
2019-10-14 00:00:00
-2914671 days, 7:19:45.490226
2019-09-26 19:32:59.490226
2018-08-22 19:32:59.490226
2018-05-14 19:32:59.490226
```

