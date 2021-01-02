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

关于各种配置的用法，参见 [官网](https://hexo.io/zh-cn/docs/configuration)，就不赘述了。