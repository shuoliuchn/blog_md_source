## Python 操作 Redis

Python 有很多的模块都可以实现对 Redis 的操作，常用有 redis 和 pyredis，这两个模块的使用操作是类似的。

这里我们使用 redis 模块来进行演示。

安装 redis 模块：

```bash
pip install redis
```

代码示例：

```python
from redis import Redis
# redis链接
# redis = Redis(host="127.0.0.1",port=6379,db=1)
# 如果设置了密码
redis = Redis(host="127.0.0.1",port=6379,db=1,password="123456")
"""字符串操作"""
# 添加一个字符串数据 set name xiaoming
# redis.set("name","xiaoming")

# 添加一个临时数据, setex title 30 hello
# redis.setex("title",30,"hello")

# 查看一个数据的有效期,-2表示过期,-1表示永久
# time = redis.ttl("title")
# print(time)

# 获取一个字符串
# name = redis.get("name")
# print(name)  # 得到的结果是bytes类型的   b'xiaoming'
# print(name.decode()) # 必须要解码,xiaoming

# 删除key,因为del是一个关键词,所以在redis模块,凡是命令如果是一个关键词,全部改成单词的全拼
# redis.delete("name")

# 哈希的操作
# dict1 = {
#     "liubei": 28,
#     "guanyu": 20,
#     "zhangfei": 14,
# }
# redis.hmset("brother",dict1)

# 获取哈希里面的所有成员
dict_data = redis.hgetall("brother")
print(dict_data) # {b'liubei': b'28', b'guanyu': b'20', b'zhangfei': b'14'}
for key,name in dict_data.items():
    print(key.decode(),name.decode())
    """
    liubei 28
    guanyu 20
    zhangfei 14
    """
age = dict_data.get("liubei".encode()).decode()
print(age) # b'28'
```

