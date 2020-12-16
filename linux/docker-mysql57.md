## Docker 部署 MySQL 5.7

首先，拉取 MySQL 5.7 镜像：

```bash
sudo docker pull mysql:5.7
```

创建 mysql 文件夹，用于存放 MySQL 的配置和数据：

```
mkdir ~/mysql
```

编辑 `~/mysql/my.cnf`，向其中写入如下配置：

```ini
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[mysqld]
#设置3306端口
port = 3306
# 允许最大连接数
max_connections=200
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

然后，启动 MySQL 5.7 容器：

```bash
sudo docker run -d \
  --name mysql \
  --restart always \
  --publish 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=123 \
  --volume ~/mysql/data:/var/lib/mysql \
  --volume ~/mysql/my.cnf:/etc/mysql/my.cnf \
  mysql:5.7
```

安装 MySQL 客户端：

```bash
sudo apt install mysql-client-core-8.0
```

即可使用命令访问 MySQL：

```bash
mysql -h 127.0.0.1 -u root -p 123
```

