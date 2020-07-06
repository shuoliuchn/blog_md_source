## scrapy 高级用法

[TOC]

### scrapy 的五大核心组件

学习 scrapy 核心组件的目的：

1. 大概了解 scrapy 的运行机制

2. 为我们后面学习分布式爬虫做铺垫

五大核心组件及其作用：

-  引擎（Scrapy）：用来处理整个系统的数据流，触发事务（框架核心）
- 调度器（Scheduler）：用来接收引擎发过来的请求，压入队列中，并在引擎再次请求的时候返回。可以想像成一个 URL（抓取网页的网址或者说是链接）的优先队列，由它来决定下一个要抓取的网址是什么，同时去除重复的网址。事实上，调度器可分为两个部分：过滤器和队列。过滤器用来将请求去重，队列用来调整网址抓取的顺序
- 下载器（Downloader）：用于下载网页内容, 并将网页内容返回给蜘蛛引擎（Scrapy 下载器是建立在 twisted 这个高效的异步模型上的）
- 爬虫（Spiders）：爬虫是主要干活的，用于从特定的网页中提取自己需要的信息，即所谓的实体（Item）。用户也可以从中提取出链接，让 Scrapy 继续抓取下一个页面
- 项目管道（Pipeline）：负责处理爬虫从网页中抽取的实体（Item），主要的功能是持久化实体、验证实体的有效性、清除不需要的信息。当页面被爬虫解析后，将被发送到项目管道，并经过几个特定的次序处理数据。

![scrapy-architecture](scrapy-advanced.assets/scrapy-architecture.png)

如上图所示，scrapy 五大核心组件的请求调度流程为：

1. 请求最先从爬虫中发出，发送给引擎。
2. 引擎将请求交给调度器。
3. 调度器分为两部分，过滤器和队列。请求经过滤器进行去重后，交给队列。调度器将按照队列中元素的次序，将请求返回给引擎。
4. 引擎将调度好的请求交给下载器下载资源。
5. 下载器从网络中把资源下载好后，把响应结果交给引擎。
6. 引擎把响应结果交给爬虫，进行数据解析
7. 爬虫将解析好的数据放到实体（Item）中，交给引擎
8. 引擎根据爬虫返回的结果进行不同的处理。如果返回的是实体对象，则交给管道进行数据持久化操作；如果返回的是请求对象，则交给调度器，重复步骤 2。

### 请求传参实现深度爬取

深度爬取指的爬取的数据没有在同一张页面中，比如首页数据和详情页数据。

在 scrapy 中如果没有请求传参我们是无法持久化存储数据的。

请求传参的实现方式：

```python
scrapy.Request(url, callback, meta)
```

meta 是一个字典，可以将 meta 传递给 callback。我们可以在回调函数 callback 中取出 meta：

```python
response.meta
```

需求：爬取 2345 电影网电影前五页中所有的电影标题和电影简介信息

网址 url：https://www.4567kan.com/index.php/vod/show/id/5.html

分析：首页只能看见电影标题，电影简介要在电影的详情页才能看见。这就涉及了网页的深度爬取，需要结合请求传参来实现。

爬虫源文件的写法为：

```python
import scrapy
from moviePro.items import MovieproItem


class MovieSpider(scrapy.Spider):
    name = 'movie'
    # allowed_domains = ['www.xxx.com']
    start_urls = ['https://www.4567kan.com/index.php/vod/show/id/5.html']
    url = 'https://www.4567kan.com/index.php/vod/show/id/5/page/%s.html'
    page = 2

    def parse(self, response):
        li_list = response.xpath('//ul[@class="stui-vodlist clearfix"]/li')
        for li in li_list:
            url = 'https://www.4567kan.com' + li.xpath('./div/a/@href').extract_first()
            name = li.xpath('./div/a/@title').extract_first()
            item = MovieproItem()
            item['name'] = name
            # 对详情页url发起请求
            # meta作用：可以将meta字典传递给callback
            yield scrapy.Request(url, self.parse_detail, meta={'item': item})
        if self.page <= 5:
            new_url = self.url % self.page
            self.page += 1
            yield scrapy.Request(new_url, self.parse)

    # 被用作于解析详情页的数据
    def parse_detail(self, response):
        desc = response.xpath('//span[@class="detail-content"]/text()').extract_first()
        if not desc:
            desc = response.xpath('//span[@class="detail-sketch"]/text()').extract_first()
        # 接收传递过来的meta
        item = response.meta['item']
        item['desc'] = desc
        yield item
```

settings 里面要配置 UA 伪装，要在 items 里面写好字段，还要再管道中写好数据持久化代码，这里就不列举了。

### 中间件

在 Django 中我们已经学习过中间件了，scrapy 的中间件与 Django 的中间件类似，也是用来批量拦截处理请求和响应。

scrapy 的中间件有两种：

- 爬虫中间件

- 下载中间件（推荐）

爬虫中间件和下在中间件的作用是类似的。稍微有点区别是，下在中间件处理的请求是经过调度器调度去重了的。

通过中间件，我们可以在三个方面进行处理：拦截请求、拦截响应和拦截异常的请求

拦截请求可以做到：

- 篡改请求 url（可以，但是没必要）
- 伪装请求头信息（通常在 settings 里面配置）
    - UA 伪装
    - Cookie 伪装

拦截响应可以用来：

- 篡改响应数据（一般不这么做）

拦截异常的请求通常用来：

- 代理操作必须使用中间件才可以实现（重点）

  ```python
  process_exception:
      request.meta['proxy'] = 'http://ip:port'
  ```

中间件的使用示例：

```python
class MiddleproDownloaderMiddleware(object):
    # 拦截所有（正常&异常）的请求
    # 参数：request就是拦截到的请求，spider就是爬虫类实例化的对象
    def process_request(self, request, spider):
        print('process_request()')
        request.headers['User-Agent'] = 'xxx'
        # request.headers['Cookie'] = 'xxxxx'
        return None #or request
    # 拦截所有的响应对象
    # 参数：response拦截到的响应对象，request响应对象对应的请求对象
    def process_response(self, request, response, spider):
        print('process_response()')
        return response
    # 拦截异常的请求
    # 参数：request就是拦截到的发生异常的请求
    # 作用：想要将异常的请求进行修正，将其变成正常的请求，然后对其进行重新发送
    def process_exception(self, request, exception, spider):
        # 如果请求的ip被禁掉，该请求就会变成一个异常的请求
        request.meta['proxy'] = 'http://ip:port' #设置代理
        print('process_exception()')
        return request #将异常的请求修正后将其进行重新发送
```

不要忘了在 settings 中，把下载中间件的代码取消注释：

```python
DOWNLOADER_MIDDLEWARES = {
   'middlePro.middlewares.MiddleproDownloaderMiddleware': 543,
}
```

### 大文件（图片视频等）下载

大文件数据是在管道中请求到的。下载管道类是 scrapy 封装好的我们直接用即可：

```python
from scrapy.pipelines.images import ImagesPipeline    # 提供了数据下载功能
```

创建一个管道类，继承自 ImagesPipeline（类似地，还有 MediaPipeline 和 FilePipeline，用法大同小异），重写该管道类的三个方法：

- get_media_requests：对图片地址发起请求
- file_path：返回图片名称即可
- item_completed：返回 item，将其返回给下一个即将被执行的管道类

在配置文件中指定文件下载后存放的文件夹：

```python
IMAGES_STORE = 'dirName'
```

需求：使用 scrapy 爬取校花网的图片

网址 url：http://www.521609.com/daxuexiaohua/

管道类代码：

```python
# 该默认管道无法帮助我们请求图片数据，因此该管道我们就不用
# class ImgproPipeline(object):
#     def process_item(self, item, spider):
#         return item

# 管道需要接受item中的图片地址和名称，然后再管道中请求到图片的数据对其进行持久化存储
from scrapy.pipelines.images import ImagesPipeline    # 提供了数据下载功能
# from scrapy.pipelines.media import MediaPipeline
# from scrapy.pipelines.files import FilesPipeline
import scrapy
class ImgsPipiLine(ImagesPipeline):
    # 根据图片地址发起请求
    def get_media_requests(self, item, info):
        # print(item)
        yield scrapy.Request(url=item['src'],meta={'item':item})
    # 返回图片名称即可
    def file_path(self, request, response=None, info=None):
        # 通过request获取meta
        item = request.meta['item']
        filePath = item['name']
        return filePath    # 只需要返回图片名称
    # 将item传递给下一个即将被执行的管道类
    def item_completed(self, results, item, info):
        return item
```

爬虫源文件代码：

```python
import scrapy
from imgPro.items import ImgproItem

class ImgSpider(scrapy.Spider):
    name = 'img'
    # allowed_domains = ['www.xxx.com']
    start_urls = ['http://www.521609.com/daxuexiaohua/']

    def parse(self, response):
        li_list = response.xpath('//div[@id="content"]/div[2]/div[2]/ul/li')
        for li in li_list:
            name = li.xpath('./a/img/@alt').extract_first() + '.jpg'
            src = 'http://www.521609.com' + li.xpath('./a/img/@src').extract_first()
            item = ImgproItem()
            item['name'] = name
            item['src'] = src
            yield item
```

items 和 settings 也要进行常规配置，就不一一列举了。

### settings.py 中的常用配置

增加并发。默认 scrapy 开启的并发线程为 32 个，可以适当进行增加。在 settings 配置文件中修改 CONCURRENT_REQUESTS 的值即可：

```python
CONCURRENT_REQUESTS = 100
```

降低日志级别。在运行 scrapy 时，会有大量日志信息的输出，为了减少 CPU 的使用率，可以设置 log 输出信息为 INFO 或者 ERROR。在配置文件中编写：

```python
LOG_LEVEL = 'INFO'
# 或者
LOG_LEVEL = 'ERROR'
```

禁止 cookie。如果不是真的需要 cookie，则在 scrapy 爬取数据时可以禁止 cookie 从而减少 CPU 的使用率，提升爬取效率。在配置文件中编写（如果要启用 cookie，将这个值改成 True 即可）：

```python
COOKIES_ENABLED = False
```

禁止重试。对失败的 HTTP 进行重新请求（重试）会减慢爬取速度，因此可以禁止重试。在配置文件中编写：

```python
RETRY_ENABLED = False
```

减少下载超时。如果对一个非常慢的链接进行爬取，减少下载超时可以能让卡住的链接快速被放弃，从而提升效率。在配置文件中进行编写：

```python
DOWNLOAD_TIMEOUT = 10    # 超时时间为10s
```

