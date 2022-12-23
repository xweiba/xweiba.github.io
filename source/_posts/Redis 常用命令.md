---
title: Redis 常用命令
date: 2018-12-23 13:13:16
tags:
 - Redis
 - 常用命令
categories:
 - Redis
---

## Window 下安装
1. 下载地址：[https://github.com/MSOpenTech/redis/releases](https://github.com/MSOpenTech/redis/releases)
   - Redis) 支持 32 位和 64 位。这个需要根据你系统平台的实际情况选择，这里我们下载 Redis-x64-xxx.zip压缩包到 C 盘，解压后，将文件夹重新命名为 redis。
2. 运行 
   - 开启服务器
      - 打开一个 cmd 窗口 使用cd命令切换目录到 C:\redis 运行 `redis-server.exe redis.windows.conf`
   - 连接数据库
      - 这时候另启一个cmd窗口，原来的不要关闭，不然就无法访问服务端了
      - 切换到redis目录下运行 redis-cli.exe -h 127.0.0.1 -p 6379 
## Linux 下安装
1. 下载地址：[http://redis.io/download](http://redis.io/download)，下载最新稳定版本。
2. 安装运行
    - 源码安装
    ```sh
    wegt http://download.redis.io/releases/redis-4.0.9.tar.gz
    tar -zvxf redis-4.0.9.tar.gz
    cd redis/
    #编译
    make
    #编译完成文件生成到当前目录下的src下
    cd src
    ```
    - 启动redis
       1. `./redis-server` 注意这种方式启动redis 使用的是默认配置。也可以通过启动参数告诉redis使用指定配置文件使用下面命令启动。
       2. `./redis-server redis.conf` redis.conf是一个默认的配置文件。我们可以根据需要使用自己的配置文件。
    - 运行 
       1. 启动redis服务进程后，就可以使用测试客户端程序redis-cli和redis服务交互了。 比如：
       ```sh
       ./redis-cli
       redis> set foo bar
       OK
       redis> get foo
       "bar"
       ```
       
## Redis 配置
Redis 的配置文件位于 Redis 安装目录下，文件名为 `redis.conf`.  
你可以通过 `CONFIG` 命令查看或设置配置项
----

### 语法 
#### `Redis CONFIG` 命令格式如下：
> redis 127.0.0.1:6379> CONFIG GET CONFIG_SETTING_NAME
#### 实例
> redis 127.0.0.1:6379> CONFIG GET loglevel
> 1) "loglevel"
> 2) "notice"

使用 * 号获取所有配置项：
#### 实例
```
redis 127.0.0.1:6379> CONFIG GET *
  1) "dbfilename"
  2) "dump.rdb"
  3) "requirepass"
  4) ""
  5) "masterauth"
  6) ""
  7) "unixsocket"
  8) ""
  9) "logfile"
 10) ""
 11) "pidfile"
 12) "/var/run/redis.pid"
 13) "maxmemory"
 14) "0"
 15) "maxmemory-samples"
 16) "3"
 17) "timeout"
 18) "0"
 19) "tcp-keepalive"
 20) "0"
 21) "auto-aof-rewrite-percentage"
 22) "100"
 23) "auto-aof-rewrite-min-size"
 24) "67108864"
 25) "hash-max-ziplist-entries"
 26) "512"
 27) "hash-max-ziplist-value"
 28) "64"
 29) "list-max-ziplist-entries"
 30) "512"
 31) "list-max-ziplist-value"
 32) "64"
 33) "set-max-intset-entries"
 34) "512"
 35) "zset-max-ziplist-entries"
 36) "128"
 37) "zset-max-ziplist-value"
 38) "64"
 39) "hll-sparse-max-bytes"
 40) "3000"
 41) "lua-time-limit"
 42) "5000"
 43) "slowlog-log-slower-than"
 44) "10000"
 45) "latency-monitor-threshold"
 46) "0"
 47) "slowlog-max-len"
 48) "128"
 49) "port"
 50) "6379"
 51) "tcp-backlog"
 52) "511"
 53) "databases"
 54) "16"
 55) "repl-ping-slave-period"
 56) "10"
 57) "repl-timeout"
 58) "60"
 59) "repl-backlog-size"
 60) "1048576"
 61) "repl-backlog-ttl"
 62) "3600"
 63) "maxclients"
 64) "4064"
 65) "watchdog-period"
 66) "0"
 67) "slave-priority"
 68) "100"
 69) "min-slaves-to-write"
 70) "0"
 71) "min-slaves-max-lag"
 72) "10"
 73) "hz"
 74) "10"
 75) "no-appendfsync-on-rewrite"
 76) "no"
 77) "slave-serve-stale-data"
 78) "yes"
 79) "slave-read-only"
 80) "yes"
 81) "stop-writes-on-bgsave-error"
 82) "yes"
 83) "daemonize"
 84) "no"
 85) "rdbcompression"
 86) "yes"
 87) "rdbchecksum"
 88) "yes"
 89) "activerehashing"
 90) "yes"
 91) "repl-disable-tcp-nodelay"
 92) "no"
 93) "aof-rewrite-incremental-fsync"
 94) "yes"
 95) "appendonly"
 96) "no"
 97) "dir"
 98) "/home/deepak/Downloads/redis-2.8.13/src"
 99) "maxmemory-policy"
100) "volatile-lru"
101) "appendfsync"
102) "everysec"
103) "save"
104) "3600 1 300 100 60 10000"
105) "loglevel"
106) "notice"
107) "client-output-buffer-limit"
108) "normal 0 0 0 slave 268435456 67108864 60 pubsub 33554432 8388608 60"
109) "unixsocketperm"
110) "0"
111) "slaveof"
112) ""
113) "notify-keyspace-events"
114) ""
115) "bind"
116) "" 
```
#### 编辑配置
你可以通过修改 redis.conf 文件或使用 CONFIG set 命令来修改配置。

#### 语法
CONFIG SET 命令基本语法：
> redis 127.0.0.1:6379> CONFIG SET CONFIG_SETTING_NAME NEW_CONFIG_VALUE

#### 实例
```
redis 127.0.0.1:6379> CONFIG SET loglevel "notice"
OK
redis 127.0.0.1:6379> CONFIG GET loglevel

1) "loglevel"
2) "notice"
```
---
### 参数说明
redis.conf 配置项说明如下：
1. Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
   > daemonize no

2. 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
   > pidfile /var/run/redis.pid

3. 指定Redis监听端口，默认端口为6379，作者在自己的一篇博文中解释了为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字
   > port 6379

4. 绑定的主机地址
   > bind 127.0.0.1

5. 当客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能()
   > timeout 300

6. 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
   > loglevel verbose

7. 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
   > logfile stdout

8. 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id 
   > databases 16

9. 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
   > save <seconds> <changes>
   > Redis默认配置文件中提供了三个条件:    
   >  save 900 1   
   >  save 300 10    
   >  save 60 10000   
   >  分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。

10. 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
    > rdbcompression yes

11. 指定本地数据库文件名，默认值为dump.rdb
    > dbfilename dump.rdb

12. 指定本地数据库存放目录
    > dir ./

13. 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
    >  slaveof <masterip> <masterport>

14. 当master服务设置了密码保护时，slav服务连接master的密码
    > masterauth <master-password>

15. 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭
    > requirepass foobared

16. 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
    > maxclients 128

17. 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
    > maxmemory <bytes>

18. 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
    > appendonly no

19. 指定更新日志文件名，默认为appendonly.aof
    > appendfilename appendonly.aof

20. 指定更新日志条件，共有3个可选值： 
    > no：表示等操作系统进行数据缓存同步到磁盘（快）   
    > always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全）    
    > everysec：表示每秒同步一次（折衷，默认值）   
    > appendfsync everysec

21. 指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
    > vm-enabled no

22. 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
    > vm-swap-file /tmp/redis.swap

23. 将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
    > vm-max-memory 0

24. Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值
    > vm-page-size 32

25. 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
    > vm-pages 134217728

26. 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4
    > vm-max-threads 4

27. 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启
    > glueoutputbuf yes

28. 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
    > hash-max-zipmap-entries 64   
    > hash-max-zipmap-value 512

29. 指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）
    > activerehashing yes

30. 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
    > include /path/to/local.conf

---

## Redis 数据类型
Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。
### String（字符串）
string是redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value。   
string类型是二进制安全的。意思是redis的string可以包含任何数据。比如jpg图片或者序列化的对象 。   
string类型是Redis最基本的数据类型，一个键最大能存储512MB。
#### 实例
> redis 127.0.0.1:6379> SET name "runoob"   
> OK    
> redis 127.0.0.1:6379> GET name    
> "runoob"

在以上实例中我们使用了 Redis 的 SET 和 GET 命令。键为 name，对应的值为 runoob。   
注意：一个键最大能存储512MB。

---
### Hash（哈希）
Redis hash 是一个键值(key=>value)对集合。   
Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。
#### 实例
> redis> HMSET myhash field1 "Hello" field2 "World"   
> "OK"   
> redis> HGET myhash field1   
> "Hello"
> redis> HGET myhash field2   
> "World"

以上实例中 hash 数据类型存储了包含用户脚本信息的用户对象。    
实例中我们使用了 **Redis HMSET**, **HGETALL** 命令，user:1 为键值。   
每个 hash 可以存储 `${2}^{32}$`-1 键值对（40多亿）。
---
### List（列表）
Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。

#### 实例
> redis 127.0.0.1:6379> lpush runoob redis   
> (integer) 1   
> redis 127.0.0.1:6379> lpush runoob mongodb  
> (integer) 2  
> redis 127.0.0.1:6379> lpush runoob rabitmq  
> (integer) 3  
> redis 127.0.0.1:6379> lrange runoob 0 10  
> 1\) "rabitmq"  
> 2\) "mongodb"  
> 3\) "redis"  
> redis 127.0.0.1:6379>

- 列表最多可存储 `${2}^{32}$` - 1 元素 (4294967295, 每个列表可存储40多亿)。
---
### Set（集合）
Redis的Set是string类型的无序集合。  
集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)。

#### sadd 命令
添加一个 string 元素到 key 对应的 set 集合中，成功返回1，如果元素已经在集合中返回 0，如果 key 对应的 set 不存在则返回错误。
> sadd key member

#### 实例
> redis 127.0.0.1:6379> sadd runoob redis  
> (integer) 1  
> redis 127.0.0.1:6379> sadd runoob mongodb  
> (integer) 1  
> redis 127.0.0.1:6379> sadd runoob rabitmq  
> (integer) 1  
> redis 127.0.0.1:6379> sadd runoob rabitmq  
> (integer) 0  
> redis 127.0.0.1:6379> smembers runoob  
> 1\) "redis"  
> 2\) "rabitmq"  
> 3\) "mongodb"  

注意：以上实例中 rabitmq 添加了两次，但根据集合内元素的唯一性，第二次插入的元素将被忽略。  
集合中最大的成员数为 `$2^{32}$` - 1(4294967295, 每个集合可存储40多亿个成员)。
---

### zset(sorted set：有序集合)
Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。  
不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。  
zset的成员是唯一的,但分数(score)却可以重复。

#### zadd 命令
添加元素到集合，元素在集合中存在则更新对应score
> zadd key score member 
#### 实例
> redis 127.0.0.1:6379> zadd runoob 0 redis  
> (integer) 1  
> redis 127.0.0.1:6379> zadd runoob 0 mongodb  
> (integer) 1  
> redis 127.0.0.1:6379> zadd runoob 0 rabitmq  
> (integer) 1  
> redis 127.0.0.1:6379> zadd runoob 0 rabitmq  
> (integer) 0  
> redis 127.0.0.1:6379> > ZRANGEBYSCORE runoob 0 1000  
> 1\) "mongodb"  
> 2\) "rabitmq"  
> 3\) "redis"

---
## 命令

1. `ping` 连接到本地的 redis 服务并执行 PING 命令，该命令用于检测 redis 服务是否启动。
2. `redis-cli -h host -p port -a password` 远程连接

3. 常用命令
   - key相关 
       - `DEL key` : 删除key
       - `DUMP key` : 返回序列化后的key value
       - `EXISTS key` : 检查key是否存在
       - `EXPIRE key seconds` : 给key设置过期时间, 单位为秒
       - `RERSIS key` : 移除key的过期时间, key将持久保存
       - `RENAME key newKey` : 修改key的名称
       - `RENAMENX key newKey` : 仅当newkey不存在时,才将key改名为newkey
       - `TYPE key` : 获取key所存储的值的类型
    - String 相关
       - `SET key vlaue` : 设定指定key的值
       - `GET key` : 获取指定key的值
       - `GERTANGE key start end` : 返回key中字符串的下标由start开始end结束的子字符
       - `GETSET key value` : 设定key的新value并返回原value
       - `SETNX key vlaue`: 只有当key不存在时才设置key的值
       - `STRLEN key` : 返回key所存储的字符串值的长度.
       - `APPEND key value` : 如果key已存在并且时一个字符串, 在原值后追加vlaue

4. 键(key)
    - Redis 键命令用于管理 redis 的键。
    - Redis 键命令的基本语法如下：
      > redis 127.0.0.1:6379> SET runoobkey redis
    
    - keys 命令
      > `DEL key` : 删除key  
      > `DUMP key` : 返回序列化后的key value  
      > `EXISTS key` : 检测key是否存在
      > `EXPIRE key seconds` : 给key设定过期时间, 单位为秒
      > `EXPIREAT key timestamp` : EXPIREAT 的作用和 EXPIRE 类似，都用于为 key 设置过期时间。不同在于 EXPIREAT 命令接受的时间参数是 UNIX 时间戳(unix timestamp) 
      > `PEXPIRE key milliseconds` : 设置 key 的过期时间以毫秒计  
      > `PEXPIREAT key milliseconds-timestamp` : 设置 key 过期时间的时间戳(unix timestamp) 以毫秒计

## 实现
1. 原生实现 [详情连接](https://blog.csdn.net/q547550831/article/details/55048591)



# 登陆

`--raw`: 显示中文  
redis-cil --raw -a password -h host -p port

# 删除key
del xxx

# 查看所有key
keys *;

# 删除所有key
./redis-cli -h 192.168.116.58 -p 9779 -a xxx keys "*"| xargs ./redis-cli -h 192.168.116.58 -p 9779 -a xxx del

# 查看redis内存占用,cpu等基础信息
info


# 删除指定testId报表缓存
```
/home/redis/redis-3.0.5/bin/redis-cli -h 127.0.0.1 -p 9779 -a password keys xxxx:testId:938254998368718848* | xargs /home/redis/redis-3.0.5/bin/redis-cli -h 127.0.0.1 -p 9779 -a password del
```

清除所有数据: `flushall`

遍历输出所有数据: `keys * `

模糊遍历输出: `keys *session*`

[scan: ](http://redisdoc.com/key/scan.html)
```
KEYS命令一次性返回所有匹配的key，所以，当redis中的key非常多时，对于内存的消耗和redis服务器都是一个隐患，对于Redis 2.8以上版本给我们提供了一个更好的遍历key的命令 SCAN.

SCAN命令是一个基于游标的迭代器。这意味着命令每次被调用都需要使用上一次这个调用返回的游标作为该次调用的游标参数，以此来延续之前的迭代过程

当SCAN命令的游标参数（即cursor）被设置为 0 时， 服务器将开始一次新的迭代， 而当服务器向用户返回值为 0 的游标时， 表示迭代已结束。

看它 1)返回的游标. 
127.0.0.1:6379> scan 99
1) "0"
2) 1) "spring:session:sessions:expires:4c7067ad-7b2f-44ce-a661-44f7a78901d7"
127.0.0.1:6379> scan 0
1) "0"
2) 1) "spring:session:sessions:4c7067ad-7b2f-44ce-a661-44f7a78901d7"
   2) "spring:session:expirations:1532337660000"
   3) "spring:session:sessions:expires:4c7067ad-7b2f-44ce-a661-44f7a78901d7"

```


获取key的类型: `type key`

输出String key值: `get key`
