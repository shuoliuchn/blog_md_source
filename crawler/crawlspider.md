## CrawlSpider 的基本使用

[TOC]

### CrawlSpider 的基本用法

CrawlSpider 是 Spider 的一个子类。Spider 是爬虫文件中爬虫类的父类。

一般来讲，子类的功能要比父类多，所以 CrawlSpider 的功能是比 Spider 更完善更强大的。

CrawlSpider 的作用：常被用作于专业实现全站数据爬取，也就是将一个页面下所有页码对应的数据进行爬取。

CrawlSpider 的基本使用：
1. 创建一个工程

2. cd 到这个工程根目录

3. 创建一个基于 CrawlSpider 的爬虫文件

    ```bash
    scrapy genspider -t crawl SpiderName www.xxx.com
    ```

4. 执行工程

注意：

1. 一个链接提取器对应一个规则解析器（多个链接提取器和多个规则解析器）
2. 在实现深度爬取的过程中需要和 `scrapy.Request()` 结合使用

需求：爬取校花网图片

网址 url：http://www.521609.com/daxuexiaohua/

示例代码：

```python
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule

class FirstSpider(CrawlSpider):
    name = 'first'
    # allowed_domains = ['www.521609.com']
    start_urls = ['http://www.521609.com/daxuexiaohua/']

    # 实例化LinkExtractor对象
    # 链接提取器：根据指定规则（allow参数）在页面中进行连接（url）的提取
    # allow='正则'：提取链接的规则
    # link = LinkExtractor(allow=r'list3\d+\.html')
    link = LinkExtractor(allow=r'') #取出网站全站的链接
    rules = (
        # 实例化一个Rule对象
        # 规则解析器：接收链接提取器提取到的链接，对其发起请求，然后根据指定规则（callback）解析数据
        Rule(link, callback='parse_item', follow=True),
    )
    # follow=True:
    # 将链接提取器 继续作用到 连接提取器提取到的页码 所对应的 页面中
    def parse_item(self, response):
        print(response)
        # 基于response实现数据解析
```

问：如何将一个网站中全站所有的链接都进行爬取。

答：把正交规则写成空字符串 `''` 即可。如果仅要爬取本站所有内容，需要设置 allowed_domains 为当前域名，避免爬取到其他网站。

### 使用 CrawlSpider 实现深度爬取

需求：爬取阳光热线问政平台最新问政的问政标题、状态和问政详细信息。

网址 url：http://wz.sun0769.com/political/index/politicsNewest?id=1&type=4&page=

分析：问政标题和状态均可在首页得到，但是详细信息需要爬取详情页内容。使用 crawl 确实可以获取到每一个详情页的网址，并获取详细信息，但是如果要将首页的问政标题和状态与详情页的详细信息匹配，需要下一番功夫。（事实上，我们可以在详情页拿到所有这三个信息，但是为了学习和训练，我们分别从首页和详情页抓取。）

我们可以使用问政编号，作为数据的标识。如此一来，可以将首页内容和详情页内容一一对应了。

基于上面的思路，我们可以编写出爬虫的源代码：

```python
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
from sunPro.items import SunproItem, SunproDeatilItem


class SunSpider(CrawlSpider):
    name = 'sun'
    # allowed_domains = ['www.xxx.com']
    start_urls = ['http://wz.sun0769.com/political/index/politicsNewest?id=1&type=4&page=']

    rules = (
        # 提取页码链接，解析每一个页码对应页面中的数据
        Rule(LinkExtractor(allow=r'id=1&page=\d+'), callback='parse_item', follow=True),
        # 提取详情页连接，解析详情页中的数据
        Rule(LinkExtractor(allow=r'index\?id=\d+'), callback='parse_detail', follow=False),
    )

    def parse_item(self, response):
        """首页，用来提取标题和状态"""
        li_list = response.xpath('//ul[@class="title-state-ul"]/li')
        for li in li_list:
            item = SunproItem()
            qid = li.xpath('./span[1]/text()').extract_first().strip()
            status = li.xpath('./span[2]/text()').extract_first().strip()
            title = li.xpath('./span[3]/a/text()').extract_first().strip()
            item['qid'] = qid
            item['status'] = status
            item['title'] = title
            yield item

    # 实现深度爬取：爬取详情页中的数据
    # 1.对详情页的url进行捕获
    # 2.对详情页的url发起请求获取数据
    def parse_detail(self, response):
        """详情页，用来提取详细信息，实现深度爬取"""
        qid = response.xpath('//div[@class="mr-three"]/div[1]/span[4]/text()').extract_first().replace('编号：', '').strip()
        content = response.xpath('//div[@class="mr-three"]/div[2]/pre/text()').extract_first().strip()
        item = SunproDeatilItem()
        item['qid'] = qid
        item['content'] = content
        yield item
```

数据通过问政 id 进行标识，数据存储的时候，相同的问政 id 保存在一起就好。

别忘了在 `items.py` 中写好两个 Item 类，SunproItem 和 SunproDetailItem。

但是现在还有一个问题：首页和详情页解析出来的数据统统给了管道。但是这两种数据是不同的，处理方式也是有差异的。我们该如何在管道中区分这两种数据，并对其进行分别存储呢？

很简单，这两个 item 是不同类的实例化对象，我们只需要通过查询类名，即可对两种数据进行区分。item 类名的查询方式为：

```python
item.__class__.__name__
```

通过判断类名，分别处理，根据 qid 进行一一对应存储，我这里就不详细写了：

```python
class SunproPipeline(object):
    def process_item(self, item, spider):
        """数据持久化存储，根据qid进行一一对应，就不详细写了"""
        qid = item['qid']
        if item.__class__.__name__ == 'SunproItem':
            title = item['title']
            status = item['status']
            print(qid, title, status)
        else:
            content = item['content']
            print(qid, content)
        return item
```

### CrawlSpider 结合 Spider 实现深度爬取

像上面那种，单独使用 CrawlSpider 确实能够实现深度爬取。但是我们发现，坑一个接着一个：首页和详情页的数据不容易一一对应，两个不同的 item 对象传入管道，携带的数据和数据处理方式也不同，需要区别对待。

链接获取和发送请求的过程的确是节省了，但是又多出了许多其他的麻烦，有些得不偿失。所以对于深度爬取的任务，我们更倾向于使用的方法是，通过 CrawlSpider 与 `spider.Request` 结合使用的方式，实现深度爬取。

这就很容易处理了，手动请求，加上请求参数传递即可实现：

```python
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
from sunPro.items import SunproItem, SunproDeatilItem

class SunSpider(CrawlSpider):
    name = 'sun'
    # allowed_domains = ['www.xxx.com']
    start_urls = ['http://wz.sun0769.com/political/index/politicsNewest?id=1&type=4&page=']

    rules = (
        # 提取页码链接，解析每一个页码对应页面中的数据
        Rule(LinkExtractor(allow=r'id=1&page=\d+'), callback='parse_item', follow=False),
        # # 提取详情页连接，解析详情页中的数据
        # Rule(LinkExtractor(allow=r'index\?id=\d+'), callback='parse_detail', follow=False),
    )

    def parse_item(self, response):
        """首页，用来提取标题和状态"""
        li_list = response.xpath('//ul[@class="title-state-ul"]/li')
        for li in li_list:
            item = SunproItem()
            qid = li.xpath('./span[1]/text()').extract_first().strip()
            status = li.xpath('./span[2]/text()').extract_first().strip()
            title = li.xpath('./span[3]/a/text()').extract_first().strip()
            url = 'http://wz.sun0769.com/' + li.xpath('./span[3]/a/@href').extract_first().strip()
            item['qid'] = qid
            item['status'] = status
            item['title'] = title
            yield scrapy.Request(url, callback=self.parse_detail, meta={'item': item})

    def parse_detail(self, response):
        item = response.meta['item']
        qid = response.xpath('//div[@class="mr-three"]/div[1]/span[4]/text()').extract_first().replace('编号：', '').strip()
        content = response.xpath('//div[@class="mr-three"]/div[2]/pre/text()').extract_first().strip()
        item['qid'] = qid
        item['content'] = content
        yield item
```

这样，只会有一种 item 对象，里面是我们需要的所有数据。在管道中，不需要分类处理，不需要考虑首页和详情页一一对应的问题。一切都变得很简单了。