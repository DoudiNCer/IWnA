# Redis 学习笔记

&emsp;&emsp;Redis 是一个开源的非关系型数据库系统

## 安装配置

&emsp;&emsp;源码安装

```bash
wget https://download.redis.io/redis-stable.tar.gz
tar xf redis-stable.tar.gz
cd redis-stable
make
make test
sudo make install
```

&emsp;&emsp;Docker 安装

```bash
docker pull redis:6 
docker run -itd --name redis6 -p 6379:6379 redis:6
```

&emsp;&emsp;安装完成后，有两个命令`redis-server`和`redis-cli`，启动服务端后，默认端口为6379（Merz），默认有16个数据库（0到15），使用`sselect`来切换。

&emsp;&emsp;Redis 的配置文件为`redis.conf`，可在启动服务端时指定加载该配置文件，如

```bash
redis-server /etc/redis.conf
```

&emsp;&emsp;服务端启动后，可使用`redis-cli`连接服务器：

```bash
redis-cli -h $host -p $port -a $password
```
