## selenium 模块的基本使用

[TOC]

### selenium 介绍和安装

爬虫过程中，各种反爬机制让人头疼。由于动态网页的存在，明明浏览器看得见可以点击的东西，却不能直接通过 requests 请求得到。非要绕很多弯才能获取我们想要的数据。有的请求还要携带 cookie 和一些乱七八糟的字符串。

selenium 模块就是为了帮我们解决这些困扰而诞生的。

selenium 是一种基于浏览器的自动化的模块。

自动化的含义是，可以通过代码指定一些列的行为动作，然后将其作用到浏览器中。

因为 selenium 本身就是基于浏览器的，所以几乎不会受到反爬机制的束缚。

selenium 的安装：

```bash
pip install selenium
```

selenium 和爬虫之间的关联

1. 便捷的捕获到任意形式动态加载的数据（可见即可得）
2. 实现模拟登录

selenium 虽然能给我们带来很大的便捷，但是却有一个显著的缺点——效率太低。因为每次爬取信息，都要打开浏览器。

在使用 selenium 之前，我们需要下载各种浏览器的驱动，注意驱动版本要和当前使用的浏览器版本相符合。

谷歌驱动下载：http://chromedriver.storage.googleapis.com/index.html

驱动下载好后，最好保存在环境变量中存在的文件夹中，比如 Python 安装目录的 bin 目录。这样，我们在使用 selenium 的时候，就不必每次都指定驱动了。

这里的示例，我把谷歌浏览器驱动放在了工作目录中，每次使用都要指定驱动的位置。

selenium 的基本使用流程如下：

```python
from selenium import webdriver
from time import sleep
from lxml import etree
# 基于浏览器的驱动程序实例化一个浏览器对象
bro = webdriver.Chrome(executable_path='./chromedriver.exe')
# 对目的网站发起请求
bro.get('https://www.jd.com')
# 标签定位，最常用的是id和xpath定位
search_box = bro.find_element_by_xpath('//input[@id="key"]')
# 向标签中输入数据
search_box.send_keys('iphone X')

search_btn = bro.find_element_by_xpath('//button[@aria-label="搜索"]')
# 点击按钮
search_btn.click()

sleep(2)

# 在搜索结果页面进行滚轮向下滑动的操作（执行js操作：js注入）
bro.execute_script('window.scrollTo(0, document.body.scrollHeight)')
sleep(2)
bro.quit()
```

再看一个处理百度网页的例子：

```python
from selenium import webdriver
from time import sleep

# 后面是你的浏览器驱动位置，记得前面加r'','r'是防止字符转义的
driver = webdriver.Chrome(r'./chromedriver.exe')
# 用get打开百度页面
driver.get("http://www.baidu.com")
# 查找页面的“设置”选项，并进行点击
driver.find_elements_by_link_text('设置')[0].click()
sleep(2)
# # 打开设置后找到“搜索设置”选项，设置为每页显示50条
driver.find_elements_by_link_text('搜索设置')[0].click()
sleep(2)

# 选中每页显示50条
m = driver.find_element_by_id('nr')
sleep(2)
m.find_element_by_xpath('//*[@id="nr"]/option[3]').click()
m.find_element_by_xpath('.//option[3]').click()
sleep(2)

# 点击保存设置
driver.find_elements_by_class_name("prefpanelgo")[0].click()
sleep(2)

# 处理弹出的警告页面   确定accept() 和 取消dismiss()
driver.switch_to.alert.accept()
sleep(2)
# 找到百度的输入框，并输入 美女
driver.find_element_by_id('kw').send_keys('美女')
sleep(2)
# 点击搜索按钮
driver.find_element_by_id('su').click()
sleep(2)
# 在打开的页面中找到第一个图片的标签，并打开这个页面
driver.find_element_by_xpath('//*[@id="1"]/div[1]/a[1]/img').click()
sleep(3)

# 关闭浏览器
driver.quit()
```

我们从前爬取过药监局的网页，那个网页时动态生成的。当时我们是通过浏览器的抓包工具，找到获取数据的请求，从而实现了页面信息的抓取。

如果使用 selenium，抓取动态网页将变得很容易。

需求：抓取药监局页面前三页所有企业名称信息

网址 url：http://125.35.6.84:81/xk/

```python
url = 'http://125.35.6.84:81/xk/'
bro = webdriver.Chrome(executable_path='./chromedriver')
bro.get(url)
page_text_list = []    # 每一页的页面源码数据都会放到这里面
sleep(2)
# 捕获到当前页面对应的页面源码数据，也就是当前页面全部加载完毕后对应的所有的数据
page_text_list.append(bro.page_source)

for i in range(2):
    next_btn = bro.find_element_by_id('pageIto_next')
    next_btn.click()
    sleep(1)
    page_text_list.append(bro.page_source)

# 点击下一页
for page_text in page_text_list:
    tree = etree.HTML(page_text)
    text_list = tree.xpath('//ul[@id="gzlist"]//dl//text()')
    for text in text_list:
        print(text)
        
sleep(2)
bro.quit()
```

### 动作链 ActionChains

有时候，我们除了点击操作之外，还会有一些比如拖动等操作。这时候，就需要用到动作链。

```python
bro = webdriver.Chrome(executable_path='./chromedriver.exe')
bro.get('https://www.runoob.com/try/try.php?filename=jqueryui-api-droppable')

# 如果通过find系列的函数进行标签定位，如果标签是存在于iframe下面，则会定位失败
# 解决方案：使用switch_to即可
bro.switch_to.frame('iframeResult')
div_tag = bro.find_element_by_id('draggable')

# 对div_tag进行滑动操作
action = webdriver.ActionChains(bro)
action.click_and_hold(div_tag)    # 点击且长按

for i in range(4):
    # perform让动作链立即执行
    action.move_by_offset(10, 15).perform()
    sleep(0.5)

sleep(2)
bro.quit()
```

### 让 selenium 规避检测

虽然 selenium 很强大，但依然有可能会漏出马脚，让人家检测出我们是在使用 selenium 发起的浏览器请求。有的网站会检测请求是否为selenium发起，如果是的话则让该次请求失败。

规避检测的方法是让 selenium 接管 chrome 浏览器。

实现步骤

1. 必须将你电脑中安装的谷歌浏览器的主程序所在的目录找到，且将目录添加到环境变量中。

2. 打开 cmd，在命令行中输入命令：

   ```bash
   chrome.exe --remote-debugging-port=9222 --user-data-dir="一个空文件夹的目录"
   ```

   指定执行结束后，会打开你本机安装好的谷歌浏览器。

3. 执行如下代码：可以使用下述代码接管步骤 2 打开的真实的浏览器

   ```python
   from selenium import webdriver
   from selenium.webdriver.chrome.options import Options
    
   chrome_options = Options()
   chrome_options.add_experimental_option("debuggerAddress", "127.0.0.1:9222")
   # 本机安装好谷歌的驱动程序路径
   chrome_driver = "./chromedriver.exe"
   
   driver = webdriver.Chrome(executable_path=chrome_driver,chrome_options=chrome_options)
   print(driver.title)
   ```

### 无头浏览器

我们看到，每次执行 selenium 代码，都要打开浏览器窗口，然后亲眼看着它操作。有的时候，如果不小心把鼠标放到浏览器的窗口里面，还有可能会影响到自动化程序的正常运行。

无头浏览器，就是将浏览器的页面隐藏起来，我们看不见它的操作。那么就不会在每次允许 selenium 代码的时候跳出窗口来犯我们。我们也不会因为误操作而影响程序的运行。

常用的无头浏览器有两个：

- 谷歌无头浏览器（推荐）
- phantomJs（停止维护）

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
import time
 
# 创建一个参数对象，用来控制chrome以无界面模式打开
chrome_options = Options()
chrome_options.add_argument('--headless')
chrome_options.add_argument('--disable-gpu')
 
# 创建浏览器对象
browser = webdriver.Chrome(executable_path='./chromedriver', chrome_options=chrome_options)
 
# 上网
url = 'https://www.baidu.com/'
browser.get(url)
time.sleep(3)
#截图
browser.save_screenshot('baidu.png')
print(browser.page_source)
browser.quit()
```