## 增量式

对于我们前面的那些爬虫方法，如果我们之前爬取过某个网站，下次再启动工程，还是会从头爬取。即便我们之前爬取过这个网站的很多数据，但是我们还是会对这些爬取过的数据重复爬取。为了减少这种重复爬取的操作，让程序更加集中运行在我们没有爬取过的，新出现的网页中，从而提高爬取效率。

增量式爬虫的概念：监测网站数据更新的情况，以便于爬取到最新更新出来的数据。

实现增量式的核心是要**去重**。

实战中去重的方式：记录表。

记录表需要记录的一定是爬取过的相关信息，能够唯一标识爬取过的任务。

爬取过的相关信息通常指的是详情页的 url。当然只要某一组数据，该组数据如果可以作为该部电影的唯一标识即可，刚好详情页的 url 往往就可以作为任务单元的唯一标识。只要可以表示任务单元唯一标识的数据我们统称为**数据指纹**。

去重的方式对应的记录表应该使用什么数据结构呢？
- python 中的 set 集合是不太合适的，因为 set 集合不方便进行持久化存储
- redis 中的 set 则可以胜任我们的记录表，因为它可以持久化存储数据

数据指纹一般是经过加密处理的。数据量不是很大的数据指纹没有必要加密。如果数据的唯一标识标识的内容数据量比较大，可以使用 hash 函数将数据加密成 32 位的密文。

给数据指纹加密目的是为了节省空间。

接下来，我们就以 2345 电影网为例，看看如何实现增量式爬虫。

需求：使用增量式爬虫实现 2345 电影网的电影标题和电影细节描述的爬取。

网址 url：<https://www.4567kan.com/index.php/vod/show/class/动作/id/1.html>

在爬虫源文件中，大部分代码和普通的爬虫是一样的，只是多了一步将数据指纹存放到 redis 集合中的操作。这里的数据指纹是电影的 url。如果 url 在集合中，添加不成功，返回的是 0。这意味着，之前已经爬取过这部电影了，无需再次爬取。如果集合中没有这部电影的 url，则说明这部电影还没有爬取，就要爬取这个链接后，获取电影的详细信息。这就很简单了，一步简单判断即可实现：

```python
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
from redis import Redis
from zlsPro.items import ZlsproItem

class ZlsSpider(CrawlSpider):
    name = 'zls'
    # allowed_domains = ['www.4567kan.com/index.php/vod/show/class/动作/id/1.html']
    start_urls = ['http://www.4567kan.com/index.php/vod/show/class/动作/id/1.html/']
    # 建立redis连接
    conn = Redis('127.0.0.1', 6379)

    rules = (
        Rule(LinkExtractor(allow=r'page/\d+\.html'), callback='parse_item', follow=True),
    )

    def parse_item(self, response):
        li_list = response.xpath('//ul[@class="stui-vodlist clearfix"]/li')
        for li in li_list:
            url = 'https://www.4567kan.com' + li.xpath('./div/a/@href').extract_first()
            title = li.xpath('./div/a/@title').extract_first()
            ex = self.conn.sadd('movie_urls', url)
            # ex==1插入成功，ex==0插入失败
            if ex:     # detail_url表示的电影没有存在于记录表中
                item = ZlsproItem()
                item['title'] = title
                # 爬取电影数据：发起请求
                print(f'有数据更新，电影《{title}》正在爬取中...')
                yield scrapy.Request(url, callback=self.parse_detail, meta={'item': item})
            else:
                # 这个链接已经存在于记录表中，不需要爬取
                print('这部电影已经爬过了，不需要再爬了...')
    def parse_detail(self, response):
        # 解析电影简介
        desc = response.xpath('//span[@class="detail-content"]/text()').extract_first()
        if not desc:
            desc = response.xpath('//span[@class="detail-sketch"]/text()').extract_first()
        item = response.meta['item']
        item['desc'] = desc
        yield item
```

在管道中，我们可以将数据以任意形式存储，这里将数据存储到 redis 中：

```python
class ZlsproPipeline(object):
    def process_item(self, item, spider):
        # spider即爬虫对象，conn是redis连接
        conn = spider.conn
        conn.lpush('movieData', item)
        return item
```

其他如 `settings.py` 中的配置和 items 的配置就不介绍了吧。