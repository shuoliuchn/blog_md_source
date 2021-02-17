# Docker 安装 ownCloud

[toc]

ownCloud 是一个非常优秀的私人网络云盘服务器。不过如果从头自己配置，十分繁琐，且因需安装大量的依赖，会把服务器环境搞得非常混乱。ownCloud 官方提供了 docker 镜像，一条命令实现安装部署，十分方便。

最简单的方式是使用 ownCloud 的官方镜像运行，只需将下面的配置修改成自己想要的即可（数据库和 Redis 相关的配置可以不写，默认使用 sqlite 数据库）：

```bash
docker run \
    --restart unless-stopped \
    -e OWNCLOUD_DOMAIN=localhost:8080 \
    -e OWNCLOUD_DB_TYPE=mysql \
    -e OWNCLOUD_DB_NAME=owncloud \
    -e OWNCLOUD_DB_USERNAME=owncloud \
    -e OWNCLOUD_DB_PASSWORD=owncloud \
    -e OWNCLOUD_DB_HOST=192.168.31.93 \
    -e OWNCLOUD_ADMIN_USERNAME=admin1 \
    -e OWNCLOUD_ADMIN_PASSWORD=admin1 \
    -e OWNCLOUD_MYSQL_UTF8MB4=true \
    -e OWNCLOUD_REDIS_ENABLED=true \
    -e OWNCLOUD_REDIS_HOST=192.168.31.93 \
    -p 8080:8080 \
    -v /path/to/data:/mnt/data \
    --name owncloud \
    -d owncloud/server
```

