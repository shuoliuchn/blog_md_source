## hashlib 模块

hashlib 也称摘要算法或加密算法，其主要功能是对数据进行加密和校验文件的一致性。

hashlib 支持的加密算法有：md5、sha1、sha256 和 sha512。加密复杂度和花费时间依次增加。

加密算法的特点有：

1. 内容相同，密文一定相同；
2. 加密的密文是不可逆的；
3. 加密过程为：明文 --> 字节 --> 密文。

简单的加密操作为：

```python
import hashlib
s = 'abc'
md5 = hashlib.md5()    # 选择加密方式，初始化一个加密
md5.update(s.encode('utf-8'))    # 将要加密的内容，添加到m中
print(md5.hexdigest())
```

输出的结果为：

```python
900150983cd24fb0d6963f7d28e17f72
```

也可以通过添加一些字符的方式，增加破解难度，称作加盐：

```python
import hashlib
user = input('username:')
pwd = input('pwd:')
md5 = hashlib.md5('oldboy'.encode('utf-8'))    # 加盐
md5.update(pwd.encode('utf-8'))
print(md5.hexdigest())
```

除了像上面那样设定固定的盐，我们还可以使用用户名来实现动态加盐：

```python
import hashlib
user = input('username:')
pwd = input('pwd:')
md5 = hashlib.md5(user.encode('utf-8'))
md5.update(pwd.encode('utf-8'))
print(md5.hexdigest())
```

除了 md5 之外，也可以使用其他方式进行加密，操作是完全相同的：

```python
import hashlib
sha1 = hashlib.sha1()
sha1.update('alex'.encode('utf-8'))
print(sha1.hexdigest())
```

同样的内容使用同样的方法加密，即便使用不同的方式编码，最终加密出来的结果也是相同的：

```python
import hashlib
sha256 = hashlib.sha256()
sha256.update('wusir'.encode('utf-8'))
print(sha256.hexdigest())

sha256 = hashlib.sha256()
sha256.update('wusir'.encode('gbk'))
print(sha256.hexdigest())
```

输出的结果为：

```python
49be7bb8561373ffbccbac435bbdfd6ba8d2f973ec7154b3ae7adf266b929ca9
49be7bb8561373ffbccbac435bbdfd6ba8d2f973ec7154b3ae7adf266b929ca9
```

虽然加密结果一致，但还是建议尽量使用 utf-8 进行编码。

对于一个长字符串，同时输入和分段输入，得到的加密结果也是一样的：

```python
import hashlib
md5obj = hashlib.md5()
md5obj.update('alex'.encode('utf-8'))
md5obj.update('wusir'.encode('utf-8'))
print(md5obj.hexdigest())

md5obj = hashlib.md5()
md5obj.update('alexwusir'.encode('utf-8'))
print(md5obj.hexdigest())
```

输出的结果为：

```python
a5e646f24aa7314d8ba3dce048358e90
a5e646f24aa7314d8ba3dce048358e90
```

因此，对于大文件来说，我们可以逐行加密来节省内存空间。

总结起来，加密的基本结构为：

```python
import hashlib
md5obj = hashlib.md5(盐)
md5obj.update(加密的内容.encode('utf-8'))
print(md5obj.hexdigest())
```