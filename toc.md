

# 技术查阅手册

[TOC]

## Python - 基础

- 背景讨论与 Python 环境部署
  - **计算机基础：** [计算机基础知识总结](python-basic\computer-basic.md) 
  - **Python 环境部署：** [Python 的版本选择与安装细节](python-basic\python-installation.md) | [环境变量的配置](python-basic\environment-variable.md) | [后缀名显示方法](python-basic\show-suffix.md) | [Visual Studio Code Python 环境部署](python-basic\vs-code-python.md) | [交互模式下启动 Python 项目](python-basic\bash-python-proj.md) | [Ubuntu 系统安装和 Python 开发环境配置](python-basic\ubuntu-python.md) | [CentOS 系统 Python 环境搭建](python-basic\centos-python.md) 
  - **笔记与博客：** [码云初识](python-basic\gitee-daily.md) | [Markdown 常用语法](python-basic\markdown.md) | [技术博客制作](python-basic\blog-writing.md) | [思维导图](python-basic\mind-map.md) | [排 bug 技巧](python-basic\bug-exclusion.md) 
- Python 基本语法
  - **Python 中的数据类型：** [变量、常量和注释](python-basic\var-const-com.md) | [整型、字符串和布尔值](python-basic\int-str-bool.md) | [字符串的格式化](python-basic\format.md) | [字符串方法详解](python-basic\str.md) | [f-strings 详解](python-basic\f-strings.md) | [整型数据详述和进制转换](python-basic\int.md) | [列表](python-basic\list.md) | [元组](python-basic\tuple.md) | [字典](python-basic\dict.md) | [集合](python-basic\set.md) | [Python 中表示 False 的方法](python-basic\false.md) | [基础数据类型补充和总结](python-basic\basic-data-type.md) 
  - **流程控制和基本运算：**[用户交互语句](python-basic\user-interface.md) | [索引和切片](python-basic\index-slice.md) | [流程控制语句](python-basic\flow-control.md) | [while 循环](python-basic\while-cycle.md) | [运算符](python-basic\operator.md) | [for 循环](python-basic\for-cycle.md) | [解构](python-basic\deconstruction.md) | [推导式](python-basic\comprehension.md) |
  - **函数：** [函数](python-basic\function.md) | [函数的名称空间](python-basic\function-name-space.md) | [函数名的第一类对象及使用](python-basic\function-name-first-class-object.md) | [函数嵌套](python-basic\function-nesting.md) | [迭代器和生成器](python-basic\iterator-generator.md) | [匿名函数](python-basic\lambda.md) | [闭包](python-basic\closure.md) | [装饰器](python-basic\decorator.md) | [递归](python-basic\recursion.md) 
  - **模块：** [模块](python-basic\module.md) | [time 和 datetime 模块](python-basic\time-datetime.md) | [random 模块](python-basic\random.md) | [os 和 sys 模块](python-basic\os-sys.md) | [序列化 json 和 pickle](python-basic\serialization.md) | [hashlib 模块](python-basic\hashlib.md) | [collections 模块](python-basic\collections.md) | [re 模块和正则表达式](python-basic\re.md) | [logging 模块和日志](python-basic\logging.md) | [包](python-basic\package.md) 
  - **内置函数 BIF：** [range](python-basic\range.md) | [文件操作](python-basic\file.md) | [基础内置函数](python-basic\basic-bif.md) | [高阶内置函数](python-basic\high-order-bif.md) 
- 其他 Python 相关知识
  - **编码：** [编码初识](python-basic\code.md) | [二次编码](python-basic\second-code.md) 
  - **Python 运行机制：** [Python 中的驻留机制：小数据池和代码块](python-basic\resistant-mechanism.md) | [深浅拷贝](python-basic\copy-deep-copy.md) | [循环删除的坑](python-basic\loop-del.md) | [语法分析和词法分析](python-basic\syntax.md) 
  - **代码编写规范：** [软件开发规范](python-basic\software-dev.md) | [Python 2 和 Python 3 的区别](python-basic\Python2-Python3.md) 
  - **实用脚本：** [使用 Python 统计中文字符的数量](python-basic\hanz_count.md) | [递归获取目录中所有文件](python-basic\recursion-get-files.md) 



## Python - 进阶

- 面向对象
  - **面向对象：** [面向对象初识](python-advanced\object-oriented.md) | [OOA、OOD 和 OOP](python-advanced\ooa-ood-oop.md) | [依赖关系和组合关系](python-advanced\class-relationship.md) | [继承](python-advanced\inheritance.md) | [封装](python-advanced\encapsulation.md) | [多态](python-advanced\polymorphism.md) | [常见设计模式](python-advanced\design-pattern.md) 
- 并发编程
  -  [消息队列和 RabbitMQ](python-advanced\queue-rabbitmq.md) 
- 网络编程
  -  [RPC 的原理和简单使用](python-advanced\rpc.md) 



## 数据库

- MySQL
  - 数据库基本概念： [数据库的基本概念和 Windows 下安装 MySQL](database\database-basic.md) | [MySQL 客户端命令](database\mysql-client-command.md) 
  - MySQL 语法： [数据库、数据表和数据的基本操作](database\mysql-db-tb.md) | [MySQL 中的数据类型](database\mysql-data-type.md) | [MySQL 表的完整性约束](database\mysql-constraint.md) 
  - Python 操作 MySQL： [mysqlclient 安装](linux\mysqlclient.md) 
- Redis
  -  [Redis 的安装和配置](database\redis-install.md) | [Redis 的基本使用](database\redis-abc.md) | [Python 操作 Redis](database\redis-python.md) 



## Django

- Django 基础
  - **HTTP 和 Web 框架相关概念：** [HTTP 协议](django\django-http.md) | [动手搭建 Web 框架深入了解 Web 框架的本质](django\django-web-framework.md) 
  - **Django 的基本用法：** [Django 初识](django\django-quickstart.md) | [Django 视图 view](django\django-views.md) | [Django 模板 template](django\django-template.md) | [Django URL 路由](django\django-url.md) | [Django 模型 model 数据库 ORM 单表操作](django\django-orm-single.md) | [Django ORM 多表增删改查](django\django-orm-multiple.md) | [ORM 执行原生 SQL 语句](django\django-sql.md) | [ORM 事务和锁](django\django-transaction-lock.md) | [Django 使用 ajax 和通过 csrf 认证的三种方式](django\django-ajax.md) | [Django 通过 form 表单和 ajax 上传文件](django\django-file-upload.md) | [JsonResponse](django\django-jsonresponse.md) | [Django 操作 Cookie 和 session](django\django-cookie.md) | [Django 中间件和请求的生命周期](django\django-middleware.md) | [Django 的表单 form 组件](django\django-form.md) | [Django ModelForm 模型表单组件](django\django-modelform.md) 
  - **Django 其他使用技巧：** [Django 外部脚本调用 models 数据库](django\django-script.md) | [解决 Django 自定义用户模型之前就已经数据迁移的 BUG](django\django-migration-bug.md) 
- Django REST Framework
  - **概述：** [前后端分离相关概念](django\drf-front-rear-sep.md) | [DRF 环境安装与配置](django\drf-envir-build.md) 
  - **序列化器 Serializer：** [序列化器 Serializer](django\drf-serializer.md) | [模型类序列化器 ModelSerializer](django\drf-modelserializer.md) | [序列化](django\drf-serialization.md) | [反序列化](django\drf-deserialization.md) 
  - **视图：** [请求与响应](django\drf-request-response.md) | [基本视图类 APIView](django\drf-api-view.md) | [通用视图类 GenericAPIView](django\drf-generic-view.md) | [5 个视图扩展类 ModelMixin](django\drf-model-mixins.md) | [GenericAPIView 的视图子类](django\drf-generic-view-children.md) | [视图集 ViewSet](django\drf-view-set.md) 
  - **路由：** [路由 Router](django\drf-router.md) 
  - **DRF 的其他组件：** [认证 Authentication 和权限 Permission](django\drf-authentication-permission.md) | [限流 Throttling 和过滤 Filtering](django\drf-throttling-filtering.md) | [排序 Ordering 和分页 Pagination](django\drf-ordering-pagination.md) | [异常处理 Exceptions](django\drf-exceptions.md) | [自动生成接口文档](django\drf-api-doc.md) 



## Web 前端

- 前端基础
  - **HTML：** [HTML 基础](web\web-html-basic.md) 
  - **CSS：** [CSS 引入方式和 CSS 选择器](web\web-css-basic.md) | [CSS 样式基础](web\web-css-style.md) 
  - **JavaScript：** [JavaScript 的使用和基本数据类型](web\web-js-basic.md) | [JavaScript 基本语法](web\web-js-syntax.md) | [JavaScript 操作 BOM 和 DOM 对象](web\web-js-dom-bom.md) | [JavaScript 的事件冒泡和事件委托](web\js-event-bubble.md) | [js 的本地存储](web\web-js-localstorage.md) | [js 小脚本集合](web\web-js-script.md) 
  - **jQuery：** [jQuery 基础和选择器](web\web-jquery-basic.md) | [jQuery 操作 DOM](web\web-jquery-dom.md) | [jQuery 操作 Cookie](web\web-jquery-cookie.md) 
  - Bootstrap：
  - **其他前端工具：** [SweetAlert 弹窗工具](web\web-sweetalart.md) 
- Vue：
  - **背景知识：** [JavaScript ES 6 简单常用语法](web\js-es6.md) | [PyCharm 配置 Vue 开发环境](web\vue-pycharm.md) 
  - **Vue 基础知识：** [vue.js 的快速入门使用](web\vue-abc.md) | [Vue 指令系统的常用指令](web\vue-common-command.md) | [Vue 对象提供的属性功能](web\vue-attr-methods.md) | [通过 axios 实现数据请求](web\axios.md) | [JSON](web\json.md) | [ajax 和同源策略](web\ajax-cors.md) | [Vue 的组件化开发](web\vue-component-dev.md) | [Vue Router 的使用](web\vue-router.md) | [Vue 单文件组件的使用](web\vue-file-component.md) | [在组件中使用 axios 获取数据](web\vue-component-axios.md) | [Vue 客户端项目搭建](web\vue-client-build.md) 



## Git

-  [Git 基本语法](git\git-syntax.md) | [GitHub 代码管理仓库](git\github.md) | [rebase 变基](git\rebase.md) | [Git 配合 Beyond Compare 来解决冲突](git\beyond-compare.md) | [Git 多人协作开发](git\team-dev.md) | [给开源项目贡献代码](git\open-source-proj.md) | [Git 配置文件详解](git\git-config.md) | [Git 远程仓库免密登陆](git\git-ssh.md) | [Git 忽略文件 .gitignore](git\gitignore.md) | [GitHub 做任务管理相关 Issues 和 Wiki](git\issues-wiki.md) 



## 测试自动化

- 测试工具
  - **selenium 模块：** [selenium 模块的安装和配置](testing\selenium-install.md) | [selenium 的基本操作](testing\selenium-basic.md) 



## Linux

- Linux 基础
  - **基础知识和系统安装：** [服务器与 Linux 基础知识](linux\server-linux.md) | [虚拟机安装 CentOS](linux\centos-vmware.md) | [远程连接 CentOS](linux\remote-connection.md) | [IP 和端口](linux\ip-port.md) | [目录和路径](linux\directory-path.md) | [dns 域名解析](linux\dns.md) 
  - **Linux 操作命令：** [vim 的基本使用](linux\vim.md) | [Linux 基本的文件增删改查命令](linux\basic-command.md) | [Linux 常用命令](linux\common-command.md) | [编译安装 Python 3 并部署简单 django 项目](linux\py3-installation.md) | [Linux 的软件包管理工具 yum](linux\yum.md) | [解压缩包安装 JDK](linux\jdk-installation.md) 
  - **nginx 的安装和使用：** [Linux 软件安装流程 - nginx 的安装](linux\software-installation.md) | [nginx 介绍](linux\nginx-intro.md) | [nginx 虚拟主机功能](linux\nginx-virtual-server.md) | [nginx 的多虚拟主机配置](linux\nginx-multi-virtual-server.md) | [nginx 的访问日志功能](linux\nginx-log.md) | [nginx 的错误页面优化功能](linux\nginx-error-page.md) | [nginx 的反向代理功能](linux\nginx-reverse-proxy.md) | [nginx 负载均衡配置](linux\nginx-load-balance.md) | [crm 项目部署](linux\crm-build.md) | [Nginx 快速入门](linux\nginx-abc.md) | [Django 项目的部署](linux\django-proj-deploy.md) 
  - **分布式：** [MySQL 分布式存储](linux\mysql-distributed-storage.md) | [ZooKeeper 分布式调度](linux\distributed-scheduling.md) | [LVS + keepalived 实现负载均衡](linux\lvs-keepalived-lb.md) | [Redis 分布式缓存](linux\redis-distributed-cache.md) 
  - **Docker：** [Docker 初识和安装](linux\docker-abc-install.md) | [docker 的镜像和容器](linux\docker-image-container.md) | [docker 的网络](linux\docker-network.md) | [docker 的持久化存储和数据共享](linux\docker-data-share.md) | [docker Compose 多容器部署](linux\docker-compose.md) |  [Docker 部署 GitLab](linux\docker-gitlab.md) | [Docker 部署 MySQL 5.7](linux\docker-mysql57.md) | [Docker 安装 Redis](linux\docker-redis.md) | [Docker 安装 Gogs](linux\docker-gogs.md) 
  - **Python 和 pip：** [Python 的虚拟环境](linux\python-virtualenv.md) | [导出导入 Python 依赖](linux\python-requirements.md) | [pip 更换为国内源](linux\pip-source.md) 



## 爬虫

- 爬虫基础
  - **环境搭建和爬虫概述：** [Anaconda 开发环境的搭建和 Jupyter 的基本使用](crawler\anaconda-jupyter.md) | [爬虫概述](crawler\crawler-intro.md) | [HTTPS 简要介绍](crawler\https.md) 
  - **requests 模块：** [requests 模块应对 UA 策略和爬取动态网页](crawler\requests-dynamic.md) | [数据解析（正则、bs4 和 xpath）](crawler\data-parse.md) | [requests 高级用法之获取 Cookie、使用代理和识别验证码](crawler\requests-advanced.md) | [异步爬虫](crawler\async-crawler.md) | [JS 解密和混淆破解](crawler\js-decode.md) 
  - **selenium 模块：** [selenium 模块的基本使用](crawler\selenium.md) | [12306 模拟登录和余票检测](crawler\12306-login.md) 
  - **scrapy 框架：** [scrapy 框架的基本用法](crawler\scrapy-basic.md) | [scrapy 高级用法](crawler\scrapy-advanced.md) | [CrawlSpider 的基本使用](crawler\crawlspider.md) | [selenium 在 scrapy 中的使用](crawler\scrapy-selenium.md) | [分布式](crawler\distributed.md) | [增量式](crawler\incremental.md) 
  - **爬虫工具：** [百度 AI 的基本用法](crawler\baidu-ai.md) | [使用 Fiddle 实现移动端数据的爬取](crawler\mobile.md) 



## 数据分析

- 数据分析基础
  - **数据分析概述：** [数据分析基本概念](data-analysis\data-analysis-abc.md) 
  - **NumPy：** [NumPy 的基本使用](data-analysis\numpy-basic.md) 
  - **Pandas：** [Pandas 的基础操作](data-analysis\pandas-basic.md) | [DataFrame 基础操作巩固 - 股票分析](data-analysis\stock-analysis.md) | [基于 Pandas 的数据清洗](data-analysis\pandas-data-cleaning.md) | [DataFrame 的级联与合并操作](data-analysis\df-concate-merge.md) | [人口分析案例](data-analysis\population-analysis.md) | [Pandas 高级操作](data-analysis\pandas-advanced.md) | [2012 美国大选献金项目数据分析](data-analysis\political-contributions.md) 
  - **Matplotlib：** [Matplotlib 绘图基础](data-analysis\matplotlib-basic.md) 
  - **seaborn：** [seaborn 基本用法](data-analysis\seaborn-abc.md) 
  - **综合：** [用户消费行为分析案例](data-analysis\consumption-behavior.md) 



## 数据结构和算法

- 数据结构
  -  [数据结构和算法概述](algorithm\data-structure-algorithm.md) | [Python 数据结构性能分析](algorithm\python-performance-analysis.md) | [栈](algorithm\stack.md) | [队列](algorithm\queue.md) | [顺序表和链表](algorithm\linked-list.md) 
- 算法
  - **查找算法：** [二叉树](algorithm\binary-tree.md) | [二分查找](algorithm\binary-search.md) 
  - **排序算法：** [冒泡排序和选择排序](algorithm\bubble-selection.md) | [插入排序和希尔排序](algorithm\insert-shell.md) | [快速排序和归并排序](algorithm\quick-merge.md) 



## 机器学习

- 机器学习基础
  - **背景和理论：** [机器学习概述](machine-learning\ml-abstract.md) | [特征工程](machine-learning\feature-engineering.md) | [sklearn 的数据集](machine-learning\sklearn-dataset.md) | [机器学习基础](machine-learning\ml-basic.md) 
  - **机器学习算法：** [K-近邻算法和交叉验证](machine-learning\knn-cross.md) | [线性回归和回归算法的评价指标](machine-learning\linear-regression.md) | [多项式回归和过拟合与欠拟合的处理](machine-learning\polynomial.md) | [朴素贝叶斯算法](machine-learning\naive-bayes.md) | [逻辑斯蒂回归](machine-learning\logistic-regression.md) | [分类模型的评价指标](machine-learning\classification-evaluation.md) 



## 综合项目

- 客户关系管理系统 CRM
- 简易自动测试平台
- 荏苒资讯：
  - **环境部署和项目搭建：** [项目开发基础概念和需求分析](project\renran-basic-requirement.md) | [荏苒资讯项目环境部署](project\renran-envir.md) | [荏苒资讯后端项目搭建](project\renran-proj-build.md) | [荏苒资讯前端项目搭建](project\renran-pc-build.md) | [通过 CORS 解决跨域问题](project\renran-cors.md) 
  - **登录和注册：** [用户登陆认证](project\renran-login.md) | [Xadmin 的安装、配置和使用](project\renran-xadmin.md) | [用户的注册认证](project\renran-register.md) | [使用 Celery 完成异步任务](project\renran-celery.md) | [荏苒资讯首页配置](project\renran-home.md) | [荏苒资讯 QQ 第三方登录实现](project\renran-qq-oauth.md) 
  - **文章模块：** [Docker 介绍和基本使用](project\docker.md) | [FastDFS 分布式文件存储系统](project\renran-fastdfs.md) | [荏苒资讯的文章模块开发](project\renran-article.md) | [荏苒资讯的文章编辑器开发](project\renran-editor.md) | [荏苒资讯专题部分开发](project\renran-special.md) | [荏苒资讯文章详情页开发](project\renran-article-detail.md) | [荏苒资讯文章打赏功能开发](project\renran-reward.md) 
  - **Feed 流文章推送系统：** [Feed 流系统概述](project\renran-feed-flow.md) | [Django 操作阿里云表格存储 Tablestore](project\renran-tablestore.md) | [荏苒资讯用户关注功能](project\renran-follow.md) | [荏苒资讯 Feed 流系统文章推送](project\renran-article-feed.md) 



## Hexo

- 使用 Markdown + Hexo 搭建个人博客： [使用 Markdown + Hexo 搭建个人博客系列简介](hexo\introduction.md) |



## 学习实践笔记

- 读书笔记：
  - **算法图解：** [图书简介](notes\grokking-algorithms-intro.md) | [二分查找](notes\grokking-algorithms-binary-search.md) | [大 O 表示法](notes\grokking-algorithms-big-o.md) | [旅行商问题简述](notes\grokking-algorithms-tsp.md) | [数组和链表](notes\grokking-algorithms-array-list.md) | [选择排序](notes\grokking-algorithms-selection-sort.md) | [递归](notes\grokking-algorithms-recursion.md) | [栈](notes\grokking-algorithms-stack.md) | [分而治之](notes\grokking-algorithms-dc.md) | [快速排序](notes\grokking-algorithms-quick-sort.md) | [散列表（哈希表）](notes\grokking-algorithms-hash-table.md) | [广度优先搜索](notes\grokking-algorithms-bfs.md) | [狄克斯特拉算法](notes\grokking-algorithms-dijkstra.md) |
- **李永乐老师视频：** [双蛋问题的 Python 递归解决](notes\two-egg-problem.md) 
  
- **杂记：** [使用 Django 搭建静态文件分发服务器](notes\django-distribute-static.md) | [腾讯云环境配置](notes\tencent-build.md) | [hexo 使用笔记](notes\hexo-notes.md) | [部署 HTTPS 网站](notes\mysite-https.md) | [把当前 Ubuntu 系统做成 ISO 镜像](notes\ubuntu-iso-notes.md) 



## 官方文档翻译

- Python 官方文档
  - **PEP 文档：** [PEP 8 -- Python代码格式规则](translation\PEP8.md) 
  - **其他官方文档：** [Python 新式类继承关系的 C3 算法（Python 2.3 的方法解析顺序，MRO）](translation\python-MRO.md) 
  - 第三方包官方文档：
    - **Django REST Framework 官方教程：** [快速入门](translation\django-rest-framework-tutorial\quickstart.md) | [教程 1: 序列化 Serialization](translation\django-rest-framework-tutorial\serialization.md) | [教程 2：请求 Request 和响应 Response](translation\django-rest-framework-tutorial\requests-response.md) | [教程 3：类视图 Class-based View](translation\django-rest-framework-tutorial\class-based-view.md) | [教程 4：认证 Authentication 和权限 Permission](translation\django-rest-framework-tutorial\authentication-permission.md) | [教程 5：关系 Relationships 和超链接 API Hyperlinked APIs](translation\django-rest-framework-tutorial\relationship-hyperlinked-api.md) 
    - **supervisor 官方文档：** [supervisor 的安装](translation\supervisor\installing.md) | [运行 Supervisor](translation\supervisor\running.md) 
    - **raspberry-gpio-python 模块使用实例：** [安装](translation\raspberry-gpio-python-examples\installing.md)  |  [基础使用](translation\raspberry-gpio-python-examples\gpio-basic-use.md)  |  [输入](translation\raspberry-gpio-python-examples\input.md)  |  [输出](translation\raspberry-gpio-python-examples\output.md)  |  [PWM（脉宽调制）](translation\raspberry-gpio-python-examples\PWM.md) |  [查看 GPIO 通道的功能](translation\raspberry-gpio-python-examples\GPIO-function.md) |  [控制 LED 和开关](translation\raspberry-gpio-python-examples\controlling-LED-and-switch.md) 
  
- 实用工具官方文档
  
  -  **Typora：** [Typora官方Markdown教程](translation\Typora-Markdown.md) 



## 树莓派

- 树莓派基本使用
  - **操作系统相关：**  [SD 卡的格式化](raspberry-pi\sd-format.md) 



## Bug 宝典

- **Python Bug 宝典：** [小经验小技巧](bug-bible\python-bug\python-experience.md) | [AttributeError](bug-bible\python-bug\AttributeError.md) | [FileNotFoundError](bug-bible\python-bug\FileNotFoundError.md) | [KeyError](bug-bible\python-bug\KeyError.md) | [NameError](bug-bible\python-bug\NameError.md) | [RecursionError](bug-bible\python-bug\RecursionError.md) | [RuntimeError](bug-bible\python-bug\RuntimeError.md) | [StopIteration](bug-bible\python-bug\StopIteration.md) | [SyntaxError](bug-bible\python-bug\SyntaxError.md) | [TypeError](bug-bible\python-bug\TypeError.md) | [UnboundLocalError](bug-bible\python-bug\UnboundLocalError.md) | [ValueError](bug-bible\python-bug\ValueError.md) | [ImportError](bug-bible\python-bug\ImportError.md) 
- **Django Bug 宝典：** [InternalError](bug-bible\django-bug\InternalError.md) 
- **MySQL Bug 宝典：** [ASCII](bug-bible\db-bug\ASCII.md) 
- **JavaScript Bug 宝典：** [小经验小技巧](bug-bible\js-bug\js-experince.md) | [Uncaught SyntaxError](bug-bible\js-bug\UncaughtSyntaxError.md) | [TypeError](bug-bible\js-bug\TypeError.md) 
- **Linux Bug 宝典：** [小经验小技巧](bug-bible\linux-bug\linux-experience.md) | [touch](bug-bible\linux-bug\touch.md) | [mkdir](bug-bible\linux-bug\mkdir.md) | [yum](bug-bible\linux-bug\yum.md) | [mkdir](bug-bible\linux-bug\mkdir.md) | [bash](bug-bible\linux-bug\bash.md) 
- **Vue Bug 宝典：**  [小经验小技巧](bug-bible\vue-bug\vue-experience.md) | [NetworkError](bug-bible\vue-bug\NetworkError.md) | [VueWarn](bug-bible\vue-bug\VueWarn.md) | [TypeError](bug-bible\vue-bug\TypeError.md) | [Uncaught Error](bug-bible\vue-bug\UncaughtError.md) 

