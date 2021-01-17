# 使用 Workover 自动更新 Docker 镜像和容器

[toc]

更新 Docker 总是一件麻烦事。为了保证 docker 容器最新，我们需要时常关注 dockerhub 上的版本状态。而更新操作也是相当繁琐，因为每个容器的参数都是不一致的，如果搞错了参数，还会涉及各种兼容问题。

此时，我们就可以使用 Workover 自动更新 Docker 容器和镜像。使用十分简单，只需要创建一个 workover 容器，即可实现对所有容器的监控：

```bash
docker run -d \
    --name watchtower \
    -v /var/run/docker.sock:/var/run/docker.sock \
    containrrr/watchtower
```

更多的配置信息可以参见：https://p3terx.com/archives/docker-watchtower.html

以及官方 GitHub：https://github.com/containrrr/watchtower