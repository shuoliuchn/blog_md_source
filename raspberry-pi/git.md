## 树莓派搭建简单的 Git 服务器

首先，创建一个操作系统用户 git，并为其建立一个 `.ssh` 目录：

```bash
sudo adduser git
su git
cd
mkdir .ssh && chmod 700 .ssh
touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys
```

将用户机的公钥加入系统用户 git 的 `.ssh` 目录下 `authorized_keys` 文件的末尾：

```bash
vim .ssh/authorized_keys
```

接下来新建一个空仓库。可以借助带 `--bare` 选项的 `git init` 命令来做到这一点，该命令在初始化仓库时不会创建工作目录：

```bash
mkdir project.git
cd project.git
git init --bare
```

这时，就可以向仓库中推送代码了：

```bash
cd myproject
git init
git add .
git commit -m 'initial commit'
git remote add origin git@gitserver:/home/git/project.git
git push origin master
```

