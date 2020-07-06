## selenium 在 scrapy 中的使用

一直以来，我们都是直接使用 scrapy 框架的 Request 模块进行网页数据的请求。但是如果网页中有动态加载的数据，这种方式就不容易实现了。

其实 scrapy 更多的处理的还是没有动态加载数据的页面。对于动态加载的页面，我们还是比较倾向于使用 requests。

但是如果真的有这么个需求，需要我们使用 scrapy 爬取动态页面的话，通过 selenium 发送请求获取数据，将会是一个不错的选择。

接下来，我们通过爬取网易新闻，来演示如何在 scrapy 中，使用 selenium 爬取数据。

需求：爬取网易新闻中的国内，国际，军事，航空，无人机这五个板块下所有的新闻数据（标题+内容）

网址 url：https://news.163.com/

分析：

- 首页没有动态加载的数据，可以直接爬取到五个板块对应的 url
- 每一个板块对应的页面中的新闻标题是动态加载，需要使用 selenium 爬取新闻标题和详情页的 url（关键）
- 每一条新闻详情页面中的数据不是动态加载，在这里可以爬取到新闻内容

selenium 在 scrapy 中的使用流程
1. 在爬虫类中实例化一个浏览器对象，将其作为爬虫类的一个属性
2. 在中间件中实现浏览器自动化相关的操作
3. 在爬虫类中重写 `closed(self, spider)` 方法，在其内部关闭浏览器对象

接下来，我们就按照流程，依次编写我们的代码。

首先，编写爬虫源文件中的代码，一切都按照正常思路走就行。先从首页到每个模块页，在模块页中拿到新闻的标题和详情页的 url。再从模块页进入到详情页，拿到文章内容。

用代码表示就是：

```python
import scrapy
from selenium import webdriver
from wangyiPro.items import WangyiproItem

class WangyiSpider(scrapy.Spider):
    name = 'wangyi'
    # allowed_domains = ['news.163.com']
    start_urls = ['http://news.163.com/']
    module_urls = []
    # 实例化了一个全局的浏览器对象，稍后在中间件中会用到
    bro = webdriver.Chrome()

    def parse(self, response):
        # 国内，国际，军事，航空，无人机这五个板块的索引
        target_list = [3, 4, 6, 7, 8]
        li_list = response.xpath('//div[@class="ns_area list"]/ul/li')
        for target in target_list:
            li = li_list[target]
            url = li.xpath('./a/@href').extract_first()
            self.module_urls.append(url)
            # 对每一个板块的url发起请求
            yield scrapy.Request(url, callback=self.parse_module)

    # 数据解析：新闻标题+新闻详情页的url（动态加载的数据）
    def parse_module(self, response):
        # 直接对response解析新闻标题数据是无法获取该数据（动态加载的数据）
        # response是不满足当下需求的response，需要将其变成满足需求的response
        # 满足需求的response就是包含了动态加载数据的response
        # 满足需求的response和不满足的response区别在哪里？
        # 区别就在于响应数据不同。我们可以使用中间件将不满足需求的响应对象中的响应数据篡改成包含
        # 了动态加载数据的响应数据，将其变成满足需求的响应对象
        div_list = response.xpath('//div[@class="newsdata_wrap"]/ul/li[1]/div/div')
        for div in div_list:
            url = div.xpath('./a/@href').extract_first()
            title = div.xpath('./div/div[1]/h3/a/text()').extract_first()
            if url and title:  # 因为广告等原因，有些链接取不到url和title
                item = WangyiproItem()
                item['title'] = title
                yield scrapy.Request(url, callback=self.parse_detail, meta={'item': item})

    def parse_detail(self, response):
        """解析新闻详情"""
        item = response.meta['item']
        content = response.xpath('//div[@id="endText"]/p/text()').extract()
        item['content'] = content
        yield item

    # 爬虫类父类的方法，该方法是在爬虫结束前最后一刻执行
    def closed(self, spider):
        self.bro.close()
```

但是这样是解析不出数据来的。因为我们前面分析过，模块页中的每个文章都是动态加载的。我们需要修改响应数据，让响应数据变成我们想要的那种，加载好了新闻链接和标题的网页数据。这就要在中间件中，通过 selenium 发送请求，获取数据了。

用代码实现就是：

```python
from scrapy.http import HtmlResponse
from time import sleep

class WangyiproDownloaderMiddleware(object):
    # 拦截所有的响应对象
    # 整个工程发起的请求：1+5+n，相应也会有1+5+n个响应
    # 只有指定的5个响应对象是不满足需求
    # 只将不满足需求的5个指定的响应对象的响应数据进行篡改即可
    def process_response(self, request, response, spider):
        # 在所有拦截到的响应对象中找出指定的5个响应对象
        if request.url in spider.module_urls:
            bro = spider.bro
            # response表示的就是指定的不满足需求的5个响应对象
            # 篡改响应数据：首先先获取满足需求的响应数据，将其篡改到响应对象中即可
            # 满足需求的响应数据就可以使用selenium获取
            bro.get(request.url)    # 对五个板块的url发起请求
            sleep(2)
            bro.execute_script('window.scrollTo(0, document.body.scrollHeight)')
            sleep(2)
            # 捕获到了板块页面中加载出来的全部数据（包含了动态加载的数据）
            # response.text = bro.page_source
            # 返回一个新的响应对象，新的对象替换原来不满足需求的旧的响应对象
            return HtmlResponse(url=request.url, body=bro.page_source, encoding='utf-8', request=request)
        return response    # 1+n
```

剩下的就是在配置中开启管道和中间件，在 items 中写上相应字段，在管道中进行数据持久化存储，就不一一介绍了。

至此，我们实现了在 scrapy 中使用 selenium 发送请求。