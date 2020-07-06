# nginx

## "server" directive is not allowed here

### 错误1.1

报错信息：

```shell
nginx: [emerg] "server" directive is not allowed here in /opt/nginx23/conf/nginx.conf:101
```

错误代码：

```shell
server {
    listen       80;
    server_name  www.s26linuxav.com;
    location / {
        root   /tmp/py/html/linuxav;
        index  index.html index.htm;
}
```

错误原因：

此代码位于 nginx 的配置文件 `/opt/nginx23/conf/nginx.conf` 中，server 的大括号没有封闭。

解决方法：

加上回括号

```shell
server {
    listen       80;
    server_name  www.s26linuxav.com;
    location / {
        root   /tmp/py/html/linuxav;
        index  index.html index.htm;
    }
}
```

