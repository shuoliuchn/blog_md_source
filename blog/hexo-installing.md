# Hexo 的安装

Hexo 的安装需要依赖 node.js 和 git 环境。

## 安装 git

各种系统安装 git 的方式都可以在 [git 官网](https://git-scm.com/downloads) 找到，都十分容易。

Windows 安装 git 就是从官网下载安装包，有些选项不懂也无妨，按照默认选项一直下一步就可以。

Linux 和 MacOS 安装可以通过包管理工具，比如 yum 或 apt-get 直接安装，一条命令即可。

如果能使用命令查看 git 的版本，说明 git 安装成功：

```bash
git version
```

## 安装 node.js

node.js 也可以直接从 [node.js 的官网](https://nodejs.org/en/download/) 下载安装。

不过我更倾向于使用 mvm 安装，因为这样可以按需使用自己需要的 node.js 版本。操作说明可以从 [nvm 的 GitHub 仓库](https://github.com/nvm-sh/nvm) 找到，几条命令就可以搞定。

安装好 node.js，就会自动安装 node.js 的包管理工具 npm。默认的 npm 镜像源在国外，访问速度慢，有时甚至无法使用。所以可以使用 [阿里的 npm 镜像源](https://developer.aliyun.com/mirror/NPM)，提高效率。

## 安装 hexo

安装好 node.js 和 git 之后，即可安装 hexo 了：

```bash
npm install -g hexo-cli
```

没错，一条命令就可以搞定 hexo 的安装。