# 数据解析（正则、bs4 和 xpath）

[TOC]

## 数据解析初识

数据解析，就是把我们所需要的数据从完整的网页中解析出来。数据解析主要用来实现聚焦爬虫。

我们知道，网页中显示的数据都是储存在 HTML 的标签或者标签的属性中。所以我们往往也是基于 HTML 的标签对数据进行解析提取。

数据解析的通用原理为：

- 通过定位找到指定的标签
- 取出标签中存储的数据或者标签属性中的数据

常用的数据解析方式有如下几种：

- 正则
- bs4
- xpath
- pyquery（自学）

## 爬取图片的两种方式

方式一，基于 requests 模块：

```python
import requests
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36'
}
url = 'https://tvax1.sinaimg.cn/large/bfe05ea9ly1fxgv7ihvvvj21hc0u07dc.jpg'    # 图片网上一大堆，如果这个链接失效了，就找一个新的吧
response = requests.get(url=url, headers=headers)
img_data = response.content    # content返回的是二进制形式的响应数据
with open('1.jpg', 'wb') as fp:
    fp.write(img_data)
```

方式二，基于 urllib 模块：

urllib 模块作用和 requests 模块一样，都是用来发送网络请求并接收响应的模块。当 requests 问世后就迅速的替代了 urllib 模块。requests 模块就是对 urllib 模块进行的封装。

```python
import urllib
url = 'https://tvax1.sinaimg.cn/large/bfe05ea9ly1fxgv7ihvvvj21hc0u07dc.jpg'
urllib.request.urlretrieve(url, '2.jpg')
```

通过比较，我们不难看出，这两种爬取图片的方式各有优劣：

- urllib 的代码更加简洁，但无法进行 UA 伪装
- requests 的代码稍繁琐些，但可以 UA 伪装

在以后的使用中，我们更多还是用到 requests 模块来爬取图片。毕竟很多网站都要进行 UA 检测。

## 正则解析

正则解析，也就是通过正则匹配的方式，将我们需要的数据从完整的网页文件中提取出来。

Python 的正则规则参见 [re 模块和正则表达式](..\python-basic\re.md)，就不在这里具体讨论了，仅在这里列举几个常见元字符：

```
单字符：
    . : 除换行以外所有字符，若匹配多行需指定re.DOTALL或re.S
    [] ：[aoe] [a-w] 匹配集合中任意一个字符
    \d ：数字  [0-9]
    \D : 非数字
    \w ：数字、字母、下划线、中文
    \W : 非\w
    \s ：所有的空白字符包,括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]。
    \S : 非空白
数量修饰：
    * : 任意多次  >=0
    + : 至少1次   >=1
    ? : 可有可无  0次或者1次
    {m} ：固定m次 hello{3,}
    {m,} ：至少m次
    {m,n} ：m-n次
边界：
    $ : 以某某结尾 
    ^ : 以某某开头
分组：
    (ab)  
贪婪模式： .*
非贪婪（惰性）模式： .*?
```

分析浏览器开发者工具中 Elements 和 network 这两个选项卡对应的页面源码数据有何不同之处？

- Elements 中包含的显示的页面源码数据为当前页面所有的数据加载完毕后对应的完整的页面源码数据（包含了动态加载数据）。Elements 很容易获得，一点击就到，而且可以很方便找到我们需要的标签。
- network 中显示的页面源码数据仅仅为某一个单独的请求对应的响应数据（不包含动态加载数据）
- 结论：如果在进行数据解析的时候，一定是需要对页面布局进行分析，如果当前网站**没有动态加载的数据**就可以直接使用 Elements 对页面布局进行分析，否则只可以使用 network 对页面数据进行分析。

接下来，我们要使用正则进行图片数据的批量解析爬取。

需求：爬取校花网中的图片数据

- 网址 url：www.521609.com
- 操作：需要将每一张图片的地址解析出来，然后对图片地址发起请求即可。

```python
dir_name = 'ImgLibs'
if not os.path.exists(dir_name):
    os.mkdir(dir_name)

# 1.捕获到当前首页的页面源码数据
main_url = 'http://www.521609.com/xiaoyuanmeinv/'
page_text = requests.get(url=main_url, headers=headers).text
# 2.从当前获取的页面源码数据中解析出图片地址
re_ex = '<li>.*?<img src="(.*?)" width=".*?</li>'
img_src_list = re.findall(re_ex, page_text, re.S)
for src in img_src_list:
    img_url = 'http://www.521609.com' + src
    img_name = dir_name + os.path.sep + src.split('/')[-1]
    urllib.request.urlretrieve(img_url, img_name)
    print(f'{img_name}下载完毕！')
```

## bs4 解析

bs4 解析代码编写流程：

1. 实例化一个 BeautifulSoup 的对象，将待解析的页面源码数据加载到该对象中
2. 调用 BeautifulSoup 对象中相关方法或者属性进行标签定位和文本数据的提取

环境安装（若使用 Anaconda，则不需要安装，因为 Anaconda 中已经集成了这两个模块）：

```bash
pip install lxml    # 可用作bs4的解析器，同时后面的xpath也会用到
pip install bs4
```

BeautifulSoup 对象的实例化：

BeautifulSoup 可以将本地的 HTML 文档或互联网请求来的页面进行解析操作

```python
BeautifulSoup(fp,'lxml')    # 用来将本地存储的html文档中的数据进行解析
BeautifulSoup(page_text，’lxml‘)    # 用来将互联网上请求到的页面源码数据进行解析
```

标签定位：

- `soup.tagName`：只可以定位到第一次出现的 tagName 标签
- `soup.find('tagName',attrName='value')`：属性定位，同样只能匹配到第一次出现的符合条件的标签
- `soup.findAll`：跟 find 一样用作属性定位，只不过 findAll 返回的是列表，列表中包含了所有符合条件的标签
- `soup.select('选择器')`：选择器定位，返回的是列表，列表中包含了所有符合条件的标签
  - 类选择器
  - id 选择器
  - 层级选择
    - 大于号 `>`：表示一个层级
    - 空格：表示多个层级

取数据

- `soup_obj.text`：返回的是该标签下所有的文本内容
- `soup_obj.string`：返回的是该标签直系的文本内容

取属性：

- `tag['attrName']`

我们可以将下面这串代码命名为 `test.html` 保存到当前工作目录下，供测试用：

```html
<html lang="en">
<head>
	<meta charset="UTF-8" />
	<title>测试bs4</title>
</head>
<body>
	<div>
		<p>百里守约</p>
	</div>
	<div class="song">
		<p>李清照</p>
		<p>王安石</p>
		<p>苏轼</p>
		<p>柳宗元</p>
		<a href="http://www.song.com/" title="赵匡胤" target="_self">
			<span>this is span</span>
		宋朝是最强大的王朝，不是军队的强大，而是经济很强大，国民都很有钱</a>
		<a href="" class="du">总为浮云能蔽日,长安不见使人愁</a>
		<img src="http://www.baidu.com/meinv.jpg" alt="" />
	</div>
	<div class="tang">
		<ul>
			<li><a href="http://www.baidu.com" title="qing">清明时节雨纷纷,路上行人欲断魂,借问酒家何处有,牧童遥指杏花村</a></li>
			<li><a href="http://www.163.com" title="qin">秦时明月汉时关,万里长征人未还,但使龙城飞将在,不教胡马度阴山</a></li>
			<li><a href="http://www.126.com" alt="qi">岐王宅里寻常见,崔九堂前几度闻,正是江南好风景,落花时节又逢君</a></li>
			<li><a href="http://www.sina.com" class="du">杜甫</a></li>
			<li><a href="http://www.dudu.com" class="du">杜牧</a></li>
			<li><b>杜小月</b></li>
			<li><i>度蜜月</i></li>
			<li><a href="http://www.haha.com" id="feng">凤凰台上凤凰游,凤去台空江自流,吴宫花草埋幽径,晋代衣冠成古丘</a></li>
		</ul>
	</div>
</body>
</html>
```

bs4 的使用示例：

```python
from bs4 import BeautifulSoup
fp = open('test.html', 'r', encoding='utf-8')
soup = BeautifulSoup(fp, 'lxml')
fp.close()
soup.p
soup.find('div', class_='song')
soup.find('a', id='feng')
soup.findAll('a', id='feng')
soup.select('.tang')
soup.select('.tang li')
a_tag = soup.find('a', id='feng')
a_tag['href']
div_tag = soup.find('div', class_='song')
div_tag.text
a_tag.string
```

接下来，我们利用 bs4 爬取网页中的文字信息。

需求：爬取《三国演义》全文

- 网址 url：http://www.shicimingju.com/book/sanguoyanyi.html
- 将每一章的内容爬取到，保存到一个本地文件中

```python
main_url = 'http://www.shicimingju.com/book/sanguoyanyi.html'
page_text = requests.get(url=main_url, headers=headers).text
fp = open('./sanguo.txt', 'w', encoding='utf-8')
# 数据解析：章节标题，详情页url，章节内容
soup = BeautifulSoup(page_text, 'lxml')
# 定位到的所有的符合要求的a标签
a_list = soup.select('.book-mulu > ul > li > a')
for a in a_list:
    title = a.string
    detail_url = 'http://www.shicimingju.com' + a['href']
    # 对详情页发起请求解析出章节内容
    page_text_detail = requests.get(url=detail_url, headers=headers).text
    soup = BeautifulSoup(page_text_detail, 'lxml')
    div_tag = soup.find('div', class_="chapter_content")
    content = div_tag.text
    fp.write(title + ':' + content + '\n')
    print(title, '保存成功！！！')
fp.close()
```

## xpath 解析

环境安装（同样，如果使用 Anaconda，则不需要安装）：

```bash
pip install lxml
```

xpath 解析原理：html 标签是以树状的形式进行展示

1. 实例化一个 etree 的对象，且将待解析的页面源码数据加载到该对象中
2. 调用 etree 对象的 xpath 方法结合着不同的 xpath 表达式实现标签的定位和数据提取

实例化 etree 对象

- `etree.parse('filename')`：将本地 html 文档加载到该对象中
- `etree.HTML(page_text)`：网站获取的页面数据加载到该对象

标签定位：

- 最左侧的 `/`：如果 xpath 表达式最左侧是以 `/` 开头则表示该 xpath 表达式一定要从根标签开始定位指定标签(用的很少，忽略)
- 非最左侧的 `/`：表示一个层级
- 非左侧的 `//`：表示多个层级
- 最左侧的 `//`：xpath 表达式可以从任意位置进行标签定位
- 属性定位：`tagName[@attrName="value"]`
- 索引定位：`tag[index]`，索引是从 1 开始
- 模糊匹配（了解）：
  - `//div[contains(@class, "ng")]`
  - `//div[starts-with(@class, "ta")]`

取文本

- `/text()`：直系文本内容
- `//text()`：所有的文本内容

取属性

- `/@attrName`

xpath 的使用示例如下：

```python
from lxml import etree
tree = etree.parse('test.html')
tree.xpath('/html/head/meta')    # 定位meta
tree.xpath('/html//meta')    # 定位meta
tree.xpath('//meta')    # 定位meta
tree.xpath('//div[@class="song"]/p')    # 定位class为song的div下面所有的p
tree.xpath('//div[@class="song"]/p[2]//text()')    # 定位class为song的div下面第2个p
tree.xpath('//a[@id="feng"]/@href')    # a标签的href属性
```

接下来，我们就用 xpath 爬取网页图片

需求：使用 xpath 爬取图片名称和图片数据

- 网址 url：http://pic.netbian.com/4kmeinv/
- 爬取前 5 页的所有图片

局部数据解析：
- 我们要将定位到的页面中的标签作为待解析的数据。再次使用 xpath 表达式解析待解析的数据。
- 在局部数据解析的时候，xpath 表达式中要使用 `./` 的操作，`./` 表示的就是当前的局部数据（xpath 的调用者）。

首先实现爬取第一页所有图片的功能：

```python
# 爬取第一页
img_dir = 'GirlsLib'
if not os.path.exists(img_dir):
    os.mkdir(img_dir)
main_url = 'http://pic.netbian.com/4kmeinv/'
response = requests.get(url=main_url, headers=headers)
response.encoding = 'gbk'
page_text = response.text
tree = etree.HTML(page_text)
# 找到所有符合要求的img标签
img_list = tree.xpath('//div[@class="slist"]//img')
for img in img_list:
    # print(type(img))
    # img的数据类型和tree的数据类型一样，img也可以调用xpath方法
    img_url = 'http://pic.netbian.com' + img.xpath('./@src')[0]
    title = img_dir + os.path.sep + img.xpath('./@alt')[0] + '.jpg'
    img_data = requests.get(url=img_url, headers=headers).content
    with open(title, 'wb') as fp:
        fp.write(img_data)
    print(title, '保存成功！')
```

分析页码规律，即可实现多页图片的爬取：

```python
# 爬取多页
img_dir = 'GirlsLib'
if not os.path.exists(img_dir):
    os.mkdir(img_dir)
main_url = 'http://pic.netbian.com/4kmeinv/'
for page in range(1, 6):
    # main_url不能修改，要保留，新的url要用新名字
    if page == 1:
        new_url = main_url
    else:
        new_url = main_url + f'index_{page}.html'
    response = requests.get(url=new_url, headers=headers)
    response.encoding = 'gbk'
    page_text = response.text
    tree = etree.HTML(page_text)
    # 找到所有符合要求的img标签
    img_list = tree.xpath('//div[@class="slist"]//img')
    for img in img_list:
        # print(type(img))
        # img的数据类型和tree的数据类型一样，img也可以调用xpath方法
        img_url = 'http://pic.netbian.com' + img.xpath('./@src')[0]
        title = img_dir + os.path.sep + img.xpath('./@alt')[0] + '.jpg'
        img_data = requests.get(url=img_url, headers=headers).content
        with open(title, 'wb') as fp:
            fp.write(img_data)
        print(title, '保存成功！')
```

## bs4 和 xpath 综合讨论

如果想要解析出携带 html 标签的局部数据，需要使用 bs4 还是 xpath 呢？

答案是 bs4。因为 bs4 在实现标签定位的时候返回的直接就是定位到标签对应的字符串数据，而 xpath 只能获取到标签对象。这也是选择用 bs4 还是 xpath 的一个重要依据。

如何让 xpath 的表达式更具有通用性？

可以通过在 xpath 表达式中使用管道符 `|`，表示管道符左右两侧的子 xpath 表达式同时生效或者一个生效。

需求：列举出所有城市名称

- 网址 url：https://www.aqistudy.cn/historydata/
- 列出包括热门城市和全部城市的所有城市名称

```python
url = 'https://www.aqistudy.cn/historydata/'
page_text = requests.get(url=url, headers=headers).text

tree = etree.HTML(page_text)
# hot_cities = tree.xpath('//div[@class="bottom"]/ul/li/a/text()')
# all_cities = tree.xpath('//div[@class="bottom"]/ul/div[2]/li/a/text()')
tree.xpath('//div[@class="bottom"]/ul/li/a/text() | //div[@class="bottom"]/ul/div[2]/li/a/text()')
```

## 作业，爬取站长素材高清图片和简历模板

1. 爬取站长素材高清图片，这里会涉及到一个反爬机制大家自行解决：http://sc.chinaz.com/tupian/
2. 爬取站长素材中的简历模板前 5 页：http://sc.chinaz.com/jianli/free.html

作业 1 参考：

```python
import json
import os
import requests
from lxml import etree

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36'
}
img_dir = 'ZhanzhangGirls'
if not os.path.exists(img_dir):
    os.mkdir(img_dir)
main_url = 'http://sc.chinaz.com/tupian/nvshengtupian.html'
response = requests.get(url=main_url, headers=headers)
response.encoding = 'utf-8'
page_text = response.text
tree = etree.HTML(page_text)
a_list = tree.xpath('//div[@id="container"]//p/a')
for a in a_list:
    detail_page_url = a.xpath('./@href')[0]
    detail_page_response = requests.get(url=detail_page_url, headers=headers)
    detail_page_response.encoding = 'utf-8'
    detail_page_tree = etree.HTML(detail_page_response.text)
    # 图片是js生成的，好在链接什么的都放在页面的json里面，可以拿到然后解析出来
    img_info_json = detail_page_tree.xpath('//script[@type="application/ld+json"]//text()')[0]
    img_info = json.loads(img_info_json)
    img_url = img_info['images'][0]
    img_title = os.path.join(img_dir, img_info['description'] + '.jpg')
    img_data = requests.get(url=img_url, headers=headers).content
    with open(img_title, 'wb') as fp:
        fp.write(img_data)
    print(img_title, '下载完成！')
```

作业 2 参考：

```python
import json
import os
import requests
from bs4 import BeautifulSoup
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36'
}
file_dir = 'ZhanzhangCV'
if not os.path.exists(file_dir):
    os.mkdir(file_dir)
main_url = 'http://sc.chinaz.com/jianli/free.html'
response = requests.get(url=main_url, headers=headers)
response.encoding = 'utf-8'
page_text = response.text
soup = BeautifulSoup(page_text, 'lxml')
a_list = soup.select('#container p > a')
for a in a_list:
    detail_url = a['href']
    title = a.text
    detail_response = requests.get(url=detail_url, headers=headers)
    detail_response.encoding = 'utf-8'
    detail_soup = BeautifulSoup(detail_response.text, 'lxml')
    file_url = detail_soup.select('.downlist li a')[0]['href']
    file_name = os.path.join(file_dir, title + '.rar')
    file_data = requests.get(url=file_url, headers=headers).content
    with open(file_name, 'wb') as fp:
        fp.write(file_data)
    print(title, '下载完毕！')
```

### 图片懒加载

图片懒加载常见于一些图片比较多的网站。懒加载的目的不单单是为了反爬，它可以减少用户打开网页所需要的时间，提高用户体验。不过这也确实给我们爬虫造成一些困扰。

图片懒加载的解决的解决要灵活处理，根据网页实现懒加载方式的不同，会有不同的处理方式。

比如站长素材的缩略图，通过将 img 标签的 src 属性先命名为 src2。等图片出现到浏览器视野中的时候，src2 会被更名回 src，从而实现图片的懒加载。

但是对于 requests 请求来说，并不会有图片出现在浏览器视野中，所以图片的 src 属性全都为 src2。此时如果还用 src 属性查找图片链接，是找不到的，应该使用 src 2 来查找。

```python
import os
import requests
from lxml import etree
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36'
}
img_dir = 'ZhanzhangGirls'
if not os.path.exists(img_dir):
    os.mkdir(img_dir)
url = 'http://sc.chinaz.com/tag_tupian/YaZhouMeiNv.html'
response = requests.get(url=url, headers=headers)
response.encoding = 'utf-8'
data_text = response.text
tree = etree.HTML(data_text)
img_list = tree.xpath('//div[@id="container"]//img')
for img in img_list:
    title = os.path.join(img_dir, img.xpath('./@alt')[0] + '.jpg')
    # 去掉链接中的 _s 即可获得高清图片
    img_url = img.xpath('./@src2')[0][:-6] + '.jpg'
    img_data = requests.get(url=img_url, headers=headers).content
    with open(title, 'wb') as fp:
        fp.write(img_data)
    print(title, '下载完成！')
```

