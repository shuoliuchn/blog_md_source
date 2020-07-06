# 使用 Django 搭建静态文件分发服务器





## 问题的产生

CRM 项目已经想好，想要部署到线上。

将 Django 项目部署项目之前，将 settings 中的 DEBUG 变量设置成 False，并指定授权访问的 IP：

```python
DEBUG = False
ALLOWED_HOSTS = ['*']    # '*'代表允许所有 IP 访问项目
```

然后以 `0.0.0.0:80` 启动 Django 项目：

```bash
sudo python3 manage.py runserver 0.0.0.0:80
```

浏览器输入服务器的 IP 访问项目。却发现什么样式都不见了。

![1579001343615](django-distribute-static.assets\1579001343615.png)

造成这种情况的原因是，Django 线上模式的静态文件托管是无效的。因为 Django 的静态文件托管很脆弱，无法应对复杂的线上环境。

我们知道这是 Django 的好心。可是对我来说却坏透了：除了 Django 之外，我不知道别的静态文件托管方式。

老师说，可以使用 Nginx 托管静态文件，但是这东西我也只知其名，根本不会用。

也就是说，现在的我处在一个尴尬的空窗期：能用 Django 把项目写出来，但是没办法部署到线上。

上次的 rbac 项目我成功使用码云的 `Gitee Pages` 服务托管了静态文件。但是一来，我不能过分依赖别人——码云服务器并不稳定，而且偶尔会拒绝外部访问。二来，这次我试图用码云托管静态文件时，出现了错误， `Gitee Pages` 服务干脆不能启动。

![1579001813164](django-distribute-static.assets\1579001813164.png)

如果慢慢研究，我或许能找出其中的问题。但有一个念头出现在我的脑海，其实这个想法我一直都有，那就是，我可不可以**把静态文件也部署到服务器上**，使用除 80 之外的端口（比如：8080）。而项目中的静态文件也不使用 Django 托管，而是让它去服务器中拿。

我觉得这是可行。一不做二不休，干起来呀~

## 静态文件托管项目

新建一个 Django 项目，把需要部署的静态文件放到一个文件夹中。为了图方便，我直接放在了 template 文件夹中。这是不规范的，因为 template 文件夹一般放的都是 html 文件。因为是权宜之计，而且也没有指望在这个项目里放什么 html 文件，让这个目录空着也不太好不是。

![1579002751188](django-distribute-static.assets\1579002751188.png)

文件拷贝完成，就该指定规则。

我的思路是，当有访问请求过来时，**根据访问请求的路径，返回他需要的静态文件**。比如，当访问请求是 `IP:PORT/NBCrm/adminAuth/css/style.css` 时，就返回给他 `\templates\NBCrm\auth\css\style.css` 这个文件。

若根据不同请求分别配置 url 路由显然是不恰当的。首先，静态文件太多，我不可能给每个文件都分配路由，写视图函数。然后，那样的写法可移植性太差。如果我需要增加静态文件，还要再分配路由，再写视图函数。也会有大量重复代码。总是，是很不合理的。

既然要处理所有的请求，我何不干脆在中间件针对所有的请求进行处理呢？也就是说，当访问请求过来时，先在中间件处理。中间件拿到请求的路径，直接把文件返回给访问者，就不去找路由了。这样一来，路径就成了参数，我们只需要写一个函数就能完成文件处理了。

写成的代码如下：

```python
import os

from django.http import StreamingHttpResponse
from django.utils.deprecation import MiddlewareMixin
from django.conf import settings


class StaticDirect(MiddlewareMixin):
    def process_request(self, request):
        base_dir = settings.BASE_DIR
        # URL路径之间以“/”分隔，但是Windows系统中的路径分隔符是“\”，所以要拆分重组
        request_path_list = request.path.split('/')
        # 拼接文件路径，我的静态文件放在了 templates 文件夹下
        file_path = os.path.join(base_dir, 'templates', *request_path_list)
        file_name = os.path.basename(file_path)
        # 以下是返回文件的基本操作
        file = open(file_path, 'rb')
        response = StreamingHttpResponse(file)
        response['Content-Type'] = 'application/octet-stream'
        response['Content-Disposition'] = f'attachment;filename="{file_name}"'
        return response
```

不要忘了在 settings.py 的设置中加入我们的中间件：

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'app01.mymiddlewares.mymiddleware.LoginAuth',
    'rbac.mymiddleware.auth.Auth',    # 也就是这一行
]
```

至此，我们静态文件托管项目已经基本完成。非常容易对不对~

## 原项目（CRM）的修改

我最开始的想法是，让静态文件托管项目在本地运行，也就是把 IP 设置成 `127.0.0.1`。那么如果我要访问静态文件，比如刚刚的 `style.css`，只需要把 css 样式的引入方式写成：

```html
<link rel="stylesheet" href="https://127.0.0.1:8080/NBCrm/adminAuth/css/style.css">
```

我们在调试时，css 文件的引入方式为：

```html
<link rel="stylesheet" href="{% static 'auth/css/style.css' %}">
```

我们发现，如果我们使用新的写法，会对原来的写法有较大改动，一条一条改起来很麻烦。

另一方面，我们或许会改变 IP 和端口号。对于前面的写法，修改起来是极麻烦的。

于是，我决定通过自定义标签的方式对原来的代码进行修改。自定义标签这样写：

```python
from django import template
from django.conf import settings

register = template.Library()

@register.simple_tag
def static_url(static_path):
    static_list = static_path.split('/')
    return '/'.join([settings.STATIC_HOST] + static_list)
```

静态文件托管项目的ip和端口信息记录在 `setting.py` 文件的 `STATIC_HOST` 中：

```python
STATIC_HOST = 'http://127.0.0.1:8080'
```

我给自定义标签取的名字为 `static_url`。这样，我只需要修改少量代码就可以顺利指向我们的静态文件托管项目上来：

```html
<link rel="stylesheet" href="{% static_url 'NBCrm/auth/css/style.css' %}">
```

路径前面加了一个 NBCrm 是因为要区分 CRM 项目 和 rbac 项目而分别建立的两个文件夹。其实不这样做也是可以的，仅仅是为了增加程序的规范性（时而要规范，时而不规范，真香~）。

把所有的静态文件都按此格式修改，就算大功告成。以后要想修改 IP 地址或端口，直接去 `settings.py` 文件中修改相应的内容即可。

## 静态文件托管在 `127.0.0.1` 测试

现在，我们完成了静态文件托管项目的创建和原项目为使用托管项目而做出的修改。万事俱备，只欠东风。

把项目传到服务器上。可以使用 ftp，也可以使用 git，都挺好用的。

然后，按预定的思路，先在 `127.0.0.1:8080` 下，以 nohub 模式启动静态文件托管系统（首先当然要用 cd 命令把工作目录转到静态文件托管系统项目目录下）：

```bash
sudo nohup python3 manage.py runserver 127.0.0.1:8080 &
```

接着，在 80 端口，以线上模式部署 CRM 项目（当然也要先 cd 目录）：

```bash
sudo python3 manage.py runserver 0.0.0.0:80
```

打开浏览器测试。果然不出所料，没有成功/(ㄒoㄒ)/~~

![1579005785237](django-distribute-static.assets\1579005785237.png)

思考一下浏览器请求的顺序，便可恍然大悟。首先，浏览器向服务器发送访问请求。CRM项目，也就是 Django 帮我们渲染好页面，并返回给我们。注意下图中的第三步我加粗的部分：渲染出来的 IP 是 `127.0.0.1`。这些内容会以 HTML 页面的形式返回到我们的浏览器中。浏览器解析页面，当发现这个样式外链时，自然会去相应的地址发出请求。而这个地址是 `127.0.0.1`，也就是指代用户的电脑。于是就向用户电脑发送访问请求。很显然，用户电脑上是没有静态文件托管系统的，于是什么静态文件也拿不到。而对于我们服务器上的静态文件托管系统，却无人对其访问。![静态文件托管系统ip问题 (django-distribute-static.assets\静态文件托管系统ip问题 (1).png)](C:\Users\Sure\OneDrive - RLS ,inc\Downloads\静态文件托管系统ip问题 (1).png)

这样看来，将静态文件托管到本地回环地址 `127.0.0.1` 是不合理的。把它以线上模式部署也变成了理所当然的选择。

## 静态文件以线上模式托管

上文说到，静态文件不该托管在  `127.0.0.1`。若要以线上模式托管，就要修改 `settings.py` 的配置。文章最开始的地方已经提到了：

```python
DEBUG = False
ALLOWED_HOSTS = ['*']
```

Django 文件可以在不停止项目运行的情况下修改，所以一般情况无需终止进程。不过这次我们要修改 IP，所以还是要终止一下。

首先，使用命令找到 Django 的进程号：

```bash
ps -ef|grep "python"
```

得到的结果是这样的：

```bash
root       924     1  0  2019 ?        00:00:00 /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
root       962     1  0  2019 ?        00:00:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
root      8618 26216  0 21:17 pts/0    00:00:00 sudo nohup python3 manage.py runserver 127.0.0.1:8080
root      8619  8618  0 21:17 pts/0    00:00:00 python3 manage.py runserver 127.0.0.1:8080
root      8630  8619  1 21:17 pts/0    00:00:00 /usr/bin/python3 manage.py runserver 127.0.0.1:8080
ubuntu    8718 26216  0 21:18 pts/0    00:00:00 grep --color=auto python
root     25109     1  0 Jan12 ?        00:00:00 sudo nohup python3 manage.py runserver 0.0.0.0:80
root     25110 25109  0 Jan12 ?        00:00:00 python3 manage.py runserver 0.0.0.0:80
root     25117 25110  0 Jan12 ?        00:20:57 /usr/bin/python3 manage.py runserver 0.0.0.0:80
```

我们找到 `/usr/bin/python3 manage.py runserver 127.0.0.1:8080` 对应的进程，其编号是 8630，使用命令将其终止：

```bash
sudo kill -s 9 8630
```

然后，以线上模式运行我们的静态文件托管项目：

```bash
sudo nohup python3 manage.py runserver 0.0.0.0:8080 &
```

再次访问，就会发现，页面成了这个样子：

![1579008162415](django-distribute-static.assets\1579008162415.png)

这怎么说呢？成功一半：图片成功返回，但是样式全部失效。其实也可以说，我们成功了一多半：图片顺利出现，说明连接已经顺利建立，我们成功和静态文件托管系统通信。而且如果我们使用浏览器的控制台查看网络情况就会发现，其实我们的 css 和 js 文件也都接收到了，只是样式没有生效而已：

![1579009110902](django-distribute-static.assets\1579009110902.png)

此时，其实已经超出我所学的范围了。不过我还是有一个不置可否地猜测：是不是浏览器在接收样式文件时需要声明一下文件类型，才能应用样式呢？

于是，我开始进行对返回文档格式进行指定的尝试。

## 指定静态文件的格式

我的这个猜想并不是凭空产生的。还记得我们在写静态文件托管项目的时候，我说，有一段是返回文件的固定写法吗？也就是这段代码：

```python
file = open(file_path, 'rb')
response = StreamingHttpResponse(file)
response['Content-Type'] = 'application/octet-stream'
response['Content-Disposition'] = f'attachment;filename="{file_name}"'
```

按道理讲，这四行代码，每一行都改有其存在的意义，也就是有修改的必要。否则，应该封装到方法中，而不是每次都要进行配置。出第三行外，每一行代码的作用都显而易见，我也不必多说。

唯独这第三行代码，从名称上看，该是指定文件的类型的。但是我一直都是使用这种写法，从来没有改过。但既然可以指定，就应该能指定成其他形式。想到在 css 样式的格式似乎是 “text/css”，js 的格式该是“text/javascript”。抱着试试看的态度，我把代码写成了这个样子：

```python
f_extension = os.path.splitext(file_name)[1]

if f_extension == '.css':
    print('css')
    response['Content-Type'] = 'text/css'
elif f_extension == '.js':
    print('js')
    response['Content-Type'] = 'text/javascript'
else:
    response['Content-Type'] = 'application/octet-stream'
```

再次刷新运行，终于，得偿所愿，所有样式全部出现：

![1579009847425](django-distribute-static.assets\1579009847425.png)

不过故事到这里还没有完全结束，因为很显然，文件类型不仅仅有 css 和 js 两种。对于其他数据类型，尤其是各种图片，万一也需要指定格式，岂不是又无法识别了吗？

我猜想，这种格式要求应该是 http 协议簇中有规定的。于是在网上一搜，果不其然，搜到 HTTP content-type 对照表。针对不同的文件扩展名，给出特定的 content-type 规范写法，比如：

| 文件扩展名                          | Content-Type(Mime-Type)  | 文件扩展名 | Content-Type(Mime-Type) |
| :---------------------------------- | :----------------------- | :--------- | :---------------------- |
| .*（ 二进制流，不知道下载文件类型） | application/octet-stream | .tif       | image/tiff              |
| .001                                | application/x-001        | .301       | application/x-301       |
| .323                                | text/h323                | .906       | application/x-906       |
| .907                                | drawing/907              | .a11       | application/x-a11       |
| .acp                                | audio/x-mei-aac          | .ai        | application/postscript  |
| .aif                                | audio/aiff               | .aifc      | audio/aiff              |

更加详细的可以参见 [HTTP content-type](https://www.runoob.com/http/http-content-type.html) 。

使用简单的脚本，即可得到文件扩展名和 content-type 作为键值对的字典。这个字典我贴在了文章的最底下，各位朋友若有需要，请自便。

有了这个字典，稍加修饰，便让代码更加简洁，通用性也更强：

```python
class StaticDirect(MiddlewareMixin):
    def process_request(self, request):
        base_dir = settings.BASE_DIR
        print(request.path)
        request_path_list = request.path.split('/')
        file_path = os.path.join(base_dir, 'templates', *request_path_list)
        file_name = os.path.basename(file_path)
        file = open(file_path, 'rb')
        response = StreamingHttpResponse(file)
        f_extension = os.path.splitext(file_name)[1]
        response['Content-Type'] = content_type_dict.get(f_extension) or content_type_dict.get('.*')
        response['Content-Disposition'] = f'attachment;filename="{file_name}"'
        return response
```

倒数第二行代码我使用了一个短路操作：

```python
response['Content-Type'] = content_type_dict.get(f_extension) or content_type_dict.get('.*')
```

意思是，如果字典中没有找到相应的后缀名，就用通用模式返回数据。

# HTTP content-type 与扩展名对应字典

```python
content_type_dict={
    '.*': 'application/octet-stream',
    '.tif': 'application/x-tif',
    '.001': 'application/x-001',
    '.301': 'application/x-301',
    '.323': 'text/h323',
    '.906': 'application/x-906',
    '.907': 'drawing/907',
    '.a11': 'application/x-a11',
    '.acp': 'audio/x-mei-aac',
    '.ai': 'application/postscript',
    '.aif': 'audio/aiff',
    '.aifc': 'audio/aiff',
    '.aiff': 'audio/aiff',
    '.anv': 'application/x-anv',
    '.asa': 'text/asa',
    '.asf': 'video/x-ms-asf',
    '.asp': 'text/asp',
    '.asx': 'video/x-ms-asf',
    '.au': 'audio/basic',
    '.avi': 'video/avi',
    '.awf': 'application/vnd.adobe.workflow',
    '.biz': 'text/xml',
    '.bmp': 'application/x-bmp',
    '.bot': 'application/x-bot',
    '.c4t': 'application/x-c4t',
    '.c90': 'application/x-c90',
    '.cal': 'application/x-cals',
    '.cat': 'application/vnd.ms-pki.seccat',
    '.cdf': 'application/x-netcdf',
    '.cdr': 'application/x-cdr',
    '.cel': 'application/x-cel',
    '.cer': 'application/x-x509-ca-cert',
    '.cg4': 'application/x-g4',
    '.cgm': 'application/x-cgm',
    '.cit': 'application/x-cit',
    '.class': 'java/*',
    '.cml': 'text/xml',
    '.cmp': 'application/x-cmp',
    '.cmx': 'application/x-cmx',
    '.cot': 'application/x-cot',
    '.crl': 'application/pkix-crl',
    '.crt': 'application/x-x509-ca-cert',
    '.csi': 'application/x-csi',
    '.css': 'text/css',
    '.cut': 'application/x-cut',
    '.dbf': 'application/x-dbf',
    '.dbm': 'application/x-dbm',
    '.dbx': 'application/x-dbx',
    '.dcd': 'text/xml',
    '.dcx': 'application/x-dcx',
    '.der': 'application/x-x509-ca-cert',
    '.dgn': 'application/x-dgn',
    '.dib': 'application/x-dib',
    '.dll': 'application/x-msdownload',
    '.doc': 'application/msword',
    '.dot': 'application/msword',
    '.drw': 'application/x-drw',
    '.dtd': 'text/xml',
    '.dwf': 'application/x-dwf',
    '.dwg': 'application/x-dwg',
    '.dxb': 'application/x-dxb',
    '.dxf': 'application/x-dxf',
    '.edn': 'application/vnd.adobe.edn',
    '.emf': 'application/x-emf',
    '.eml': 'message/rfc822',
    '.ent': 'text/xml',
    '.epi': 'application/x-epi',
    '.eps': 'application/postscript',
    '.etd': 'application/x-ebx',
    '.exe': 'application/x-msdownload',
    '.fax': 'image/fax',
    '.fdf': 'application/vnd.fdf',
    '.fif': 'application/fractals',
    '.fo': 'text/xml',
    '.frm': 'application/x-frm',
    '.g4': 'application/x-g4',
    '.gbr': 'application/x-gbr',
    '.': 'application/x-',
    '.gif': 'image/gif',
    '.gl2': 'application/x-gl2',
    '.gp4': 'application/x-gp4',
    '.hgl': 'application/x-hgl',
    '.hmr': 'application/x-hmr',
    '.hpg': 'application/x-hpgl',
    '.hpl': 'application/x-hpl',
    '.hqx': 'application/mac-binhex40',
    '.hrf': 'application/x-hrf',
    '.hta': 'application/hta',
    '.htc': 'text/x-component',
    '.htm': 'text/html',
    '.html': 'text/html',
    '.htt': 'text/webviewhtml',
    '.htx': 'text/html',
    '.icb': 'application/x-icb',
    '.ico': 'application/x-ico',
    '.iff': 'application/x-iff',
    '.ig4': 'application/x-g4',
    '.igs': 'application/x-igs',
    '.iii': 'application/x-iphone',
    '.img': 'application/x-img',
    '.ins': 'application/x-internet-signup',
    '.isp': 'application/x-internet-signup',
    '.IVF': 'video/x-ivf',
    '.java': 'java/*',
    '.jfif': 'image/jpeg',
    '.jpe': 'application/x-jpe',
    '.jpeg': 'image/jpeg',
    '.jpg': 'application/x-jpg',
    '.js': 'application/x-javascript',
    '.jsp': 'text/html',
    '.la1': 'audio/x-liquid-file',
    '.lar': 'application/x-laplayer-reg',
    '.latex': 'application/x-latex',
    '.lavs': 'audio/x-liquid-secure',
    '.lbm': 'application/x-lbm',
    '.lmsff': 'audio/x-la-lms',
    '.ls': 'application/x-javascript',
    '.ltr': 'application/x-ltr',
    '.m1v': 'video/x-mpeg',
    '.m2v': 'video/x-mpeg',
    '.m3u': 'audio/mpegurl',
    '.m4e': 'video/mpeg4',
    '.mac': 'application/x-mac',
    '.man': 'application/x-troff-man',
    '.math': 'text/xml',
    '.mdb': 'application/x-mdb',
    '.mfp': 'application/x-shockwave-flash',
    '.mht': 'message/rfc822',
    '.mhtml': 'message/rfc822',
    '.mi': 'application/x-mi',
    '.mid': 'audio/mid',
    '.midi': 'audio/mid',
    '.mil': 'application/x-mil',
    '.mml': 'text/xml',
    '.mnd': 'audio/x-musicnet-download',
    '.mns': 'audio/x-musicnet-stream',
    '.mocha': 'application/x-javascript',
    '.movie': 'video/x-sgi-movie',
    '.mp1': 'audio/mp1',
    '.mp2': 'audio/mp2',
    '.mp2v': 'video/mpeg',
    '.mp3': 'audio/mp3',
    '.mp4': 'video/mpeg4',
    '.mpa': 'video/x-mpg',
    '.mpd': 'application/vnd.ms-project',
    '.mpe': 'video/x-mpeg',
    '.mpeg': 'video/mpg',
    '.mpg': 'video/mpg',
    '.mpga': 'audio/rn-mpeg',
    '.mpp': 'application/vnd.ms-project',
    '.mps': 'video/x-mpeg',
    '.mpt': 'application/vnd.ms-project',
    '.mpv': 'video/mpg',
    '.mpv2': 'video/mpeg',
    '.mpw': 'application/vnd.ms-project',
    '.mpx': 'application/vnd.ms-project',
    '.mtx': 'text/xml',
    '.mxp': 'application/x-mmxp',
    '.net': 'image/pnetvue',
    '.nrf': 'application/x-nrf',
    '.nws': 'message/rfc822',
    '.odc': 'text/x-ms-odc',
    '.out': 'application/x-out',
    '.p10': 'application/pkcs10',
    '.p12': 'application/x-pkcs12',
    '.p7b': 'application/x-pkcs7-certificates',
    '.p7c': 'application/pkcs7-mime',
    '.p7m': 'application/pkcs7-mime',
    '.p7r': 'application/x-pkcs7-certreqresp',
    '.p7s': 'application/pkcs7-signature',
    '.pc5': 'application/x-pc5',
    '.pci': 'application/x-pci',
    '.pcl': 'application/x-pcl',
    '.pcx': 'application/x-pcx',
    '.pdf': 'application/pdf',
    '.pdx': 'application/vnd.adobe.pdx',
    '.pfx': 'application/x-pkcs12',
    '.pgl': 'application/x-pgl',
    '.pic': 'application/x-pic',
    '.pko': 'application/vnd.ms-pki.pko',
    '.pl': 'application/x-perl',
    '.plg': 'text/html',
    '.pls': 'audio/scpls',
    '.plt': 'application/x-plt',
    '.png': 'application/x-png',
    '.pot': 'application/vnd.ms-powerpoint',
    '.ppa': 'application/vnd.ms-powerpoint',
    '.ppm': 'application/x-ppm',
    '.pps': 'application/vnd.ms-powerpoint',
    '.ppt': 'application/x-ppt',
    '.pr': 'application/x-pr',
    '.prf': 'application/pics-rules',
    '.prn': 'application/x-prn',
    '.prt': 'application/x-prt',
    '.ps': 'application/postscript',
    '.ptn': 'application/x-ptn',
    '.pwz': 'application/vnd.ms-powerpoint',
    '.r3t': 'text/vnd.rn-realtext3d',
    '.ra': 'audio/vnd.rn-realaudio',
    '.ram': 'audio/x-pn-realaudio',
    '.ras': 'application/x-ras',
    '.rat': 'application/rat-file',
    '.rdf': 'text/xml',
    '.rec': 'application/vnd.rn-recording',
    '.red': 'application/x-red',
    '.rgb': 'application/x-rgb',
    '.rjs': 'application/vnd.rn-realsystem-rjs',
    '.rjt': 'application/vnd.rn-realsystem-rjt',
    '.rlc': 'application/x-rlc',
    '.rle': 'application/x-rle',
    '.rm': 'application/vnd.rn-realmedia',
    '.rmf': 'application/vnd.adobe.rmf',
    '.rmi': 'audio/mid',
    '.rmj': 'application/vnd.rn-realsystem-rmj',
    '.rmm': 'audio/x-pn-realaudio',
    '.rmp': 'application/vnd.rn-rn_music_package',
    '.rms': 'application/vnd.rn-realmedia-secure',
    '.rmvb': 'application/vnd.rn-realmedia-vbr',
    '.rmx': 'application/vnd.rn-realsystem-rmx',
    '.rnx': 'application/vnd.rn-realplayer',
    '.rp': 'image/vnd.rn-realpix',
    '.rpm': 'audio/x-pn-realaudio-plugin',
    '.rsml': 'application/vnd.rn-rsml',
    '.rt': 'text/vnd.rn-realtext',
    '.rtf': 'application/x-rtf',
    '.rv': 'video/vnd.rn-realvideo',
    '.sam': 'application/x-sam',
    '.sat': 'application/x-sat',
    '.sdp': 'application/sdp',
    '.sdw': 'application/x-sdw',
    '.sit': 'application/x-stuffit',
    '.slb': 'application/x-slb',
    '.sld': 'application/x-sld',
    '.slk': 'drawing/x-slk',
    '.smi': 'application/smil',
    '.smil': 'application/smil',
    '.smk': 'application/x-smk',
    '.snd': 'audio/basic',
    '.sol': 'text/plain',
    '.sor': 'text/plain',
    '.spc': 'application/x-pkcs7-certificates',
    '.spl': 'application/futuresplash',
    '.spp': 'text/xml',
    '.ssm': 'application/streamingmedia',
    '.sst': 'application/vnd.ms-pki.certstore',
    '.stl': 'application/vnd.ms-pki.stl',
    '.stm': 'text/html',
    '.sty': 'application/x-sty',
    '.svg': 'text/xml',
    '.swf': 'application/x-shockwave-flash',
    '.tdf': 'application/x-tdf',
    '.tg4': 'application/x-tg4',
    '.tga': 'application/x-tga',
    '.tiff': 'image/tiff',
    '.tld': 'text/xml',
    '.top': 'drawing/x-top',
    '.torrent': 'application/x-bittorrent',
    '.tsd': 'text/xml',
    '.txt': 'text/plain',
    '.uin': 'application/x-icq',
    '.uls': 'text/iuls',
    '.vcf': 'text/x-vcard',
    '.vda': 'application/x-vda',
    '.vdx': 'application/vnd.visio',
    '.vml': 'text/xml',
    '.vpg': 'application/x-vpeg005',
    '.vsd': 'application/x-vsd',
    '.vss': 'application/vnd.visio',
    '.vst': 'application/x-vst',
    '.vsw': 'application/vnd.visio',
    '.vsx': 'application/vnd.visio',
    '.vtx': 'application/vnd.visio',
    '.vxml': 'text/xml',
    '.wav': 'audio/wav',
    '.wax': 'audio/x-ms-wax',
    '.wb1': 'application/x-wb1',
    '.wb2': 'application/x-wb2',
    '.wb3': 'application/x-wb3',
    '.wbmp': 'image/vnd.wap.wbmp',
    '.wiz': 'application/msword',
    '.wk3': 'application/x-wk3',
    '.wk4': 'application/x-wk4',
    '.wkq': 'application/x-wkq',
    '.wks': 'application/x-wks',
    '.wm': 'video/x-ms-wm',
    '.wma': 'audio/x-ms-wma',
    '.wmd': 'application/x-ms-wmd',
    '.wmf': 'application/x-wmf',
    '.wml': 'text/vnd.wap.wml',
    '.wmv': 'video/x-ms-wmv',
    '.wmx': 'video/x-ms-wmx',
    '.wmz': 'application/x-ms-wmz',
    '.wp6': 'application/x-wp6',
    '.wpd': 'application/x-wpd',
    '.wpg': 'application/x-wpg',
    '.wpl': 'application/vnd.ms-wpl',
    '.wq1': 'application/x-wq1',
    '.wr1': 'application/x-wr1',
    '.wri': 'application/x-wri',
    '.wrk': 'application/x-wrk',
    '.ws': 'application/x-ws',
    '.ws2': 'application/x-ws',
    '.wsc': 'text/scriptlet',
    '.wsdl': 'text/xml',
    '.wvx': 'video/x-ms-wvx',
    '.xdp': 'application/vnd.adobe.xdp',
    '.xdr': 'text/xml',
    '.xfd': 'application/vnd.adobe.xfd',
    '.xfdf': 'application/vnd.adobe.xfdf',
    '.xhtml': 'text/html',
    '.xls': 'application/x-xls',
    '.xlw': 'application/x-xlw',
    '.xml': 'text/xml',
    '.xpl': 'audio/scpls',
    '.xq': 'text/xml',
    '.xql': 'text/xml',
    '.xquery': 'text/xml',
    '.xsd': 'text/xml',
    '.xsl': 'text/xml',
    '.xslt': 'text/xml',
    '.xwd': 'application/x-xwd',
    '.x_b': 'application/x-x_b',
    '.sis': 'application/vnd.symbian.install',
    '.sisx': 'application/vnd.symbian.install',
    '.x_t': 'application/x-x_t',
    '.ipa': 'application/vnd.iphone',
    '.apk': 'application/vnd.android.package-archive',
    '.xap': 'application/x-silverlight-app'
}
```

