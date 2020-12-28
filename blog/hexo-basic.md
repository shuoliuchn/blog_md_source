# Hexo 的基本使用

[toc]

## 初始化博客项目

安装完成 hexo，即可使用命令初始化创建一个博客项目：

```bash
hexo init blog
cd blog
npm install
```

这个命令会在当前目录下生成一个名为 blog 的 hexo 项目，其目录结构为：

```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

### \_config.yml

Hexo 的 [配置](https://hexo.io/zh-cn/docs/configuration) 文件，通过修改这个文件可以配置 hexo 成为我们想要的样子。

### package.json

npm 的包文件，用来记录环境依赖。使用命令 `npm install`，这些依赖的包将会安装到 node_modules 文件夹中。

应用程序的信息。[EJS](https://ejs.co/), [Stylus](http://learnboost.github.io/stylus/) 和 [Markdown](http://daringfireball.net/projects/markdown/) renderer 已默认安装，可以自由修改和移除。

```
package.json{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "hexo": {
    "version": ""
  },
  "dependencies": {
    "hexo": "^3.8.0",
    "hexo-generator-archive": "^0.1.5",
    "hexo-generator-category": "^0.1.3",
    "hexo-generator-index": "^0.2.1",
    "hexo-generator-tag": "^0.2.0",
    "hexo-renderer-ejs": "^0.3.1",
    "hexo-renderer-stylus": "^0.3.3",
    "hexo-renderer-marked": "^0.3.2",
    "hexo-server": "^0.3.3"
  }
}
```

### scaffolds

[模版](https://hexo.io/zh-cn/docs/writing) 文件夹。当您新建文章时，Hexo 会根据 scaffold 来建立文件。

Hexo的模板是指在新建的文章文件中默认填充的内容。例如，如果您修改scaffold/post.md中的Front-matter内容，那么每次新建一篇文章时都会包含这个修改。

### source

资源文件夹是存放用户资源的地方。除 `_posts` 文件夹之外，开头命名为 `_` (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。后面我们写好的博客文件就将会放到 `_posts` 文件夹中。

当我们执行 hexo generate 命令生成静态网站文件时，source 目录下的所有 Markdown 和 HTML 文件都会按照相对路径的形式会被解析并放到 `public` 文件夹，而其他文件会被直接拷贝过去。

public 文件夹和 source 同级，在使用 `hexo generate` 命令后，会创建该文件夹，而当使用 `hexo clean` 命令时会清空该文件夹中的内容，在使用 `hexo deploy` 命令后，会将该文件夹下的文件提交到设置的 git 仓库中。

使用时，我们只需要将 nginx 指向 public 文件，即可访问到博客内容了。

### themes

[主题](https://hexo.io/zh-cn/docs/themes) 文件夹。Hexo 会根据主题来生成静态页面。

## Hexo 的配置

在初始化的文件夹中，有一个名为 `_config.yml` 的文件。此文件以 yaml 格式书写，可在 `yaml 官网` 了解更多关于 yaml 语法相关的知识。当然，如果不了解也无伤大雅，配置十分简单，一目了然。而且很多配置人家都已经写好了示例，我们只需要简单修改即可。

打开并编辑 `_config.yml` 配置文件：

```bash
cd /path/to/hexo
sudo vim _config.yml
```

### 网站

最上面是备注为 Site 的一些配置，用来设置网站相关的内容。可以配置成这样子：

```yaml
# Site
title: '刘硕的技术查阅手册'
subtitle: 'Python 全栈开发学习笔记'
description: '不成为自己讨厌的人'
keywords:
  - Python
  - 全栈开发
  - 并发编程
  - 网络编程
  - 爬虫
  - 数据库
  - 自动化测试
  - Web开发
  - HTML
  - CSS
  - JavaScript
  - jQuery
  - Bootstrap
  - 测试自动化
  - Selenium
  - postman
  - Linux
  - Vue
  - drf
  - django-rest-framework
  - Django
  - 文档翻译
  - PEP8
  - Hexo
  - 博客制作
  - Supervisor
author: '刘硕'
language: 'zh-CN'
timezone: 'Asia/Shanghai'
```

参数解释：

- title：网站标题，会渲染到至少三个地方：head 标签的 title 标签（在浏览器标签中显示的内容）、页面的标题位置、name 属性分别为 `og:title` 和 `og:site_name` 的两个 meta 标签的 `content` 属性中
- subtitle：网站副标题，会渲染到网页的副标题位置
- description：网站描述，可用于SEO，告诉搜索引擎一个关于站点的简单描述，通常建议在其中包含网站的关键词。不过有些模板比如 Next，会将这个描述显示在头像下，如果内容太多不好看，故而我在这里放了一个格言
- keywords：网站的关键词。可以像我这样写成多个，也可以使用逗号分隔写成一个大的字符串，看喜好，反正页面上看不到，主要是 SEO 用的
- author：当然是作者咯，有些主题可能会用到
- language：页面显示的语言，模板如果支持多语言的话可以在这里设置到底显示哪一种
- timezone：时区，默认使用服务器时间，北京时间是我写的亚洲上海这种

### 网址

接下来是一部分和网址有关的配置：

```yaml
url: https://sliu.vip
root: /
permalink: :title/
permalink_defaults:
pretty_urls:
  trailing_index: true # Set to false to remove trailing 'index.html' from permalinks
  trailing_html: true # Set to false to remove trailing '.html' from permalinks
```

参数解释：

- url：网址，必须以 http 或 https 开头
- root：网站根目录
- permalink：网站永久链接格式，影响生成的静态文件的存储路径，我喜欢直接以标题作为链接（虽然这样不利于 SEO，但是用着方便，尤其是站内链接很好写）
- permalink_defaults：永久链接各部分的默认值
- pretty_urls.trailing_index：是否在永久链接中保留尾部的 `index.html`，设置为 `false` 时去除
- pretty_urls.trailing_html：是否在永久链接中保留尾部的 `.html`, 设置为 `false` 时去除（*对尾部的 `index.html`无效*）

最后两项如果使用 nginx 作为静态文件服务器的话其实是不需要的，不管加不加最后的 `index.html` 或 `.html`，都是可以显示的

### 目录

然后是关于目录的配置，设置的文件夹都是相对于 hexo 项目的根目录的相对路径。通常没必要修改这些文件夹，大致了解就行：

```yaml
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:
```

参数解释：

- source_dir：资源文件夹的位置，这个文件夹用来存放内容
- public_dir：公共文件夹，这个文件夹用于存放生成的站点文件
- tag_dir： 标签文件夹
- archive_dir：归档文件夹
- category_dir：分类文件夹
- code_dir：代码文件夹，不清楚是做什么的，`source_dir` 下的子目录
- i18n_dir：国际化（i18n）文件夹，用于模板的多语言设置
- skip_render：跳过指定文件的渲染。匹配到的文件将会被不做改动地复制到 `public` 目录中。您可使用 [glob 表达式](https://github.com/micromatch/micromatch#extended-globbing)来匹配路径

### 文章

文章相关的配置，主要是对文章内容显示方面的：

```yaml
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link:
  enable: true # Open external links in new tab
  field: site # Apply to the whole site
  exclude: ''
filename_case: 0
render_drafts: false
post_asset_folder: true
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: true
  tab_replace: ''
  wrap: true
  hljs: false
prismjs:
  enable: false
  preprocess: true
  line_number: true
  tab_replace: ''
```

参数解释：

- new_post_name：使用 `hexo new` 命令新生成的 markdown 文章的文件名
- default_layout：预设布局
- auto_spacing：在中英文直接加空格，默认为 false。可以将其设置为 true，让页面显示好看些。不过我已经习惯在中英文之间加空格了，所以按原设置来，没有加这个选项
- titlecase：把标题转换成每个单词首字母大写的标题形式，一般是中文标题，没必要
- external_link：在新标签页打开链接
- external_link.enable：开启本项设置
- external_link.field：对整个网站（`site`）生效或仅对文章（`post`）生效
- external_link.exclude：需要排除的域名。主域名和子域名如 `www` 需分别配置
- filename_case：把文件名称转换为（1）小写或（2）大写，或（0）不转换
- render_drafts：渲染草稿，默认情况下草稿是不会被渲染的
- post_asset_folder：启动资源文件夹，我的 hexo_migrator 会使用这个功能，所以设成了 true
- relative_link：把链接改为与根目录的相对路径
- future：显示未来（即发布时间晚于当前时间）的文章
- highlight 和 prismjs：代码块的显示样式，这两个只能有一个生效

