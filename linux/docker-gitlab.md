## Docker 部署 GitLab

首先，从 duckerhub 拉取 GitLab 镜像：

```bash
sudo docker pull gitlab/gitlab-ce
```

然后，编辑 ~/.bashrc 文件，指定 GITLAB_HOME 目录：

```bash
export GITLAB_HOME=/srv/gitlab
```

接下来，启动 GitLab 镜像即可：

```bash
sudo docker run --detach \
  --hostname gitlab.example.com \
  --publish 443:443 --publish 80:80 --publish 2222:22\
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab \
  --volume $GITLAB_HOME/logs:/var/log/gitlab \
  --volume $GITLAB_HOME/data:/var/opt/gitlab \
  gitlab/gitlab-ce:latest
```

浏览器输入服务器 IP，即可访问。

第一次访问会提示修改密码，对应的是 root 用户。

可以将 `gitlab.example.com` 放入 hosts 文件中，这样就可以使用这个 url 访问 gitlab 了。

更多内容，参见：https://docs.gitlab.com/omnibus/docker/README.html