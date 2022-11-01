# Nginx学习笔记

&emsp;&emsp;**Nginx** (读作"engine X") 由Igor Sysoev(俄罗斯)于2005年编写，是一个免费、开源、高性能的HTTP服务器和反向代理，也可以作为一个IMAP/POP3代理服务器。Nginx因为稳定，丰富的功能集，配置简单，资源占用低而闻名世界。

## 安装配置

### Arch Linux 安装

```shell
pacman -S nginx                 # LTS版本
pacman -S nginx-mainline		# 主线版本
```

### CentOS 手动编译

```shell
yum -y install gcc gcc-c++ make libtool zlib zlib-devel openssl openssl-devel pcre pcre-devel
./configure --prefix=/usr/local/nginx-1.6 --with-pcre /
 --with-http_stub_status_module --with-http_ssl_module /
 --with-http_gzip_static_module --with-http_realip_module /
 --add-module=../nginx_upstream_check_module-0.3.0
 make && make install
```

### Docker 安装

```shell
docker run --name nginx-test -p 8080:80 -d nginx
```

### 常用命令

> 注意：Nginx 属于系统服务，其可执行文件位于 sbin，手册为nginx(8)。同时，nginx可能以 systemd service 的形式运行。

- 帮助/信息

  ```shell
  nginx -h    # 帮助
  nginx -v    # 版本
  nginx -V    # 版本/配置
  ```

- 运行状态

  ```shell
  nginx           # 启动
  nginx -s stop   # 快速退出（SIGTERM）
           quit   # “优雅”（graceful）退出（SIGQUIT）
           reopen # 重新打开日志文件（SIGUSR1）
           reload # 重新加载配置文件（SIGHUP）
  ```

- 验证配置文件

  ```shell
  nginx -t    # 不运行，只检查配置文件
        -T    # 检查配置文件并将其输出到标准输出
  ```

### 配置文件

> 注意：自己编译的Nginx的配置文件位于`/usr/local/nginx/conf/nginx.conf`，某些版本位于`/etc/nginx/nginx.conf`

&emsp;&emsp;Nginx 的配置文件主要分为全局部分、events 部分和 http 部分：

#### 全局部分

&emsp;&emsp;此部分包括对 Nginx 运行相关的配置

```nginx
user http;                  # 运行使用的用户，后可加用户组
worker_processes auto;      # 进程数
# max_clients = worker_processes * worker_connections
worker_cpu_affinity auto;
```

#### events

&emsp;&emsp;此部分为 Nginx “系统”的配置

```nginx
events {
    multi_accept on;
    worker_connections 1024;    # 每个进程最大连接数
}
```

#### http

&emsp;&emsp;此部分为 Nginx HTTP 服务相关配置，包括全局部分和多个 server 部分：

```nginx
http {
    charset utf-8;
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    server_tokens off;
    log_not_found off;
    types_hash_max_size 4096;
    client_max_body_size 16M;

    # MIME
    include mime.types;
    default_type application/octet-stream;

    # logging
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log warn;

    # load configs
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

&emsp;&emsp;当然，通常将不同的服务配置写在单独的配置文件中：

0. 创建文件夹

   ```shell
   mkdir /etc/nginx/sites-available
   mkdir /etc/nginx/sites-enabled
   ```

1. 将配置文件写入`/etc/nginx/sites-available/*.conf`

2. 在http块中添加`include /etc/nginx/sites-enabled/*;`

   ```nginx
   server {
       listen 443 ssl http2;
       listen [::]:443 ssl http2;
   }
   ```
3. 将需要开启的服务配置文件链接到`/etc/nginx/sites-enabled`中，并在不需要时取消链接

   ```shell
   ln -s /etc/nginx/sites-available/example.conf /etc/nginx/sites-enabled/example.conf
   unlink /etc/nginx/sites-enabled/example.conf
   ```
> DigitalOcean 提供了一个 [Nginx 配置工具](https://nginxconfig.io/)。
>
> [Nginx Beautifier](https://github.com/vasilevich/nginxbeautifier)是一个用于格式化 Nginx 配置文件的工具，可在 aur 中安装

## 反向代理

&emsp;&emsp;反向代理（请求转发）指 Nginx 根据设定的规则将某些请求转发给其他服务器程序。

```nginx
# /etc/nginx/sites-available/fake_reverse_proxy.conf
server {
    listen 443;
    listen [::]:443;
    server_name fakegoogle;
    location ~ /google/ {
        proxy_pass https://google.com;
    }
    location ~ /github/ {
        proxy_pass https://github.com;
    }
}
```

&emsp;&emsp;访问路径匹配规则如下：

> - 默认 ：在无其他匹配项时匹配
> - = ：不含正则的严格匹配（必须这个样）
> - ~ ：正则区分大小写匹配
> - ~* ：正则不区分大小写匹配
> - ^~ ：不含正则匹配第一个出现的匹配项
> - @ ：用于内部定向

## 负载均衡

&emsp;&emsp;负载均衡用于自动将请求平均分配给多个服务器程序。

0. 在 http 部分中指定负载集群：

   ```nginx
   upstream myStream{
       ip_hash;
       # 对请求 IP 进行散列存储，保证同一请求 IP 始终由同一服务器处理
       server 192.168.17.100:8080 weight=1;
       server 192.168.17.100:8000 weight=2;
   }
   ```

1. 在 location 指定负载均衡集群：

   ```nginx
   location / {
       proxy_pass https://myStream;
   }
   ```


&emsp;&emsp;默认采用轮询的分配方式，当某一服务不可用时忽略。可通过指定权重（`weight`, 默认为1）指定不同服务器收到请求的比例；也可开启`ip_hash`保证来自同一 IP 的请求始终由同一服务器处理，以解决 session 共享问题；还可开启`fair`，根据服务器响应时间分配。

## 动静分离

&emsp;&emsp;为了提高服务器运行效率，通常由不同服务器提供动态资源和静态资源。Nginx配置静态资源的方法如下：

0. 参考如下方式放置静态资源：

   ```shell
   tree /www
   .
   ├── html
   │   └── index.htm
   └── img
       └── a.png
   ```

1. 配置 server 块：

   ```nginx
   server{
       listen 8080;
       server_name mmt.com;
       location /mmt/ {
           root /www/html;
           # 访问 http://mmt.com/mmt/index.htm 会获取 /www/html/mmt/index.htm
           index index.htm index.html;
       }
       location /img/ {
           alias /www/image;
           # 访问 http://mmt.com/img/zml.jpg 会获取 /www/image/zml.jpg
           autoindex on;
       }
   }
   ```

## 参考&文档

- [Arch Wiki: Nginx](https://wiki.archlinux.org/title/Nginx)
- [Nginx Official Website](https://nginx.org/)
- [Arch manual pages: nginx(8)](https://man.archlinux.org/man/nginx.8)