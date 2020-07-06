## os 和 sys 模块

[TOC]

### os 模块

os 模块主要用来进行与操作系统进行交互，它的导入方式为：

```python
import os
```

os 模块主要涉及文件操作、文件夹操作、路径操作和其他操作四个方面。

#### 文件操作

通过 os 模块对文件直接进行的操作：

```python
os.rename('旧名字', '新名字')    # 重命名    非常重要
os.remove('要删除的文件名')    # 删除文件    非常重要
```

#### 文件夹操作

对文件夹进行操作：

```python
os.makedirs('a/b/c/d/e/f')    # 递归创建文件夹    非常重要
os.removedirs('a/b/c/d/e/f')    # 递归删除文件夹，若文件夹非空，会报错    非常重要
os.mkdir('a')    # 创建文件夹    非常重要
os.rmdir('a')    # 删除文件夹    非常重要
print(os.listdir('.'))    # 查看指定路径下的所有文件    非常重要
```

#### 路径操作

对路径的操作主要使用 `os.path` 下的各种方法：

```python
print(os.getcwd())    # 获取当前工作路径    非常重要
os.chdir('..\day14')    # 改变当前脚本工作目录，相当于终端下的cd
print(os.path.abspath('lib.py'))    # 获取文件的绝对路径
print(os.path.split('C:\Python_26\day14'))    # 对路径进行分割位目录和文件名，返回值为元组
print(os.path.dirname('C:\Python_26\day14'))    # 获取父级目录    非常重要
print(os.path.basename('C:\Python_26\day14'))    # 返回path最后的文件名

# is系列
print(os.path.exists('C:\Python_26\day14'))    # 判断路径是否存在
print(os.path.isabs('day07'))    # 判断是否是绝对路径，如果是则返回True
print(os.path.isfile('C:\Python_26\day15\lib.py'))    # 判断是否是一个存在的文件，如果是则返回True    非常重要
print(os.path.isdir('C:\Python_26\day15')    # 判断是否是一个存在的目录，如果是则返回True    非常重要
print(os.path.join("C:\\User",'Python_26',"day15","lib.py"))    # 将多个路径组合后返回    非常重要
print(os.path.getsize('C:\Python_26\day15\lib.py'))    # 获取文件大小
```

#### 其他操作

os 方法的其他操作：

```python
print(os.open('dir').read())    # 给终端发送指令
print(os.environ)    # 获取环境变量
```

#### 总结

os 模块功能很多很杂，不可能全都记住，但是下面的这些功能我们日后会经常用到，一定要尽量学会使用：

```python
os.getcwd()
os.mkdir()
os.rmdir()
os.makedirs()
os.removedirs()
os.rename()
os.remove()
os.path.abspath()
os.path.dirname()
os.path.basename()
os.path.join()
os.path.isfile()
os.path.isdir()
os.path.getsize()
```

### sys 模块

sys 模块用来和 Python 解释器进行交互，我们只需要记住 `sys.path` 功能即可：

```python
import sys
print(sys.path)
```

除此之外，sys 模块还有几个有意思的功能可以稍作了解：

```python
import sys
if sys.platform == 'win32':    # sys.platform方法用来查看操作系统平台
    print('Windows')
elif sys.platform == 'darwin':
    print('Mac')
    
lst = sys.argv    # 在终端中模拟input输入，避免input函数阻塞
print(lst[-1])

print(sys.version)    # 用来查看Python的版本
sys.exit(1)    # 修改Python结束后的报告代码

print(sys.modules)   # 获取所有的模块

sys.setrecuisionlimit()    # 设置递归深度
```