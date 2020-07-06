## 把当前 Ubuntu 系统做成 ISO 镜像

[TOC]

### 事件背景

有完整部署好环境的 Ubuntu 的 VMware 虚拟机，但是我的小霸王能力有限，运行起来一卡一卡的。直接装 Ubuntu 系统，极麻烦，而且很多东西都要到外网下载，很慢甚至根本下载不到。

所以决定，把虚拟机中的 Ubuntu 系统制作成 ISO 镜像，尝试着安装到电脑中。

网上很多教程都是用的 Systemback。但是因为网络原因，我这边一致安装不了。

### 安装 remastersys

下载 remastersys 的安装包：

```bash
wget ftp://ftp.gwdg.de/pub/linux/easyvdr/mirror/remastersys/ubuntu/remastersys/remastersys_3.0.4-2_all.deb
wget ftp://ftp.gwdg.de/pub/linux/easyvdr/mirror/remastersys/ubuntu/remastersys-gui/remastersys-gui_3.0.4-1_amd64.deb
```

安装依赖（即便如此，也是安装不完，过会儿还要解决依赖问题）：

```bash
sudo apt install syslinux-utils isolinux squashfs-tools casper libdebian-installer4 ubiquity-frontend-debconf user-setup discover
sudo apt install libvte-common libvte9 plymouth-x11
wget http://security.ubuntu.com/ubuntu/pool/universe/x/xresprobe/xresprobe_0.4.24ubuntu9_amd64.deb
dpkg -i xresprobe_*ubuntu9_amd64.deb
```

安装 remastersys：

```bash
sudo dpkg -i remastersys_3.0.4-2_all.deb
sudo dpkg -i remastersys-gui_3.0.4-1_amd64.deb
```

若上面的过程报错，可以通过下面的方式解决依赖问题：

```bash
sudo apt-get -f install
sudo apt-get -f install
```

启动 remastersys：

```bash
sudo remastersys-gui
```

功能解释和介绍：

```
Backup：打包整个系统，包括你的个人数据和配置信息。
Distribution：打包一个可与朋友分享的iso文件，不包括你的个人数据和配置信息。
Customize: 软件设置
Clea WorkingFolder：清除临时文件
Check Log：检查remastersys.log
Quit：退出Remastersys Backup
```



### 参考资料

1. [将自己的ubuntu18.04打包成镜像](https://blog.csdn.net/weixin_38280090/article/details/83927091?utm_source=distribute.pc_relevant.none-task)
2. [ubuntu18.04使用remastersys备份系统](https://juejin.im/post/5c6a4843f265da2db1560b2e)

