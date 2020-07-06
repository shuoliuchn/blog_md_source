## nginx 的访问日志功能

[TOC]

### http 请求状态码

nginx 的各种功能其实都是改改 nginx.conf 就好，访问日志功能也不例外。

nginx 根据访问相应的状态码进行相应的日志记录，常见的状态码如下：

| 状态码 | 所属系列 |                             描述                             |
| :----: | :------: | :----------------------------------------------------------: |
|  404   |   40x    |        客户端请求出错。用户那里出错了，请求的姿势不对        |
|  500   |   50x    | 服务器端出错。django 后台崩溃；MySQL 数据库连不上；Python 报错 |
|  200   |   20x    |                   表示请求正确，并给与响应                   |
|  300   |   30x    |       请求被重定向转发。比如旧的网址启用，转发至新网址       |

### 配置日志文件流程

1. 打开配置文件 `nginx.conf`，找到如下配置，打开注释即可：

   ```shell
   log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                       '$status $body_bytes_sent "$http_referer" '
                       '"$http_user_agent" "$http_x_forwarded_for"';
   
   access_log  logs/access.log  main;    # 日志存放位置，相对于nginx安装位置，可指定
   access_log  "pipe:rollback logs/access_log interval=1d baknum=7 maxsize=2G"  main;
   ```

   提示 1：vim 命令模式按下 x 键可以直接删除光标位置的字符。通过这个方式可以快速删除注释代码前的 #

   提示 2：源码编译的 nginx 的配置文件位于指定的安装位置的 conf 文件夹中的 `nginx.conf`；yum 安装的 nginx 的配置文件默认位于 `/etc/nginx/nginx.conf`

2. 打开注释之后，平滑重启 nginx：

   ```shell
   nginx -s reload
   ```

3. 如果想杀死 nginx，可以用如下命令之一：

   ```shell
    kill  pid     # 指定进程 id 杀死进程
    pkill nginx   # 通过名字杀死进程
   ```

4. 解释一下日志设置中的每个变量：

   ```shell
   $remote_addr    # 记录客户端ip
   $remote_user    # 远程用户，没有就是 “-”
   $time_local 　　 # 对应[14/Aug/2018:18:46:52 +0800]
   $request　　　 　# 对应请求信息"GET /favicon.ico HTTP/1.1"
   $status　　　  　# 状态码
   $body_bytes_sent　　# 571字节 请求体的大小
   $http_referer　　# 对应“-”　　由于是直接输入浏览器就是 -
   $http_user_agent　　# 客户端身份信息
   $http_x_forwarded_for　　# 记录客户端的来源真实ip 97.64.34.118
   ```