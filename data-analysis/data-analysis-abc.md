## 数据分析基本概念

[TOC]

### 数据分析概述

我们爬虫可以爬取到大量的数据，公司日常运营，也会产生很多数据。这些数据单独放在那里，并没有什么作用。我们需要对这些数据进行整理归纳和分析，从数据中提取到我们想要的信息，这样才能让这些数据体现出它们应有的价值来。

数据分析，就是是把隐藏在一些看似杂乱无章的数据背后的信息提炼出来，总结出所研究对象的内在规律。

通过数据分析，可以使得数据的价值最大化。比如，在实际工作中，我们可以通过数据分析进行以下的分析。

- 通过分析用户的消费行为，我们可以：
  - 制定促销活动的方案
  - 制定促销时间和粒度
  - 计算用户的活跃度
  - 分析产品的回购力度
- 通过分析广告点击率，我们可以：
  - 决定投放时间
  - 制定广告定向人群方案
  - 决定相关平台的投放
- ......

数据分析是用适当的方法对收集来的大量数据进行分析，帮助人们做出判断，以便采取适当的行动。生活中，方方面面都有数据分析的影子，只需稍加留意，就可以发现：

- 保险公司从大量赔付申请数据中判断哪些可能是骗保的情况
- 支付宝通过从大量的用户消费记录和行为自动调整花呗的额度
- 短视频平台通过用户的点击和观看行为数据针对性的给用户推送喜欢的视频

### 数据分析的作用

我们已经讨论了，数据分析最根本的作用是尽可能地从数据中挖取价值。

数据就好比矿石，数据分析就是将矿石中的有用的贵重的金属提取出来的过程。没有数据分析，矿石只能是不起眼的石头。有了数据分析，矿石就变成了发光的金子。

前面的价值都是理论上的，说实际一点，就是数据分析能给我们带来财富。这包括数据分析有岗位的需求，我们也可以通过数据竞赛平台赚一些赏金。

另外，数据分析是 Python 数据科学的基础。要想通过 Python 搞科学研究，首先要会数据分析。

数据分析也是机器学习课程的基础。机器学习本质上就是大型的数据分析项目。

### 数据分析的实现流程[¶](http://localhost:8888/notebooks/数据分析day01/1.numpy模块.ipynb#数据分析实现流程)

1. 提出问题。

   问题通常由甲方提出，给我们数据，告诉我们要完成的任务和目标。有了这些，我们才能进行数据分析。

2. 准备数据。

   我们的数据来源通常可以是这么几个：

   - 爬虫项目爬取到的数据。互联网数据十分庞大，我们爬虫可以爬取到几乎任何我们想要的数据。这些数据便成了我们数据分析十分不错的数据来源。
   - 公司提供。公司日常会产生大量的诸如财务流水、人事调度等数据。分析这些数据可以对公司的运行状态进行大致的评估。
   - 合法途径购买。数据本身并非一文不值，所以有时候，我们也需要通过合法的方式购买一些我们不容易获得到的数据。

3. 分析数据

   这是我们数据分析要做的核心任务。

4. 获得结论

   数据分析过后，得出需要的结论。没有结论的数据分析是没有价值的。

5. 成果可视化

   我们的老板未必是懂技术的，直接把数字给他，恐怕是在难为人家。我们需要通过数据的可视化处理，让数据以更加直观的方式展示出来。

### 开发环境介绍

我们使用 anaconda 结合 jupyter 进行开发。这两个工具在爬虫部分已经使用很多了，这里只是简单做一些回顾。

#### anaconda

官网：https://www.anaconda.com/

集成环境：集成好了数据分析和机器学习中所需要的全部环境

注意：安装目录不可以有中文和特殊符号

#### jupyter

jupyter 就是 anaconda 提供的一个基于浏览器的可视化开发工具

jupyter 的基本使用

- 启动：在终端中录入：`jupyter notebook` 的指令，按下回车
- 新建：
  - Python 3：anaconda 中的一个源文件
  - cell 有两种模式：
    - code：编写代码
    - markdown：编写笔记
- 快捷键：
  - 添加 cell：a 或者 b
  - 删除：x
  - 修改 cell 的模式：
    - m：修改成 markdown 模式
    - y：修改成 code 模式
  - 执行 cell：
    - shift + enter
  - tab：自动补全
  - 代开帮助文档：shift + tab

### 数据分析三剑客

- numpy
- pandas（重点）
- matplotlib