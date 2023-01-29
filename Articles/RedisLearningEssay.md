# Redis 学习笔记

&emsp;&emsp;Redis 是一个开源的非关系型数据库系统

## NoSQL

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

## 数据类型与数据操作

### key&database

&emsp;&emsp;Redis 中的数据以“key-value”的形式储存，对key的操作主要有以下这些：

> - exists：判断 key 是否存在
> - type：查看 key 的数据类型（注意是 key 的）
> - del/unlink：删除 key （unlink为非阻塞删除）
> - expire/ttl：设置/查看 key 的过期时间（-1永不过期，-2已过期）

对数据库的操作主要有以下这些：

> - select：切换数据库
> - dbsize：查看数据库中 key 数量
> - flushdb/flushall：清空数据库/全部数据库

&emsp;&emsp;在 Redis 6 以前，有五种基本数据类型可以作为值（value），即 string（字符串），hash（哈希），list（列表），set（集合）和 zset(sorted set：有序集合)。

### String

&emsp;&emsp;Redis 的 String 是二进制安全的一种数据类型，可用于存储任何数据，String 类型的 value 最大为512MB ，小于 1MB 时每次成倍扩大大于 1MB 时每次扩大增加 1MB 。

&emsp;&emsp;对于 String 类型的 value,还有以下几种操作：

> - append：在 value 后追加内容，如`append k1 aa`，返回操作后 value 的长度
> - strlen：查看 value 的长度
> - get/set/setnx：获取键的值/设置键值对，setnx仅在键值对不存在时新建键值对
> - incr/decr：对于 value 为纯数字的键值对，对 value 原子操作自增/自减
> - incrby/decrby：对于 value 为纯数字的键值对，对 value 原子操作增加/减少指定步长
> - mget/mset/msetnx：原子操作批量处理键值对
> - getrange：获取一个范围（含）中的值
> - setex：在设置值的同时设置过期时间，如`settx k1 25 v1`
> - getset：获取旧值并设置新值

### List

&emsp;&emsp;Redis 的 List 在数据较少时为单个压缩列表（ziplist），数据连续存储；数据较多时转换为ziplist组成的快速链表+（quicklist）。其相关操作如下：

> - lpush/rpush：从左边/右边插入值，返回操作后总长度
> - lpop/rpop：从左边/右边取出值
> - rpoplpush：从右侧取出值放到左侧，如`rpushlpop l1 l2`
> - lrrange：获取指定范围内的值，`lrange l1 0 -1`表示获取所有值
> - lindex：根据下标获取指定值
> - llen：获取 List 长度
> - linsert：在指定 value 前/后插入 value ，如`linsert mylist v1 newvalue`，当有多个 value 时操作从左向右第一个
> - lren：从左向右删除指定个 value 
> - lset：根据下标替换 value，只能操作已有 value

### Set

&emsp;&emsp;Redis 的 Set 是一个 String 键值对的 Hash表，最多可存储`2^32 - 1`个 key （ value 为空）。对Set的操作主要有以下这些：

> - sadd：向 Set 中添加一个或多个 value
> - smembers：取出所有 value
> - sismember：判断 value 是否位于 Set 中
> - scard：获取元素个数
> - srem：删除一个或多个元素
> - spop：随即取出一个值
> - srandmember：随机获取 n 个值，不会删除这些值
> - soce：将一个集合中的值移动到另一个集合，如`smove s1 s2 v3`
> - sinter/sunion/sdiff：取两个集合的交集/并集/差集

### Hash

&emsp;&emsp;Redis 的 Hash 类似 Java 中的 `Map<String, Object>`，数据量少时使用 ziplist ，数据量多时使用 hashtable 。对 Hash 的常用操作有以下这些：

> - hset/hget：设置/读取 hash，如：
>
>   ```redis
>   hset user:1000 name 张三
>   hset user:1000 score 90
>   hget user:1000 score
>   ```
>   
> - hmset：设置多组 field-value 对，如`hmset user:1001 name 猫猫 score 98 address 7-115-猫窝`
>
> - hexist：判断 key 是否具有某 field
>
> - hkeys/kvals：查看 key 中的 field 和 value
>
> - hincrby：为纯数字 value 增加 n 个步进
>
> - hsetnx：为 key 添加不存在的 field-value 对

### Zset

&emsp;&emsp;&emsp;Zset 与 Set相似，为不可重复的集合，但 Zset 加入了“score”来进行排序。对Zset的操作主要有以下这些：

> - zadd：添加元素，如`zadd z1 200 java 200 C++ 300 php`
>
> - zrange：读取范围内的元素，如：
>
>   `zrange z1 0 -1`获取所有元素
>
>   `zrange z1 0 -1 withscores`获取所有元素和 score
>
> - zrangebyscore：根据给出的 score 范围从小到大读取元素，如：
>
>   `zrangebyscore z1 100 200`，读取 score 在 100 到 200之间的元素，同样可使用`withscores`
>
> - zrevrangebyscore：类似`zrangebyscore`，改为从大到小排列
>
> - zcount：统计给出的 score 范围内元素个数
>
> - zrank：统计所给元素的排名（从小到大，从0开始）

## 发布&订阅

&emsp;&emsp;Redis 的发布/订阅（pub/sub）是一种消息通信模式，发送者 (pub) 发送消息，订阅者 (sub) 接收消息。发布/订阅的操作主要有以下这些：

> - subscribe：订阅一个频道
> - psubscribe：订阅一个或多个频道
> - pubsub：查看发布/订阅系统状态
> - punsubscribe：退订频道
> - unsubscribe：退订某个频道
> - publish：向频道内发送信息，如`publish channel Hello`

## Jedis与SpringBoot

### Jedis

&emsp;&emsp;Jedis 是一个使用 Java 操作 Redis 的工具。

0. 导入依赖

   ```xml
   <dependency>
       <groupId>redis.clients</groupId>
       <artifactId>jedis</artifactId>
       <version>3.2.0</version>
   </dependency>
   ```

1. 创建连接

   ```java
   String host = "127.0.0.1";
   Integer port = 6379;
   String password = "123456";
   Jedis jedis = new Jedis(host, port);
   jedis.auth(password);
   System.out.println(jedis.ping());
   jedis.close();
   ```

2. 常用方法

   ```java
   Integer index = 9;
   jedis.select(index);
   jedis.set("zml", "朱孟璐");
   String value = jedis.get("zml");
   List<String> mget = jedis.mget("zly", "zsh", "dzq", "zxq");
   // 基本上前面涉及的命令都可以拿来用
   ```

### SpringBoot 整合 Redis

0. 导入依赖

   ```xml
   <!--spring-boot-starter-data-redis-->
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-redis</artifactId>
   </dependency>
   <!--commons-pool2连接池-->
   <dependency>
       <groupId>org.apache.commons</groupId>
       <artifactId>commons-pool2</artifactId>
   </dependency>
   ```

1. 配置文件

   ```yaml
   spring:
     redis:
       host: localhost         # 主机地址
       port: 6379              # 端口号
       connect-timeout: 10000  # 连接超时时间
       timeout: 10000          # 访问都是时间
       database: 0             # 数据库索引
       jedis:
         pool:
           max-active: 20      # 最大连接数
           max-wait: -1        # 最大阻塞等待时间
           max-idle: 8         # 最大空闲连接数
           min-idle: 0         # 最小空闲连接数
   
   ```

2. 配置类

   ```java
   @EnableCaching
   @Configuration
   public class RedisConfig extends CachingConfigurerSupport {
       @Bean
       public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
           RedisTemplate<String, Object> template = new RedisTemplate<>();
           RedisSerializer<String> redisSerializer = new StringRedisSerializer();
           Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
           ObjectMapper om = new ObjectMapper();
           om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
           om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
           jackson2JsonRedisSerializer.setObjectMapper(om);
           template.setConnectionFactory(factory);
   				// key序列化方式
           template.setKeySerializer(redisSerializer);
   				// value序列化
           template.setValueSerializer(jackson2JsonRedisSerializer);
   				// value hashmap序列化
           template.setHashValueSerializer(jackson2JsonRedisSerializer);
           return template;
       }
   
       @Bean
       public CacheManager cacheManager(RedisConnectionFactory factory) {
           RedisSerializer<String> redisSerializer = new StringRedisSerializer();
           Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
   				// 解决查询缓存转换异常的问题
           ObjectMapper om = new ObjectMapper();
           om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
           om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
           jackson2JsonRedisSerializer.setObjectMapper(om);
   				// 配置序列化（解决乱码的问题）,过期时间600秒
           RedisCacheConfiguration config = 
             RedisCacheConfiguration.defaultCacheConfig()
                   .entryTtl(Duration.ofSeconds(600))
         .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
                   .disableCachingNullValues();
           RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
                   .cacheDefaults(config)
                   .build();
           return cacheManager;
       }
   }
   ```

## 事务和锁

&emsp;&emsp;Redis 的事务可保证一组操作不被打扰地有序执行，但不保证原子化操作。首先使用`multi`开启事务，随后的操作会被放入队列中，使用`exec`依次执行队列中的操作，或使用`discard`取消事务。

&emsp;&emsp;在 Multi 阶段出现错误会导致这个事务立刻失败；在 Exec 阶段某个操作失败不会影响接下来的操作。

&emsp;&emsp;乐观所对数据冲突保持乐观的态度，允许数据操作并发进行，仅在提交操作时进行检测；悲观锁在某一次数据操作时都对数据进行加锁，操作完成后再解锁。在 Redis 事务中，可使用`watch`对一个变量加乐观锁，若事务执行过程中该变量被其他操作修改，该事务会失败。

## 数据持久化

&emsp;&emsp; Redis 提供了两种数据持久化方案—— RDB 和 AOF.

### RDB

&emsp;&emsp;RDB（Redis Database）在指定的时间间隔内将内存中的数据集快照写入硬盘中，默认开启

### AOF

&emsp;&emsp;AOF（Append Only File）以增量日志形式记录每个对数据的操作，默认不开启，开启后 RDB 失效。

## 主从复制

&emsp;&emsp;主机（master）数据更新后，根据配置和策略，自动同步到从机（slave）。 Master 以写为主，Slave 以读为主。实现读写分离，提高性能且实现了容灾快速恢复。

&emsp;&emsp;首先创建每个 Redis 服务的配置文件：

```config
# 以主机6379为例
include /etc/redis/redis.conf
profile /var/run/redis_6379.pid
port 6379
dbfilename dump6379.rdb
```

&emsp;&emsp;使用指定配置文件启动 Redis 服务后，可使用`info replication`查看主从配置情况，在从服务器上使用`slaveof 127.0.0.1 6379`设置其主服务器。

&emsp;&emsp;通过`slaveof`连接主服务器后，从服务器会向主服务器发送数据同步请求，主服务器进行数据备份，将 rdb 文件传给从服务器，随后主服务器若发生数据更新会主动向从服务器发送增量更新。注意，若从服务器重启，需要重新手动连接主服务器。从服务器也可以作为其他服务器的主服务器。

&emsp;&emsp;若主服务器宕机，可直接重启主服务器，或使用`slave no one`将一台从服务器转换为主服务器。

&emsp;&emsp;在生产环境，可以配置“哨兵”来监视主服务器并在主服务器宕机后自动将一台从服务器转换为主服务器。

&emsp;&emsp;首先创建配置文件`sentinel.conf`：

```config 
sentinel monitor mymaster 127.0.0.1 6379 1
# 1 表示同意切换的最小哨兵数
```

&emsp;&emsp;使用`redis-sentinel sentinel.conf`启动监视器，选择主服务器宕机后自动选择题一个从服务器作为主服务器，原主服务器重启后会变成从服务器。选择的依据依次为：

> - 配置文件中的优先级`replica-priority`，数字越小优先级越高
> - 偏移量：与原主服务器数据差异
> - runid（随机生成） 最小

## 集群

### 无中心化集群

&emsp;&emsp;为了让不同实例分担数据，产生了集群；为了解决 IP 地址改变的问题，Redis3 开始有了无中心化集群。在无中心化集群中，任何一个节点都可作为先前的“代理服务器”。

&emsp;&emsp; Redis 集群通过分区（partition）提供一定程度的可用性，即使部分节点失效或无法通信集群仍可处理请求。

### 配置集群

0. 编写节点配置文件

   ```config 
   # 以主机6379为例
   include /etc/redis/redis.conf
   profile /var/run/redis_6379.pid
   port 6379
   dbfilename dump6379.rdb
   # 启用集群
   cluster-enable yes
   # 节点文件，一个服务一个
   cluster-config-file nodes6379.conf
   # 主从切换阈值（毫秒）
   cluster-node-timeout 1500
   # 是否需要所有节点都在线
   cluster-require-fullcoverage no
   ```

1. 启用集群

   ```bash
   redis-cli --cluster create --cluster-reolicas 1 [ip:port]
   # ip和端口为先主后从
   ```

2. 登录集群

   ```bash
   redis-cli -c -p $port
   # 查看集群信息
   cluster nodes
   ```

## 常见问题

### 缓存穿透

&emsp;&emsp;缓存穿透是指客户端进行大量非正常访问、对无效字段发起大量请求，造成数据库压力过大的问题。表现为缓存命中率过低、数据库压力过大。解决方案有：

- 对空值做缓存
- 对数据加可访问白名单
- 使用布隆过滤器
- 实时监控，添加黑名单

### 缓存击穿

&emsp;&emsp;缓存击穿是指对某些字段的访问频率过高，缓存过期，造成数据库压力过大的问题。解决方案有：

- 预先对某些热门数据设置不过期
- 实时调整过期时间
- 加锁

### 缓存雪崩

&emsp;&emsp;缓存雪崩是指在短时间内大量 key 集中过期。造成数据库压力过大的问题。解决方案有：

- 使用多级缓存
- 使用锁或队列
- 对缓存过期做出预警
- 随机化过期时间

### 分布式锁

&emsp;&emsp;在集群场景中，对 JVM 进程和对 Redis 节点的锁都不能对整个集群起到效果，此时就需要分布式锁。实现分布式锁可以基于数据库实现、基于 Redis 实现或使用 Zookeeper，其中基于 Redis 的分布式锁效率更高：

```redis
set lock value nx ex 20
setnx lock value
del lock
```

## Redis6 新特性

### ACL

&emsp;&emsp;ACL（Access Control List, 访问控制列表）可用于控制不同账户的权限：

```redis
acl list            # 查看 ACL 配置
acl whoami
acl setuser user0   # 设置用户权限
```

&emsp;&emsp;`acl list`结果默认为`"user default on nopass ~* &* +@all"`，分别对应用户名、是否启用、密码、可操作的 key、可使用的命令。

### IO多线程

&emsp;&emsp;从 Redis 6.0 开始，Redis 除核心读写操作外，其他部分（与客户端交流等）采用了多线程。
