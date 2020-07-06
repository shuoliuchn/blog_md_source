## 荏苒资讯项目环境部署

### 创建虚拟环境

如果当前系统中没有虚拟环境，则使用以下命令安装虚拟环境：

```bash
pip3 install virtualenv  -i https://pypi.tuna.tsinghua.edu.cn/simple
pip3 install virtualenvwrapper -i https://pypi.tuna.tsinghua.edu.cn/simple
pip3 install virtualenvwrapper-win -i https://pypi.tuna.tsinghua.edu.cn/simple
```

创建新的虚拟环境 renran：

```bash
mkvirtualenv renran
```

如果自己的开发机子中存在多个版本的python，则可以指定python解析器的版本

```bash
mkvirtualenv renran -p python3
```

### 相关命令

虚拟环境相关命令：

```bash
创建虚拟环境：                mkvirtualenv 虚拟环境名称
创建虚拟环境(指定python版本)： mkvirtualenv -p python3 虚拟环境名称
查看所有虚拟环境：             workon
使用虚拟环境：                workon 虚拟环境名称
退出虚拟环境：                deactivate
删除虚拟环境（必须先退出虚拟环境内部才能删除当前虚拟环境）:
                           	rmvirtualenv 虚拟环境名称
```

其他相关命令：

```bash
查看虚拟环境中安装的包：              pip freeze  或者 pip list
收集当前环境中安装的包及其版本：       pip freeze > requirements.txt
在部署项目的服务器中安装项目使用的模块： pip install -r requirements.txt
```

提示:

- 虚拟环境只会管理环境内部的模块和 Python 解析器,对于源代码是毫无关系
- 创建虚拟环境需要联网
- 创建成功后, 会自动工作在这个虚拟环境上
- 工作在虚拟环境上, 提示符最前面会出现 “(虚拟环境名称)”

### 技术选型

#### 外部依赖

1. 注册支付宝的开发者账号
2. 注册阿里云账号。如果可以购买一个服务器，或者第一次使用的可以申请一个免费外网服务器
3. 注册容联云短信接口平台的账号
4. 注册腾讯开发者账户，申请 QQ 互联开发者实名认证
5. 申请 163 或者 QQ 邮箱开通 smtp/pop3 服务
6. 注册 gitee（码云）的账号
7. 如果有条件的，可以申请一个域名进行备案（ICP 备案和公安部备案）。如果没有的话，可以注册 natapp

#### 依赖包安装

```bash
pip install django==2.2.0  -i https://pypi.douban.com/simple
pip install djangorestframework  -i https://pypi.douban.com/simple
pip install PymySQL  -i https://pypi.douban.com/simple
pip install Pillow  -i https://pypi.douban.com/simple
pip install django-redis  -i https://pypi.douban.com/simple
```

