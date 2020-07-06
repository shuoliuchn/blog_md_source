## 分布式

[TOC]

### 分布式爬虫概述

分布式爬虫，是一种能够将爬虫效率发挥到极致的爬虫方法。

实现方式：scrapy + redis（完整说法是 scrapy 结合着 scrapy-redis 组件）

原生的 scrapy 框架是无法实现分布式的。

什么是是分布式？
- 分布式就是搭建一个分布式的机群，然后让机群中的每一台电脑执行同一组程序，让其对同一组资源
    进行联合且分布的数据爬取。

为什么原生的 scrapy 框架无法实现分布式？
- 调度器无法被分布式机群共享
- 管道无法分布式机群被共享

如何实现分布式？

- 使用scrapy-redis组件即可

scrapy-redis 组件的作用是，可以给原生的 scrapy 框架提供共享的管道和调度器。

scrapy-redis 的安装：

```bash
pip install scrapy-redis
```

### 分布式爬虫的实现流程

#### 修改爬虫文件

导包方式：

```python
from scrapy_redis.spiders import RedisCrawlSpider
```

然后修改当前爬虫类的父类为 RedisCrawlSpider，将 start_url 替换成 redis_keys 的属性，属性值为任意字符串，比如：

```python
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
from scrapy_redis.spiders import RedisCrawlSpider

class FbsSpider(RedisCrawlSpider):
    name = 'fbs'
    # allowed_domains = ['www.xxx.com']
    # start_urls = ['http://www.xxx.com/']
    redis_key = 'sunQueue'    # 可以被共享的调度器队列的名称，可以随意取
```

`redis_key = 'xxx'` 表示的是可以被共享的调度器队列的名称，最终是需要将起始的 url 手动放置到 redis_key 表示的队列中。

剩下的，就跟正常解析数据一样了。

#### 配置 `settings.py` 文件

指定调度器。增加一个去重容器类的配置，作用是使用 Redis 的 set 集合来存储请求的指纹数据，从而实现请求去重的持久化。

```python
DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"
```

使用 scrapy-redis 组件自己的调度器。

```python
SCHEDULER = "scrapy_redis.scheduler.Scheduler"
```

配置调度器是否要持久化。也就是当爬虫结束了，要不要清空 Redis 中请求队列和去重指纹的 set。如果是 True, 就表示要持久化存储，就不清空数据，否则清空数据。

```python
SCHEDULER_PERSIST = True
```

指定管道。

```python
ITEM_PIPELINES = {
    'scrapy_redis.pipelines.RedisPipeline': 400
}
```

特点：该种管道只可以将 item 写入 redis。

指定 redis。

```python
REDIS_HOST = 'redis服务的ip地址'
REDIS_PORT = 6379
```

#### 配置 redis 的配置文件

Windows 中是 redis 安装目录下的 `redis.window.conf` 文件。

解除默认绑定，注释掉第 56 行的绑定 IP 的代码

```ini
#bind 127.0.0.1
```

关闭保护模式，将第 75 行的 protected-mode 设置成 no

```ini
protected-mode no
```

#### 启动 redis 服务和客户端

进入 redis 安装路径，执行命令启动 redis。

#### 执行 scrapy 工程

```bash
scrapy crawl fbs
```

注意，执行 scrapy 工程时，不要在配置文件中加入 LOG_LEVEL。我们需要分析这些日志信息。

工程启动后，程序会停留在 listening 位置，等待起始的 url 加入。

#### 向 redis_key 表示的队列中添加起始 url

需要在 redis 的客户端执行如下指令（调度器队列是存在于 redis 中）：

```redis
lpush sunQueue http://wz.sun0769.com/political/index/politicsNewest?id=1&page=1
```

### 分布式爬虫实例

需求：使用分布式爬虫爬取阳光问政平台全站的问政标题和状态

网址 url：http://wz.sun0769.com/political/index/politicsNewest?id=1&page=1

分析：分布式爬虫和普通爬虫的核心数据解析代码是一致的，只需要稍加修改，再进行一些配置，即可实现分布式。

爬虫源文件的写法：

```python
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
from scrapy_redis.spiders import RedisCrawlSpider
from fbsPro.items import FbsproItem


class FbsSpider(RedisCrawlSpider):
    name = 'fbs'
    # allowed_domains = ['www.xxx.com']
    # start_urls = ['http://www.xxx.com/']
    redis_key = 'sunQueue'    # 可以被共享的调度器队列的名称，可以随意取
    # 稍后我们是需要将一个起始的url手动的添加到redis_key表示的队列中
    rules = (
        Rule(LinkExtractor(allow=r'id=1&page=\d+'), callback='parse_item', follow=True),
    )

    def parse_item(self, response):
        li_list = response.xpath('//ul[@class="title-state-ul"]/li')
        for li in li_list:
            status = li.xpath('./span[2]/text()').extract_first().strip()
            title = li.xpath('./span[3]/a/text()').extract_first().strip()
            item = FbsproItem()
            item['status'] = status
            item['title'] = title
            yield item
```

然后安装前面所说的，配置 settings.py 即可。