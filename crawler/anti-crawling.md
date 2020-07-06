## 常见的反爬机制以及相应的反反爬策略

道高一尺，魔高一丈。互联网上的爬虫、反爬与反反爬之间的博弈堪称互联网中的壮举。

本文记录集中常见的反爬机制，与响应的反反爬策略，仅供学习交流使用。万不可用来做违法乱纪的勾当。

那我们就开始吧。

### robots 协议，防君子不防小人的反爬机制

- robots 协议：
  - 是一个纯文本的协议，协议中规定了该网站中哪些数据可以被哪些爬虫爬取，哪些不可以被爬取
  - 一般在网站的门户页面后面加上 `/robots.txt` 即可查看该网站的 robots 协议
- 破解：
  - 你自己主观性地不遵从该协议即可。

### UA 检测和 UA 伪装

UA 检测：网站后台会检测请求对应的 User-Agent，以判定当前请求是否为异常请求。

UA 检测对应的反反爬策略是 UA 伪装：我们使用一个浏览器的 User-Agent，而不是爬虫的，去访问网页。

伪装流程：

- 使用抓包工具捕获到某一个基于浏览器请求的 User-Agent 的值，将其伪装作用到一个字典中，将该字典作用到请求方法（get，post）的 headers 参数中即可。
- 因为 UA 检测机制很多网站都会有，所以一般我们写爬虫代码的时候，都会加上 User-Agent 请求头，有备无患

使用代码表示就是：

```python
keyword = input('enter a keyword:')
headers = {
    'User-Agent':'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36'
}
params = {
    'query':keyword
}
url = 'https://www.sogou.com/web'
# 实现了UA伪装
response = requests.get(url=url, params=params, headers=headers)
response.encoding = 'utf-8'
page_text = response.text
fileName = keyWord + '.html'
with open(fileName, 'w', encoding='utf-8') as fp:
    fp.write(page_text)
print(fileName, '爬取完毕！！！')
```

### 页面动态加载

有时候，直接通过 get 请求到的网址并不包含我们想要的数据。数据是通过 ajax 等方法，异步请求得到的。这时候，如果我们直接使用聚焦爬虫，分析一整个页面的内容，是无法获取到数据信息的。

应付页面动态加载的方式也很简单，因为数据一定是通过请求获得的，所以只要我们捕获到浏览器向后端请求数据所用的各种参数，就可以得到我们需要的数据。

比如豆瓣电影的页面就是动态加载的，数据通过 ajax 请求获得。要爬取豆瓣电影的信息，只需要用浏览器的抓包工具，找到这个请求，然后把需要的参数都带上，即可实现。

```python
url = 'https://movie.douban.com/j/chart/top_list?type=5&interval_id=100%3A90&action=&start=0&limit=20'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36'
}
# 注意data的键值都写成字符串形式，没有值的话，就写成空字符串
data = {
    'type': '5',
    'interval_id': '100:90',
    'action': '',
    'start': '0',
    'limit': '20',
}
response = requests.get(url=url, headers=headers, data=data)
# .json()将获取的字符串形式的json数据序列化成字典或者列表对象
data_list = response.json()
#解析出电影的名称+评分
for movie in data_list:
    title = movie['title']
    score = movie['score']
    print(title, score)
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

