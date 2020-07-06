## Django 操作 Cookie 和 session

### Cookie 初识

#### Cookie 的由来

大家都知道 HTTP 协议是无状态的。

无状态的意思是每次请求都是独立的。它的执行情况和结果与前面的请求和之后的请求都无直接关系，它不会受前面的请求响应情况直接影响，也不会直接影响后面的请求响应情况。

一句有意思的话来描述就是人生只如初见，对服务器来说，每次的请求都是全新的。

状态可以理解为客户端和服务器在某次会话中产生的数据，那无状态的就以为这些数据不会被保留。会话中产生的数据又是我们需要保存的，也就是说要“保持状态”。因此 Cookie 就是在这样一个场景下诞生。

并且还有一个问题就是，你登陆我的网站的时候，我没法确定你是不是登陆了。之前我们学的 Django，虽然写了很多页面，但是用户不用登陆都是可以看所有网页的，只要他知道网址就行。但是我们为了自己的安全机制，我们还是做验证。访问哪一个网址，都要验证用户的身份。但是用户登陆过之后，还要保证登陆了的用户不需要再重复登陆，就能够访问我网站的其他的网址的页面。但是 HTTP 无状态啊，怎么保证这个事情呢？此时就要找 Cookie 了。

#### 什么是 Cookie

首先来讲，Cookie 是浏览器的技术。Cookie 具体指的是一段小信息，它是服务器发送出来存储在浏览器上的一组组键值对，可以理解为服务端给客户端的一个小甜点，下次访问服务器时浏览器会自动携带这些键值对，以便服务器提取有用信息。

#### Cookie 的原理

Cookie 的工作原理是：浏览器访问服务端，带着一个空的 Cookie，然后由服务器产生内容，浏览器收到相应后保存在本地。当浏览器再次访问时，浏览器会自动带上Cookie，这样服务器就能通过Cookie的内容来判断这个是“谁”了。

#### Http 协议特点

- 无状态，无连接（HTTP 1.1 版本出现了短连接）
- 格式：请求行 -- 请求头 -- 空行 -- 请求数据

### Django 操作 Cookie

获取 Cookie：

```python
request.COOKIES.get('xx')
```

获取签名 Cookie（不常用）：

```python
request.get_signed_cookie('is_login',salt='xxxxxx')
```

设置 Cookie：

```python
HttpResponse('xx').set_cookie('键','值')
```

设置签名 Cookie（不常用）：

```python
ret.set_signed_cookie('is_login',True,'xxxxxx')
```

设置 Cookie 超时时间和过期日期：

```python
ret.set_cookie('is_login', True, max_age=5)    # 超时时间 秒数
ret.set_cookie('is_login', True, expires=datetime.datetime.now() + datetime.timedelta(days=7))    #过期日期
```

删除 Cookie：

```python
ret.delete_cookie('xxoo')
```

### session 的优势

1. 借助于 Cookie 进行传输
2. 非明文显示
3. 长度不限
4. Django 为我们提供了十分方便的 session 使用接口，只需要简单地几条命令就可以实现 session 的设置、获取和清空。

### 设置 session

```python
request.session['xx'] = 'oo'
```

设置 session 的命令隐含三个步骤：

1. 生成随机字符串
2. 放到 Cookie 中进行传输
3. 将随机字符串和对应数据保存到自己服务端的数据库中，django-session 表

### 获取 Session

```python
request.session.get('xx') -- 'oo'
```

这简简单单的一条命令，其实隐含了大约三个获取session的步骤，也就是设置session的逆过程：

```python
# 1 取出cookie中的session随机字符串{'sessionid':'asdfasfpoaijsdgihsdj'}
xx = request.COOKIES.get('sessionid')
# 2 到数据库中查询这个sessionid对应的那条记录
data = select session_data from django_session where session_key = xx;
# 3 拿出记录中的session_data数据部分进行解密,并取出数据
dic = sss(data) -- {'is_login':True}
dic.get('is_login') -- True
```

### 注销session

```python
def logout(request):
    request.session.flush()  # 删除session
    return redirect('login')
```

`session.flush()` 命令是将 session 注销，主要是两个步骤：

1. 删除 Cookie 中的 sessionid 那个键值对
2. 删除了数据库中的这条记录

### Cookie 和 session 的总结与对比

cookie：

- 保持会话，使用户不需要重复地去登录
- 有大小限制
- 有个数限制
- Cookie 总大小上限为 4KB
- 一个服务器最多在客户端浏览器上保存 20 个 Cookie
- 一个浏览器最多保存 300 个 Cookie，因为一个浏览器可以访问多个服务器

session：

- 比 Cookie 在表面上更安全一些
- session 没有大小限制
- 可以配置多个存储方案，可以配置到缓存中