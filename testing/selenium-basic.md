## selenium 的基本操作

[TOC]

### 使用示例

首先来看一个 selenium 的使用示例：

```python
import time

# 导入浏览器驱动
from selenium import webdriver
# 使用浏览器驱动实例化一个谷歌浏览器驱动对象: driver,所有关于浏览器的操作,都由driver对象完成
driver = webdriver.Chrome()
try:

    # 打开浏览器,访问指定的url
    driver.get('https://www.baidu.com/')
    # 你对当前的页面要做的操作
    input_obj = driver.find_element_by_id('kw')
    print(input_obj)
    input_obj.send_keys('听雨危楼')
    time.sleep(2)
    driver.find_element_by_id('su').click()
    time.sleep(2)
    driver.find_element_by_xpath('//*[@id="1"]/h3/a').click()

finally:
    time.sleep(10)
    # 关闭浏览器
    driver.close()
```

在下面的讨论中，如无特别声明，我将使用 driver 指代浏览器驱动对象，而不会每次都创建。而且使用完毕后，都会关闭掉驱动，关闭操作也先不写了。

### 常用类

```python 
from selenium import webdriver    # 驱动浏览器
from selenium.webdriver import ActionChains    # 鼠标的相关操作，比如滑动验证
from selenium.webdriver.common.by import By    # 选择器，以什么方式选择标签元素
from selenium.webdriver.common.keys import Keys    # 键盘相关
from selenium.webdriver.support import expected_conditions as EC    # 各种判断，一般跟等待事件连用，比如说等待某个元素加载出来
from selenium.webdriver.support.wait import WebDriverWait    # 等待事件，可以与EC连用
```

### 浏览器页面操作

```python
driver.maximize_window()    # 浏览器窗口最大化
driver.minimize_window()    # 最小化
driver.set_window_size(800, 600)    # 指定窗口大小
print(driver.title)    # 获取页面的title
print(driver.page_source)    # 获取网页文本
print(driver.current_url)    # 获取当前页的url
print(driver.name)    # 获取driver对象名，比如：chrome
print(driver.get_cookies())   # 获取cookies
print(driver.current_window_handle)    # 获取当前浏览器窗口id，切换窗口时会用到
print(handles = driver.window_handles)    # 获取所有浏览器窗口id，存放在列表中
driver.refresh()    # 刷新当前页面
driver.back()    # 后退
driver.forward()    # 前进
driver.execute_script('alert("xxoo不可描述")')    # js注入
driver.close()    # 关闭当前的窗口对象
driver.quit()    # 退出浏览器
```

### 截屏操作

```python
# 获取截屏的字节流，需要我们手动写到文件中
res = driver.get_screenshot_as_png()
# print(res)    # 图片二进制数据
f = open('a.png', 'wb')
f.write(res)
# 直接保存截屏，图片的格式为png
driver.save_screenshot('b.png')
# 截屏某个元素
driver.find_element_by_id('login_box').screenshot('c.png')
```

### 选择器

选择器用来找到页面中的某个指定到标签。`find_element_by_xxx` 只会找到第一个符合条件的标签，返回的将是一个标签对象；而 `find_elements_by_xxx` 返回的则是一个列表，列表里面装的是所有符合条件的标签。

```python
# 通过id定位
driver.find_element_by_id('su')
driver.find_elements_by_id('xxx')
# 通过xpath定位
driver.find_element_by_xpath('xxx')
driver.find_elements_by_xpath('xxxx')
# 通过class类名定位
driver.find_element_by_class_name('xxx')
driver.find_elements_by_class_name('xxx')
# 通过css选择器定位
# 浏览 f12 选中标签,鼠标右键 copy -- copy selector
# css selector 
#     - 标签
#     - id
#     - class
driver.find_element_by_css_selector('xx')
driver.find_elements_by_css_selector('xx')
# 通过tag标签名定位
driver.find_element_by_tag_name('div')
driver.find_elements_by_tag_name('div')
# 通过a标签的link属性文本定位
# 只能适用于超链接
# link_text 精确定位
# partial_link_text : 模糊定位,谨慎使用
driver.find_element_by_link_text('xxx')
driver.find_elements_by_link_text('xxx')
driver.find_element_by_partial_link_text('xx')
driver.find_elements_by_partial_link_text('xxx')
# 根据name属性定位
driver.find_element_by_name('xx')
driver.find_elements_by_name('xx')
```

### By

上面介绍的各种标签选择器全部都可以通过 By 来调用：

```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Firefox()
driver.get('https://www.baidu.com/')

# driver.find_element_by_id('su')
driver.find_element(By.ID, 'kw').send_keys('抠脚')
driver.find_elements(By.ID, 'kw')[0].send_keys('抠脚')
# By.ID
# By.CLASS_NAME
# By.LINK_TEXT
# By.NAME
# By.XPATH
# By.CSS_SELECTOR
```

### 鼠标事件

如果是简单的鼠标左键点击，可以直接使用标签的 click 方法实现：

```python
driver.get('https://www.baidu.com/')
driver.find_element_by_id('su').click()
```

动作链 ActionChains 用来实现鼠标事件。鼠标事件有很多，包括点击拖动等。注意鼠标事件设置好后，要使用 perform 方法执行生效。

```python 
from selenium.webdriver import ActionChains    # 鼠标相关的操作都在这里

# 左键单击
a1 = driver.find_element_by_xpath('/html/body/form/input[3]')
ActionChains(driver).click(a1).perform()
# 左键双击
a2 = driver.find_element_by_xpath('/html/body/form/input[2]')
ActionChains(driver).double_click(a2).perform()
# 右键单击
a3 = driver.find_element_by_xpath('/html/body/form/input[4]')
ActionChains(driver).context_click(a3).perform()

# 移动鼠标
ActionChains(driver).move_to_element(li).perform()

# 拖拽
a = driver.find_element_by_id('draggable')
time.sleep(1)
b = driver.find_element_by_id('droppable')
time.sleep(1)
ActionChains(driver).drag_and_drop(a, b).perform()
```

鼠标事件所有方法：

```python
print(dir(ActionChains))

['click', 'click_and_hold', 'context_click', 'double_click',
 'drag_and_drop', 'drag_and_drop_by_offset', 'key_down',
 'key_up', 'move_by_offset', 'move_to_element',
 'move_to_element_with_offset', 'pause', 'perform',
 'release', 'reset_actions', 'send_keys', 'send_keys_to_element']
```

### 键盘事件

键盘事件是标签对象的方法，应用最多的是向 input 框中输入数据。

```python
from selenium.webdriver.common.keys import Keys

# 获取input标签
user = driver.find_element_by_id('id_username')

user.send_keys('alexdsb')    # 向input标签中输入内容
user.send_keys(Keys.CONTROL, 'a')    # 在input标签中使用快捷键 ctrl + a
user.send_keys(Keys.DELETE)    # 按下delete键
user.send_keys(Keys.BACKSPACE)    # 按下backspace键
user.send_keys(Keys.ENTER)    # 按下回车键
user.send_keys(Keys.TAB, 'ABC')    # TAB之后，输入值
user.send_keys(Keys.TAB, Keys.ENTER)    # tab之后，直接回车

# 上传文件流程：1.访问url，2.获取input框，3.输入文件路径，4.点击上传
obj = driver.find_element_by_css_selector('input[name="excel"]')
file_path = r'C:\Users\Administrator\Desktop\接口测试示例.xlsx'
obj.send_keys(file_path)
```

键盘操作所有方法：

```python
print(dir(Keys))    # 查看Keys中所有的属性和方法

['ADD', 'ALT', 'ARROW_DOWN', 'ARROW_LEFT', 'ARROW_RIGHT',
'ARROW_UP', 'BACKSPACE', 'BACK_SPACE', 'CANCEL', 'CLEAR',
'COMMAND', 'CONTROL', 'DECIMAL', 'DELETE', 'DIVIDE', 'DOWN',
'END', 'ENTER', 'EQUALS', 'ESCAPE', 'F1', 'F10', 'F11',
'F12', 'F2', 'F3', 'F4', 'F5', 'F6', 'F7', 'F8', 'F9',
'HELP', 'HOME', 'INSERT', 'LEFT', 'LEFT_ALT', 'LEFT_CONTROL',
'LEFT_SHIFT', 'META', 'MULTIPLY', 'NULL', 'NUMPAD0',
'NUMPAD1', 'NUMPAD2', 'NUMPAD3', 'NUMPAD4', 'NUMPAD5',
'NUMPAD6', 'NUMPAD7', 'NUMPAD8', 'NUMPAD9', 'PAGE_DOWN',
'PAGE_UP', 'PAUSE', 'RETURN', 'RIGHT', 'SEMICOLON', 'SEPARATOR',
'SHIFT', 'SPACE', 'SUBTRACT', 'TAB', 'UP']
```

### 窗口切换

要注意，Selenium 是无法找到 iframe 中的标签的，需要切换到 iframe 中，才能进行后续的查找。

```python
driver.switch_to.window('窗口ID')    # 切换到指定的窗口对象中，current_window_handle和window_handles属性可得到
driver.switch_to.frame(<frame标签对象>)     # 切换到指定的iframe
driver.switch_to.alert()    # 切换到alert
driver.switch_to.default_content()    # 切换到之前的页面中
driver.switch_to.parent_frame()    # 切换到它爹的iframe
```

### 滚动条操作

通过 js 注入的方式，滚动滚动条。

```python
import time
from selenium import webdriver

driver = webdriver.Chrome()
driver.get('https://www.cnblogs.com/Neeo/articles/11002003.html')
time.sleep(3)
for i in range(1, 10):
    driver.execute_script('window.scrollTo(0,{}00);'.format(i))
    time.sleep(1)
```

直接滚动到页面底部：

```python
driver.execute_script('window.scrollTo(0, document.body.scrollHeight);')
```

