## nginx 负载均衡配置

[TOC]

当有很多用户同时进行请求时，对服务器的压力是十分巨大的。这时，如果能够通过一个代理服务器，将这些请求分流给不同的服务器，让多个服务器均摊压力，情况就会好很多。这个负责分发请求的代理服务器，就是负载均衡服务器。这个过程，就是负载均衡。

一句话，负载均衡，就是将负载均衡地发给了服务器。

![image-20200207145205876](nginx-load-balance.assets/image-20200207145205876.png)

### 负载均衡环境准备

按照负载均衡的含义，如果要实现真正的负载均衡，我们只要需要准备 3 台服务器：

```
机器1：代理服务器（nginx提供负载均衡，反向代理的功能）
机器2：资源服务器1
机器3：资源服务器2  
```

请求发给代理服务器，然后配置规则，请求会均衡发送给机器 2 或机器 3。

考虑到测试学习用的电脑配置可能较低，而且部署 3 台服务器也太过繁琐，并没有这么大的必要。不要忘了，nginx 是支持多虚拟主机的，所以其实准备 2 台 Linux 服务器便足矣：

```
准备一个 代理服务器 192.168.248.148 ，为代理服务器
准备一个资源服务器(目的是准备2个资源站点，可以用nginx的多虚拟主机模拟) 192.168.248.149
```

请求发送的具体流程为：

```
请求 -> 代理服务器 ->负载均衡转发 -+-> 第一次发给第一个 server{} 站点
                               |
                               +-> 第二次发给第二个 server{} 站点
```

### 配置资源服务器，准备好 2 个页面

**注意，线上真实的负载均衡部署，后端服务器应该是一样的**

我们在做实验阶段，为了看到效果，是准备2个不同的资源服务器的，注意不要误解。

1. 在 192.168.248.129 资源服务器机器上，安装好 nginx。

2. 修改nginx.conf配置如下，第一个 server{} 虚拟主机返回吴亦凡的照片；第二个 server{} 虚拟主机返回黄渤的照片：

   ```shell
   # 虚拟主机1，返回吴亦凡照片
   server {
       listen       8000;
       server_name  localhost;
   
       #charset koi8-r;
   
       #access_log  logs/host.access.log  main;
       #access_log  "pipe:rollback logs/host.access_log interval=1d baknum=7 maxsize=2G"  main;
   
       location / {
           root   /tmp/lb/wyf;
           index  index.html;
       }
   
       #error_page  404              /404.html;
   
       # redirect server error pages to the static page /50x.html
       #
       error_page   500 502 503 504  /50x.html;
           location = /50x.html {
           root   html;
       }
   }
   # 虚拟主机2，返回黄渤照片
   server {
       listen       8001;
       server_name  localhost;
       location / {
           root   /tmp/lb/hb;
           index  index.html;
       }
   }
   ```

3. 分别准备 2 个资源服务器的文件夹：

   ```
   mkdir -p /tmp/lb/{wyf,hb}
   ```

4. 分别写入 2 个首页文件：

   ```shell
   [root@localhost /]# cat /tmp/lb/wyf/index.html 
   
   <meta charset="utf8">
   <img src="http://photocdn.sohu.com/20160428/Img446589732.jpg">
   [root@localhost /]# cat /tmp/lb/hb//index.html 
   
   <meta charset="utf8">
   <img src="http://img4.cache.netease.com/ent/2011/5/18/2011051808191860a06.jpg">
   ```

5. 重启 nginx，加载配置：

   ```shell
   nginx -s reload
   ```

### 配置负载均衡服务器

负载均衡服务器，负责把用户发来的请求，均衡分配给 `192.168.248.129:8000` 和 `192.168.248.129:8001` 两个资源服务器。

负载均衡服务器的配置流程为：

1. 在 192.168.248.128 负载均衡服务器机器上，安装好 nginx。

2. 修改 nginx 配置，添加负载均衡参数。在 server{} 标签上面，添加一个负载均衡池 upstream{}，并在 server 标签中进行相应修改：

   ```shell
   # 服务器地址池，负载均衡池，名字是lb，可任意取
   upstream lb {
    server 192.168.248.129:8000;
       server 192.168.248.129:8001;
   }
   server {
       listen       80 default_server;
       listen       [::]:80 default_server;
       server_name  _;
       root         /usr/share/nginx/html;
   
       # Load configuration files for the default server block.
       include /etc/nginx/default.d/*.conf;
       # 请求在这里转发给负载均衡池lb
       location / {
           proxy_pass http://lb;
       }
   
       error_page 404 /404.html;
           location = /40x.html {
       }
   
       error_page 500 502 503 504 /50x.html;
           location = /50x.html {
           }
       }
   }
   ```
   
3. 使用命令，检测 `nginx.conf` 是否编写正确：

   ```shell
   nginx -t
   ```

4. 编写正确后，重启 nginx：

   ```shell
   ngins -s reload
   ```

5. 此时，访问负载均衡服务器，既可以看到服务会由两台资源服务器分别提供：

   ![lb](nginx-load-balance.assets/lb.gif)

6. 我们也可以在负载均衡池中加入权重，这样会给权重高的服务器发送更多的请求，从而达到性能好的服务器处理更多任务的目的：

   ```shell
   upstream lb {
       server 192.168.248.129:8000  weight=1;
       server 192.168.248.129:8001  weight=2;
   }
   ```

7. 重启负载均衡服务器的 nginx：

   ```shell
   nginx -s reload
   ```

8. 再次访问时，会发现，两台主机出现的频率为 1:2

   ![lbw](nginx-load-balance.assets/lbw.gif)

### nginx 负载均衡的分发算法

分发规则，自行决定，根据自己的服务器架构决定。

最常用的就是权重，或者大家根本就不管，默认用轮询。

nginx 负载均衡有三种分发算法：

1. 默认是轮训模式，一台服务器一次

2. 权重模式，配置如下：

   ```shell
   upstream lb {
       server 192.168.248.129:8000  weight=1;
       server 192.168.248.129:8001  weight=2;
   }
   ```

3. ip 哈希模式，根据客户端的 ip 来源，指定给一台机器。

   根据客户端的来源ip进行哈希，得到一个唯一值，永远只发给某一台机器了

   就是指定某一个服务器来服务客户端

   其配置方式为：

   ```shell
   upstream lb {
       server 192.168.248.129:8000;
       server 192.168.248.129:8001;
       ip_hash;
   }
   ```

