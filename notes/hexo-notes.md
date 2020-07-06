## hexo 部署博客的使用笔记

[TOC]

### hexo 的安装

hexo 需要有 node 和 git 环境。

安装好依赖，即可通过 npm 安装 hexo：

```shell
npm install -g hexo-cli
```

### hexo 项目的初始化

进入要存放 hexo 项目的文件夹，运行命令初始化创建一个 hexo 项目：

```shell
cd /tmp/hexo
hexo init
 # hexo也可以初始化到指定文件夹：
hexo init /tmp/hexo
```

在项目文件夹中运行命令，会自动根据刚刚生成的 package.json 文件安装 npm 依赖的包：

```shell
npm install
```

博客文章 md 文件保存在 `source/_posts` 目录下，模板文件保存在 `theme` 文件夹中，`_config.yml` 为配置文件。

### hexo 的命令写法

以下的命令，如无说明，都是默认在刚刚初始化的 hexo 的文件夹下进行的。

hexo 命令会在当前文件夹即其外层文件夹中寻找。若有 hexo 项目，则可正常运行。若无 hexo 项目，需要指定 hexo 项目的文件夹：

```shell
hexo --cwd /tmp/hexo server
```

### 创建博客

可以使用命令创建博客：

```shell
hexo new [layout] <title>
```

可以在命令中指定文章的布局（layout），默认为 `post`，可以通过修改 `_config.yml` 中的 `default_layout` 参数来指定默认布局。

命令执行后，会在 `source/_posts` 目录下生成一个 md 文件。

也可以自己创建 md 文件，在最顶端加上下面几行内容：

```
---
title: 文章标题
date: 2020-02-12 14:30:24
tags: 标签
toc: true
categories: Web 前端
layout: single-column
---
```

### 设置图片位置

打开配置文件 `config.yml` ，把下面的这一行设置成 true：

```ini
post_asset_folder: true
```

创建与 md 文件同名（不要 .md 后缀）的文件夹，把图片保存到里面。

在需要使用图片的位置，写上下面的格式：

```jinja2
{% asset_img slug [title] %}
# 例如
{% asset_img example.jpg This is an example image %}
```

### 模板下载

安装主题和渲染器（GitHub 访问太慢，已经转移至码云）：

```shell
git clone https://gitee.com/shuoliuchn/maupassant-hexo.git themes/maupassant
npm install hexo-renderer-pug --save
npm install hexo-renderer-sass --save
```

编辑 Hexo 目录下的 `_config.yml`，将 `theme` 的值改为 `maupassant`。

更多配置信息，参见： https://www.haomwei.com/technology/maupassant-hexo.html

### 模板修改

原版的模板使用 `single-column` 模式输出，没有目录展示。修改代码，让其支持目录。

打开 `theme/manpassant/layout` 目录下的 `single-column.pug` 文件，将其内容修改为：

```
extends base-without-sidebar
block title
  title= page.title + ' | ' + config.title
block content
  if page.toc
    div(class='clear')
      div(id='toc' class='toc-article')
        div(class='toc-title')= __('contents')
        != toc(page.content, {list_number: theme.toc_number})
  .post
    h1.post-title= page.title
    .post-content
      != page.content
  if page.comments
    include _partial/comments.pug
```

### 修改配置文件

如果 md 文件中有一对大括号之类的代码，部署博客时可能会出现莫名其妙的错误。这是因为 hexo 有一些参数，就是通过下面的符号来占位的：

```jinja2
{{ }}
{# #}
{% %}
```

要避免这样的问题，我们当然可以通过转义的方式来修改：

```jinja2
{% raw %}
{{ 双大括号内包裹的内容 }}
{% endraw %}
```

但是这样做并不合适，尤其是当我们写 django 的模板渲染或者 vue 的前端渲染语法的时候，因为我们会经常用到这些类似的符号。如果每一个都转义一下，好麻烦的。

所以，我更倾向于一劳永逸的，使用修改配置文件的方式规避这个问题。

1. 打开文件 `node_modules/nunjucks/src/lexer.js`

2. 找到这样两行代码：

   ```js
   var VARIABLE_START = '{{';
   var VARIABLE_END = '}}';
   ```

3. 将其修改为：

   ```js
   var VARIABLE_START = '{$';
   var VARIABLE_END = '$}';
   ```

### 部署上线

编译：

```shell
hexo generate
# 可简写为：
hexo g
```

运行服务，服务默认运行在 4000 端口，绑定 `0.0.0.0` 的 IP 地址：

```shell
hexo server
# 可简写为：
hexo s
# 后台运行：
hexo s &
# 可指定端口：
hexo server -p 5000
# 亦可指定 ip：
hexo server -i 192.168.1.1
```

要将服务传至 GitHub 或码云，需要先建立仓库。

安装 `npm install hexo-deployer-git --save` 工具：

```shell
npm install hexo-deployer-git --save
```

修改配置文件中的 git 信息：

```ini
deploy:
  type: git
  repo: <repository url> #https://bitbucket.org/JohnSmith/johnsmith.bitbucket.io
  branch: [branch]
  message: [message]
```

修改完毕后，可以部署博客了：

```bash
hexo g
hexo deploy
# 可简写为：
hexo d
# 两条命令可以合并为：
hexo g -d
# 或
hexo d -g
```

### 设置博客首页置顶

先卸载掉原来的首页生成器，然后安装有指定功能的首页生成器

```bash
npm uninstall hexo-generator-index --save
npm install hexo-generator-index-pin-top --save
```

在文章的头部的 `front-matter` 中设置 `top: true` 即可。

### 设置 hexo 后台运行

每次都用 hexo s 命令启动博客很麻烦，而且一旦关闭终端，进程也自动终止。虽然按照官方方法，通过命令 hexo s & 可以实现后台运行，但是持续时间不到一天就会莫名其妙地关闭终止掉。好在在网上找到了合适的解决方案，思路是使用 pm2 工具让 hexo 进程时刻保持运行状态。

首先，安装 pm2：

```bash
npm install -g pm2
```

安装完成，进入 hexo 的根目录，创建一个 js 文件，比如 `hexo_run.js`

```bash
cd /tmp/hexo
vim hexo_run.js
```

在里面写上让进程保持运行的代码，写好后保存并推出：

```JavaScript
const { exec } = require('child_process')
exec('hexo server',(error, stdout, stderr) => {
        if(error){
                console.log('exec error: ${error}')
                return
        }
        console.log('stdout: ${stdout}');
        console.log('stderr: ${stderr}');
})
```

用 pm2 运行 `hexo_run.js`：

```bash
pm2 start hexo_run.js
pm2 stop/restart hexo_run.js    # 停止/重启
```

pm2 常用命令：

```bash
pm2 list    # 查看pm2进程
pm2 -h      # 查看帮助
```

### 编辑页脚

页脚要通过模板的 footer 组件编辑。我的模板 footer 组件放在这里，对于不同的模板，footer 的存放位置和编辑方式是有差异的，这一点一定要注意：

```bash
vim themes/maupassant/layout/_partial/footer.pug
```

footer.pug 文件最开始的版本为：

```pug
#footer= 'Copyright © ' + date(Date.now(), 'YYYY') + ' '
  a(href=url_for('.'), rel='nofollow')= config.title + '.'
  |  Powered by
  a(rel='nofollow', target='_blank', href='https://hexo.io')  Hexo.
  a(rel='nofollow', target='_blank', href='https://github.com/tufu9441/maupassant-hexo')  Theme
  |  by
  a(rel='nofollow', target='_blank', href='https://github.com/pagecho')  Cho.
```

在最后加上个 a 连接，完整版本就是：

```
#footer= 'Copyright © ' + date(Date.now(), 'YYYY') + ' '
  a(href=url_for('.'), rel='nofollow')= config.title + '.'
  |  Powered by
  a(rel='nofollow', target='_blank', href='https://hexo.io')  Hexo.
  a(rel='nofollow', target='_blank', href='https://github.com/tufu9441/maupassant-hexo')  Theme
  |  by
  a(rel='nofollow', target='_blank', href='https://github.com/pagecho')  Cho.
  |
  a(rel='nofollow', target='_blank', href='http://beian.miit.gov.cn')  辽ICP备20001451号
```

保存退出，重新生成即可。

### 数学公式

若文章中出现了数学公式，可以使用



### 参考资料

1. [hexo 官方中文文档](https://hexo.io/zh-cn/docs/)
2. [大道至简——Hexo简洁主题推荐](https://www.haomwei.com/technology/maupassant-hexo.html)
3. [解决Hexo标签与MD文件冲突问题](https://www.jianshu.com/p/6032a1a2dc25)
4. [让Hexo一直在后台运行](https://expoli.tech/articles/2018/05/02/1564714955650.html)
5. [Hexo页脚加公安备案](https://www.return520.com/posts/25301/)