## 爬虫学习过程中的小经验小技巧总结

1. Jupyter 本质上还是基于浏览器的工具。网页打不开的文件格式，Jupyter 应该也打不开。如果文件的后缀名错了，不是浏览器能识别的格式，会默认以文本形式打开。于是乎，比如把 jpg 图片的后缀名协程了 jgp，使用 Jupyter 打开会出错。
2. scrapy 创建爬虫源文件时，网址可以随便写，但是不能不写