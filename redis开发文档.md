> [TOC]
>

# 一：缓存概念：

缓存是为了调节速度不一致的两个或多个不同的物质的速度，在中间对速度较快的一方起到一个加:速访问速度较慢的一方的作用，比如 CPU 的一级、二级缓存是保存了 CPU
最近经常访问的数据，内存是保存 CPU经常访问硬盘的数据，而且硬盘也有大小不一的缓存，甚至是物理服务器的 raid卡有也缓存，都是为了起到加速 CPU 访问硬盘数据的目的，因为 CPU 的速度太快了，CPU需要的数据硬盘往往不能在短时间内满足 CPU 的需求，因此 PCU 缓存、内存、Raid卡以及硬盘缓存就在一定程度上满足了 CPU 的数据需求，即 CPU从缓存读取数据可以大幅提高 CPU 的工作效率。

## 1.1：系统缓存

### 1.1.1：buffer 与cache：

buffer：缓冲也叫写缓冲，一般用于写操作，可以将数据先写入内存在写入磁盘，buffer一般用于写缓冲，用于解决不同介质的速度不一致的缓冲，先将数据临时写入到里自己最近的地方，以提高写入速度，CPU会把数据线写到内存的磁盘缓冲区，然后就认为数据已经写入完成看，然后内核的线程在后面的时间在写入磁盘，所以服务器突然断电会丢失内存中的部分数据。cache：缓存也叫读缓存，一般用于读操作，CPU读文件从内存读，如果内存没有就先从硬盘读到内存再读到CPU，将需要频繁读取的数据放在里自己最近的缓存区域，下次读取的时候即可快速读取。

### 1.1.2：cache 的保存位置：

客户端：浏览器内存：本地服务器、远程服务器 硬盘：本机硬盘、远程服务器硬盘-内存-远程内存-硬盘-远程硬盘。

### 1.1.3：cache 的特性：

过期时间强制过期，源网站更新图片后 CDN是不会更新的，需要强制是图片缓存过期命中率，即缓存的读取命中率

## 1.2：用户层缓存：

### 1.2.1：DNS 缓存：

默认为 60 秒，即 60 秒之内在访问同一个域名就不在进行 DNS 解析： 查看 chrome浏览器的 DNS 缓存：

chrome://net-internals/\#dns

![](media/ab07c5e3ba9aead4a670b40b2e94987a.png)

### 1.2.2火狐浏览器缓存：

![](media/38b172e0c7180193f8d238ef01528ae1.png)

### 1.2.3浏览器缓存过期机制：

#### 1.2.3.1最后修改时间：

系统调用会获取文件的最后修改时间，如果没有发生变化就返回给浏览器 304的状态码，表示没有发生变化，然后浏览器就使用的本地的缓存展示资源

![](media/b8aa3912e3f24e390dedeb1836cb2ddb.jpg)



#### 1.2.3.2：Etag标记：

基于 Etag 标记是否一致做判断页面是否发生过变化

![](media/0a4c88f007a0630224dc9a64585e122f.jpg)

#### 1.2.3.3：过期时间：

以上两种都需要发送请求，即不管资源是否过期都要发送请求进行协商，这样会消耗不必要的时间因此有了缓存的过期时间，即第一次请求资源的时候带一个资源的过期时间，默认为30天，当前这种方式使用的比表较多，但是无法保证客户的时间都是准确并且一致的，因此假如一个最大生存周期使用用户本地的时间计算缓存数据是否超过多少天，下面的过期时间为2027 年，但是缓存的最大生存周期计算为天等于 3650 天即 10 年，过期时间如下：

![](media/471843bf4d27d301e685e6824470a335.jpg)

## 1.3：CDN缓存：

### 1.3.1：什么是 CND：

内容分发网络（Content DeliveryNetwork），通过将服务内容分发至全网加速节点，利用全球调度系统使用户能够就近获取，有效降低访问延迟，提升服务可用性，CDN第一降低机房的使用带宽，因为很多资源通过 CDN就直接返回用户了，第二解决不同运营商之间的互联，因为可以让联通的网络访问联通让电信的网络访问电信，起到加速用户访问的目的，第三：解决用户访问的地域问题，就近返回用户资源。

百度 CDN：<https://cloud.baidu.com/product/cdn.html>

阿里 CDN：<https://www.aliyun.com/product/cdn?spm=5176.8269123.416540.50.728y8n>

腾讯 CDN：<https://www.qcloud.com/product/cdn>

### 1.3.2：用户请求 CDN 流程：

提前对静态内容进行预缓存，避免大量的请求回源，导致主站网络带宽被打满而导致数据无法更新另外CDN 可以将数据根据访问的热度不同而进行不同级别的缓存，例如访问量最高的资源访问
CDN 边缘节点的内存，其次的放在 SSD 或者SATA，再其次的放在云存储，这样兼顾了速度与成本。

![](media/eeec0edd812d83e6e4866472a9661e86.jpg)

### 1.3.3：CDN 主要优势：

提前对静态内容进行预缓存，避免大量的请求回源，导致主站网络带宽被打满而导致数据无法更新，另外 CDN可以将数据根据访问的热度不通而进行不通级别的缓存，例如访问量最高的资源访问 CDN
边缘节点的内存，其次的放在 SSD 或者SATA，再其次的放在云存储，这样兼顾了速度与成本。缓存-缓存到最快的地方如内存，缓存的数据准确命中率高，访问速度就快调度准确-将用户调度到最近的边缘节点性能优化-CDN专门用于缓存响应速度快安全相关-抵御攻击节省带宽：由于用户请求由边缘节点响应，因此大幅降低到源站带宽。

1.4：应用层缓存：
--------------

Nginx、PHP 等 web 服务可以设置应用缓存以加速响应用户请求，另外有些解释性语言比如PHP/Python不能直接运行，需要先编译成字节码，但字节码需要解释器解释为机器码之后才能执行，因此字节码也是一种缓存，有时候会出现程序代码上线后字节码没有更新的现象。

1.5：其他层面缓存：
----------------

CPU 缓存(L1 的数据缓存和 L1 的指令缓存)、二级缓存、三级缓存、磁盘缓存、RAID卡分布式缓存：redis、memcache、MegaCli64 -LDinfo -Lall -aAll 

# 二、redis 部署与使用：

## 2.1：redis基础：

官网地址：<https://redis.io/>Redis 和 Memcached 是非关系型数据库也成为 NoSQL，MySQL、Mariadb、SQL-Server、PostgreSQL、Oracle 数据库属于关系型数据(RDBMS, Relational Database
Management System)

### 2.1.1：redis 简介：

Redis(Remote Dictionary Server)在 2009 年发布，开发者 Salvatore Sanfilippo是意大利开发者，他本想为自己的公司开发一个用于替换 MySQL 的产品Redis，但是没有想到他把 Redis 开源后大受欢迎，短短几年，Redis就有了很大的用户群体，目前国内外使用的公司有知乎网、新浪微博、GitHub 等redis是一个开源的、遵循BSD 协议的、基于内存的而且目前比较流行的键值数据库(key-value-database) 是一个非关系型数据库，redis提供将内存通过网络远程共享的一种服务，提供类似功能的还有memcache，但相比memcache，redis 还提供了易扩展、高性能、具备数据持久性等功能。

Redis 在高并发、低延迟环境要求比较高的环境使用量非常广泛，目前 redis 在DB-Engine 月排行榜https://db-engines.com/en/ranking>中一直比较靠前，而且一直是键值型存储类的首位。

![](media/ea9a678129f2fac6697e647c8ae73cbd.png)

### 2.1.2：redis对比memcached：

支持数据的持久化：可以将内存中的数据保持在磁盘中，重启 redis服务或者服务器之后可以从备份文件中恢复数据到内存继续使用。支持更多的数据类型：支持string(字符串)、hash(哈希数据)、list(列表)、set(集合)、zet(有序集合)支持数据的备份：可以实现类似于数据的 master-slave模式的数据备份，另外也支持使用快照+AOF 支持更大的 value 数据：memcache 单个 keyvalue 最大只支持 1MB，而 redis 最大支持 512MB。Redis 是单线程，而 memcache是多线程，所以单机情况下没有 memcache 并发高，但 redis支持分布式集群以实现更高的并发，单 Redis 实例可以实现数万并发。支持集群横向扩展：基于 redis cluster的横向扩展，可以实现分布式集群，大幅提升性能和数据安全性。都是基于 C 语言开发。

### 2.1.3：redis 典型应用场景：

Session 共享：常见于web 集群中的 Tomcat 或者 PHP 中多 web 服务器 session共享消息队列：ELK 的日志缓存、部分业务的订阅发布系统计数器：访问排行榜、商品浏览数等和次数相关的场景缓存：数据查询、电商网站商品信息、新闻内容微博/微信社交场合：共同好友、点赞评论等

## 2.2：Redis安装及使用：

官方下载地址：<http://download.redis.io/releases/>

### 2.2.1yum 安装redis：

在 centos 系统上需要安装 epel 源。

![](media/ecd6e64a0b5e701c09e9e02b62102184.png)

#### 2.2.1.1：查看 yum仓库redis版本：

#### 2.2.1.2：安装redis：

```bash
yum install redis –y
systemctl start redis && systemctl enable redis
[root@redis-s3 ~]#redis-cli
127.0.0.1:6379> info
Server
redis_version:3.2.12 redis_git_sha1:00000000 redis_git_dirty:0
redis_build_id:7897e7d0e13773f redis_mode:standalone
os:Linux 3.10.0-862.el7.x86_64 x86_64 arch_bits:64
```

### 2.2.2：编译安装 redis：

下载当前最新 release 版本 redis 源码包：

![](media/0d705d8d152c0c7e218a2ced76c9587f.png)

#### 2.2.2.1编译安装命令：

官方的安装命令：<https://redis.io/download>

```bash
pwd
/usr/local/src
tar xf redis-VERSION.tar.gz 
cd redis-VERSION
make PREFIX=/usr/local/redis install
ll /usr/local/redis/
drwxr-xr-x 2 root root 134 Dec 13 09:21 bin
mkdir /usr/local/redis/etc
cp /usr/local/src/redis-VERSION/redis.conf /usr/local/redis/etc/
```

#### 2.2.2.2前台启动redis：

```bash
/usr/local/redis/bin/redis-server /usr/local/redis/etc/redis.conf
```



#### 2.2.2.3：解决当前的警告提示：

##### 2.2.2.3.1：tcp-backlog：

The backlog argument defines the maximum length to which the queue of pending connections for sockfd may grow. If a connection request arrives when the queue is full, the client may receive an error with an indication of ECONNREFUSED or,if the underlying protocol supports retransmission, the request may be ignored so that a later reattempt at connection succeeds.

backlog 参数控制的是三次握手的时候 server 端收到 client ack 确认号之后的队列值。

```bash
net.core.somaxconn = 512 
```



##### 2.2.2.3.2：vm.overcommit_memory：

0、表示内核将检查是否有足够的可用内存供应用进程使用；如果有足够的可用内存，内存申请允许；否则，内存申请失败，并把错误返回给应用进程。

1、表示内核允许分配所有的物理内存，而不管当前的内存状态如何。

2、表示内核允许分配超过所有物理内存和交换空间总和的内存

```bash
vm.overcommit_memory = 1 
```



##### 2.2.2.3.3：transparent hugepage：

开启大页内存动态分配，需要关闭让 redis 负责内存管理。

```bash
echo never > /sys/kernel/mm/transparent_hugepage/enabled 
```



##### 2.2.2.3.4：再次启动 redis：

![](media/3dc075fcb62a0e6fddcc232be78e5e39.jpg)

将以上配置同步到其他 redis 服务器。

#### 2.2.4:编辑redis服务启动脚本:

```bash
cat /usr/lib/systemd/system/redis.service

[Unit]
Description=Redis persistent key-value database After=network.target
After=network-online.target Wants=network-online.target

[Service]
ExecStart=/usr/local/redis/bin/redis-server /usr/local/redis/etc/redis.conf --supervised systemd
ExecReload=/bin/kill -s HUP $MAINPID 
ExecStop=/bin/kill -s QUIT $MAINPID
Type=notify
User=root
Group=root
RuntimeDirectory=redis RuntimeDirectoryMode=0755

[Install]
WantedBy=multi-user.target
```

#### 2.2.2.5：创建redis用户和数据目录：

```bash
groupadd -g 1000 redis && useradd -u 1000 -g 1000 redis -s /sbin/nologin
mkdir -pv /usr/local/redis/{etc,logs,data,run}
chown redis.redis -R
/usr/local/redis/
```

#### 2.2.2.6:验证redis启动：

```bash
useradd redis -s /sbin/nologin
chown redis.redis /usr/local/redis/ -R
chown redis.redis /usr/local/redis/ -R #注意目录权限
```

![](media/dfaa7a45870860314a496e3fa02b7d3e.png)

#### 2.2.2.7：使用客户端连接redis：

```bash
/usr/local/redis/bin/redis-cli -h IP/HOSTNAME -p PORT -a PASSWORD 
```

#### 2.2.2.8：创建命令软连接：

```bash
ln -sv /usr/local/redis/bin/redis-* /usr/bin/
‘/usr/bin/redis-benchmark’ -> ‘/usr/local/redis/bin/redis-benchmark’
‘/usr/bin/redis-check-aof’ -> ‘/usr/local/redis/bin/redis-check-aof’
‘/usr/bin/redis-check-rdb’ -> ‘/usr/local/redis/bin/redis-check-rdb’
‘/usr/bin/redis-cli’ -> ‘/usr/local/redis/bin/redis-cli’
‘/usr/bin/redis-sentinel’ -> ‘/usr/local/redis/bin/redis-sentinel’
‘/usr/bin/redis-server’ -> ‘/usr/local/redis/bin/redis-server’
```

#### 2.2.2.9编译安装后的命令：

```bash
ll /usr/local/redis/bin/
total 32656
-rwxr-xr-x 1 redis redis 4365488 Dec 13 09:21 redis-benchmark #redis性能测试工具
-rwxr-xr-x 1 redis redis 8088920 Dec 13 09:21 redis-check-aof #AOF文件检查工具
-rwxr-xr-x 1 redis redis 8088920 Dec 13 09:21 redis-check-rdb #RDB文件检查工具
-rwxr-xr-x 1 redis redis 4800752 Dec 13 09:21 redis-cli #redis #客户端工具
lrwxrwxrwx 1 redis redis 12 Dec 13 09:21 redis-sentinel -> redis-server #哨兵，软连接到server
-rwxr-xr-x 1 redis redis 8088920 Dec 13 09:21 redis-server #redis 服务端
```

### 2.2.3：windows 安装redis：

Windows 版 Redis 下载地址：

<https://github.com/MicrosoftArchive/redis/releases>强烈不推荐在生产环境使用 Windows 系统运行 Redis 服务。

#### 2.2.3.1：解压后的目录：

![1553654816965](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553654816965.png)

#### 2.2.3.2:编辑配置文件并执行redis-server.exe:

```powershell
C:\Users\ZhangShiJie\>cd C:\Users\ZhangShiJie\Desktop\Redis-x64-3.0.504
C:\Users\ZhangShiJie\Desktop\Redis-x64-3.0.504\>redis-server.exe redis.windows.conf
```

![](media/123cc8bd02c67001b6bbb191f76eeb03.png)

#### 2.2.3.3：验证Redis服务端口：

![](media/e2a2086e74b7f20c224affca0d9be7bb.png)

#### 2.2.3.4：执行redis-cli客户端：

使用客户端连接到 Windows 版 redis server，进行创建 key 与获取 key 操作。

![](media/e9af0e1837a882c975b191e93f994bbf.png)

### 2.2.4：连接到Redis：

主要分为运维人员的连接和程序的连接

##### 2.2.4.1本机非密码连接：

```bash
redis-cli 
```

##### 2.2.4.2：跨主机非密码连接：

```bash
redis-cli -h HOSTNAME/IP -p PORT
```

##### 2.2.4.3：跨主机密码连接：

```
redis-cli -h HOSTNAME/IP -p PORT -a PASSWORD
```

##### 2.2.4.4:python连接方式：

```python
#!/bin/env python
#Author: ZhangJie
import redis import time
pool = redis.ConnectionPool(host="192.168.7.101", port=6379,password="")
r = redis.Redis(connection_pool=pool) for i in range(100):
r.set("k%d" % i,"v%d" % i) time.sleep(1) data=r.get("k%d" % i) print(data)
```

## 2.3：redis配置文件：

### 2.3.1：redis 主要配置项：

```bash
bind 0.0.0.0 #监听地址，可以用空格隔开后多个监听 IP
protected-mode yes #redis3.2 之后加入的新特性，在没有设置 bind IP和密码的时候只允许访问127.0.0.1:6379
port 6379 #监听端口
tcp-backlog 511 #三次握手的时候 server 端收到 client ack确认号之后的队列值。timeout 0 \#客户端和 Redis 服务端的连接超时时间，默认是0，表示永不超时。
tcp-keepalive 300 #tcp 会话保持时间
daemonize no #认情况下 redis不是作为守护进程运行的，如果你想让它在后台运行，你就把它改成yes,当 redis 作为守护进程运行的时候，它会写一个 pid 到 /var/run/redis.pid文件里面
supervised no #和操作系统相关参数，可以设置通过 upstart 和 systemd 管理 Redis守护进程，centos 7以后都使用 systemd
pidfile /var/run/redis_6379.pid #pid 文件路径
loglevel notice #日志级别
logfile "" #日志路径
databases 16 #设置db 库数量，默认 16 个库
always-show-logo yes #在启动 redis 时是否显示 log
save 900 1 #在 900 秒内有一个键内容发生更改就出就快照机制
save 300 10
save 60 10000
stop-writes-on-bgsave-error yes #快照出错时是否禁止 redis写入操作
rdbcompression yes #持久化到 RDB文件时，是否压缩，"yes"为压缩，"no"则反之
rdbchecksum yes #是否开启 RC64校验，默认是开启
dbfilename dump.rdb #快照文件名
dir ./ #快照文件保存路径
replica-serve-stale-data yes#当从库同主库失去连接或者复制正在进行，从机库有两种运行方式：1) 如果replica-serve-stale-data 设置为 yes(默认设置)，从库会继续响应客户端的请求。2)如果 replica-serve- stale-data设置为no，除去指定的命令之外的任何请求都会返回一个错误"SYNC with master inprogress"
replica-read-only yes #是否设置从库只读
repl-diskless-sync no #是否使用 socket 方式复制数据，目前 redis复制提供两种方式，disk 和 socket，如果新的 slave 连上来或者重连的 slave无法部分同步，就会执行全量同步，master 会生成 rdb 文件，有2 种方式：disk 方式是master 创建一个新的进程把 rdb 文件保存到磁盘，再把磁盘上的 rdb 文件传递给slave，socket 是 master 创建一个新的进程，直接把 rdb 文件以 socket 的方式发给slave，disk 方式的时候，当一个 rdb 保存的过程中，多个 slave 都能共享这个 rdb文件，socket 的方式就是一个个 slave顺序复制，只有在磁盘速度缓慢但是网络相对较快的情况下才使用 socket方式，否则使用默认的 disk 方式
repl-diskless-sync-delay 5 #diskless 复制的延迟时间，设置 0为关闭，一旦复制开始还没有结束之前master 节点不会再接收新 slave 的复制请求，直到下一次开始
repl-ping-slave-period 10 #slave 根据 master 指定的时间进行周期性的 PING 监测
repl-timeout 60 #复制链接超时时间，需要大于repl-ping-slave-period，否则会经常报超时
repl-disable-tcp-nodelay no #在 socket 模式下是否在 slave 套接字发送 SYNC之后禁用 TCP_NODELAY， 如果你选择“yes”Redis 将使用更少的 TCP 包和带宽来向 slaves发送数据。但是这将使数据传输到 slave 上有延迟，Linux 内核的默认配置会达到 40毫秒，如果你选择了 "no" 数据传输到 salve 的延迟将会减少但要使用更多的带宽
repl-backlog-size 1mb #复制缓冲区大小，只有在 slave 连接之后才分配内存。
repl-backlog-ttl 3600 #多次时间 master 没有 slave 连接，就清空 backlog 缓冲区。
replica-priority 100 #当master 不可用，Sentinel 会根据 slave 的优先级选举一个master。最低的优先级的 slave，当选 master。而配置成 0，永远不会被选举。
requirepass foobared #设置 redis 连接密码
rename-command #重命名一些高危命令
maxclients 10000 #最大连接客户端
maxmemory #最大内存，单位为 bytes 字节，8G 内存的计算方式8(G)\*1024(MB)\*1024(KB)\*1024(Kbyte)，需要注意的是 slave 的输出缓冲区是不计算在maxmemory 内。
appendonly no #是否开启 AOF 日志记录，默认 redis 使用的是rdb方式持久化，这种方式在许多应用中已经足够用了。但是 redis如果中途宕机，会导致可能有几分钟的数据丢失，根据 save 来策略进行持久化，Append Only File 是另一种持久化方式，可以提供更好的持久化特性。Redis会把每次写入的数据在接收后都写入 appendonly.aof 文件，每次启动时Redis都会先把这个文件的数据读入内存里，先忽略 RDB 文件appendfilename"appendonly.aof" #AOF 文件名
appendfsync everysec #aof 持久化策略的配置,no 表示不执行fsync,由操作系统保证数据同步到磁盘,always表示每次写入都执行fsync，以保证数据同步到磁盘,everysec表示每秒执行一次fsync，可能会导致丢失这 1s 数据。no-appendfsync-on-rewrite no 在 aof rewrite 期间,是否对 aof 新记录的 append暂缓使用文件同步策略,主要考虑磁盘 IO 开支和请求阻塞时间。默认为no,表示"不暂缓",新的 aof 记录仍然会被立即同步Linux 的默认 fsync 策略是 30秒，如果为 yes 可能丢失 30 秒数据，但由于 yes性能较好而且会避免出现阻塞因此比较推荐。
auto-aof-rewrite-percentage 100 # 当 Aof log 增长超过指定比例时，重写 logfile， 设置为 0 表示不自动重写 Aof 日志，重写是为了使 aof体积保持最小，而确保保存最完整的数据。
auto-aof-rewrite-min-size 64mb #触发 aof rewrite 的最小文件尺寸
aof-load-truncated yes #是否加载由于其他原因导致的末尾异常的 AOF 文件(主进程被kill/断电等)
aof-use-rdb-preamble yes #redis4.0 新增 RDB-AOF混合持久化格式，在开启了这个功能之后，AOF 重写产生的文件将同时包含 RDB格式的内容和 AOF 格式的内容，其中 RDB 格式的内容用于记录已有的数据，而 AOF格式的内存则用于记录最近发生了变化的数据，这样 Redis 就可以同时兼有 RDB持久化和AOF持久化的优点（既能够快速地生成重写文件，也能够在出现问题时，快速地载入数据）。
lua-time-limit 5000 #lua 脚本的最大执行时间，单位为毫秒
cluster-enabled yes #是否开启集群模式，默认是单机模式
cluster-config-file nodes-6379.conf #由 node 节点自动生成和的集群配置文件
cluster-node-timeout 15000 #集群中 node 节点连接超时时间
cluster-replica-validity-factor 10 #在执行故障转移的时候可能有些节点和 master断开一段时间数据比较旧，这些节点就不适用于选举为master，超过这个时间的就不会被进行故障转移
cluster-migration-barrier 1 #一个主节点拥有的至少正常工作的从节点，即如果主节点的 slave节点故障后会将多余的从节点分配到当前主节点成为其新的从节点。
cluster-require-full-coverage yes #集群槽位覆盖，如果一个主库宕机且没有备库就会出现集群槽位不全那么 yes 情况下redis 集群槽位验证不全就不再对外提供服务，而 no则可以继续使用但是会出现查询数据查不到的情况(因为有数据丢失)。
cluster-replica-no-failover no #Slow log 是 Redis 用来记录查询执行时间的日志系统，slow log保存在内存里面，读写速度非常快， 因此你可以放心地使用它，不必担心因为开启 slowlog 而损害 Redis 的速度。
slowlog-log-slower-than 10000 #以微秒为单位的慢日志记录，为负数会禁用慢日志，为0 会记录每个命令操作。
slowlog-max-len 128 #记录多少条慢日志保存在队列，超出后会删除最早的，以此滚动删除
```

```bash
127.0.0.1:6379> slowlog len
(integer) 14
127.0.0.1:6379> slowlog get
    1）1) (integer) 14
		2) (integer) 1544690617
        3） (integer) 4
        4）1) "slowlog" 127.0.0.1:6379> SLOWLOG reset OK
127.0.0.1:6379> SLOWLOG reset
OK
```

### 2.3.2：redis 持久化：

redis 虽然是一个内存级别的缓存程序，即 redis是使用内存进行数据的缓存的，但是其可以将内存的数据按照一定的策略保存到硬盘上，从而实现数据持久保存的目的，redis支持两种不同方式的数据持久化保存机制，分别是 RDB 和 AOF

#### 2.3.2.1：RDB模式：

RDB：基于时间的快照，只保留当前最新的一次快照，特点是执行速度比较快，缺点是可能会丢失从上次快照到当前快照未完成之间的数据。RDB 实现的具体过程 Redis 从主进程先 fork出一个子进程，使用写时复制机制，子进程将内存的数据保存为一个临时文件，比如dump.rdb.temp，当数据保存完成之后再将上一次保存的 RDB文件替换掉，然后关闭子进程，这样可以保存每一次做 RDB快照的时候保存的数据都是完整的，因为直接替换 RDB文件的时候可能会出现突然断电等问题而导致 RDB文件还没有保存完整就突然关机停止保存而导致数据丢失的情况，可以手动将每次生成的RDB 文件进程备份，这样可以最大化保存历史数据。

#### 2.3.2.2：RDB 模式的优缺点：

优点：

-RDB 快照保存了某个时间点的数据，可以通过脚本执行 bgsave(非阻塞)或者save(阻塞)命令自定义时间点北备份，可以保留多个备份，当出现问题可以恢复到不同时间点的版本。

\-可以最大化 o 的性能，因为父进程在保存 RDB 文件的时候唯一要做的是 fork出一个子进程，然后的操作都会有这个子进程操作，父进程无需任何的 IO 操作RDB 在大量数据比如几个 G 的数据，恢复的速度比 AOF 的快

缺点：

\-不能时时的保存数据，会丢失自上一次执行 RDB 备份到当前的内存数据

\-数据量非常大的时候，从父进程 fork 的时候需要一点时间，可能是毫秒或者秒

#### 2.3.2.3：AOF模式：

AOF:按照操作顺序依次将操作添加到指定的日志文件当中，特点是数据安全性相对较高，缺点是即使有些操作是重复的也会全部记录。

AOF 和 RDB 一样使用了写时复制机制，AOF 默认为每秒钟 fsync一次，即将执行的命令保存到 AOF 文件当中，这样即使 redis服务器发生故障的话顶多也就丢失 1 秒钟之内的数据，也可以设置不同的 fsync策略，或者设置每次执行命令的时候执行 fsync，fsync会在后台执行线程，所以主线程可以继续处理用户的正常请求而不受到写入 AOF 文件的 IO影响

#### 2.3.2.4：AOF模式优缺点：

AOF 的文件大小要大于 RDB 格式的文件根据所使用的 fsync 策略(fsync 是同步内存中 redis所有已经修改的文件到存储设备)，默认是appendfsync everysec 即每秒执行一次 fsync

## 2.4：redis数据类型：

### 2.4.1：字符串(string)：

字符串是所有编程语言中最常见的和最常用的数据类型，而且也是 redis最基本的数据类型之一，而且 redis 中所有的 key 的类型都是字符串。

#### 2.4.1.1：添加一个key：

```bash
127.0.0.1:6379> set key1 value1
OK
127.0.0.1:6379> get key1
"value1"
127.0.0.1:6379> TYPE key1
string
```

#### 2.4.1.2：获取一个key的内容：

```bash
127.0.0.1:6379> get key1
"value1"
```

#### 2.4.1.3：删除一个key：

```bash
127.0.0.1:6379> DEL key1
(integer) 1
```

#### 2.4.1.4：批量设置多个key：

```bash
127.0.0.1:6379> MSET key1 value1 key2 value2
OK
```

#### 2.4.1.5：批量获取多个key：

```bash
127.0.0.1:6379> MSET key1 value1 key2 value2
OK
```

#### 2.4.1.6：追加数据：

```bash
127.0.0.1:6379> APPEND key1 append
(integer) 12
127.0.0.1:6379> get key1
"value1append"
```

#### 2.4.1.7：数值递增：

```bash
127.0.0.1:6379> set num 10
OK
127.0.0.1:6379> INCR num
(integer) 11
127.0.0.1:6379\> get num
"11"
```

#### 2.4.1.8：数值递减：

```bash
127.0.0.1:6379> set num 10
OK
127.0.0.1:6379> DECR num
(integer) 9
127.0.0.1:6379 get num
"9"
```



#### 2.4.1.9：返回字符串key长度：

```bash
127.0.0.1:6379> STRLEN key1
(integer) 12
```



### 2.4.2：列表(list)：

列表是一个双向可读写的管道，其头部是左侧尾部是右侧，一个列表最多可以包含 2\^32-1个元素即4294967295 个元素。

#### 2.4.2.1：生成列表并插入数据：

```bash
127.0.0.1:6379> LPUSH list1 jack
(integer) 1
127.0.0.1:6379> TYPE list1
list
```

#### 2.4.2.2：向列表追加数据：

```bash
127.0.0.1:6379> LPUSH list1 tom
(integer) 2
127.0.0.1:6379> RPUSH list1 jack
(integer) 3
```

#### 2.4.2.3：获取列表长度：

```bash
127.0.0.1:6379> LLEN list1
(integer) 3
```

#### 2.4.2.4：移除列表数据：

```bash
127.0.0.1:6379> RPOP list1 #最后一个
"jack"
127.0.0.1:6379> LPOP list1 #第一个
"tom"
```

### 2.4.3：集合(set)：

Set 是 String类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据。

#### 2.4.3.1：生成集合key:

```bash
127.0.0.1:6379> SADD set1 v1
(integer) 1
127.0.0.1:6379> SADD set2 v2 v4
(integer) 2
127.0.0.1:6379> TYPE set1
set
127.0.0.1:6379> TYPE set2
set
```

#### 2.4.3.2：追加数值：

追加的时候不能追加已经存在的数值

```bash
127.0.0.1:6379> SADD set1 v2 v3 v4
(integer) 3
127.0.0.1:6379> SADD set1 v2 #没有追加成功(integer) 0
127.0.0.1:6379> TYPE set1
set
127.0.0.1:6379> TYPE set2
set
```

#### 2.4.3.3：查看集合的所有数据：

```bash
127.0.0.1:6379> SMEMBERS set1
1) "v4"
2) "v1"
3) "v3"
4) "v2"
127.0.0.1:6379> SMEMBERS set2
1) "v4"
2) "v2"
```

#### 2.4.3.4：获取集合的差集：

差集：已属于 A 而不属于 B 的元素称为 A 与 B 的差（集）

```bash
127.0.0.1:6379> SDIFF set1 set2
1) "v1"
2) "v3"
```

#### 2.4.3.5：获取集合的交集：

交集：已属于 A 且属于 B 的元素称为 A 与 B 的交（集）

```bash
127.0.0.1:6379> SINTER set1 set2
1) "v4"
2) "v2"
```

#### 2.4.3.6：获取集合的并集：

并集：已属于 A 或属于 B 的元素为称为 A 与 B 的并（集）

```bash
127.0.0.1:6379> SUNION set1 set2
1) "v2"
2) "v4"
3) "v1"
4) "v3"
```

### 2.4.4：sorted set(有序集合):

Redis 有序集合和集合一样也是 string类型元素的集合,且不允许重复的成员，不同的是每个元素都会关联一个double(双精度浮点型)类型的分数，redis正是通过分数来为集合中的成员进行从小到大的排序，序集合的成员是唯一的,但分数(score)却可以重复，集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)， 集合中最大的成员数为 232 - 1(4294967295, 每个集合可存储40 多亿个成员)。

#### 2.4.1：生成有序集合：

```bash
127.0.0.1:6379> ZADD zset1 1 v1
(integer) 1
127.0.0.1:6379> ZADD zset1 2 v2
(integer) 1
127.0.0.1:6379> ZADD zset1 2 v3
(integer) 1
127.0.0.1:6379> ZADD zset1 3 v4
(integer) 1
127.0.0.1:6379> TYPE zset1
zset
127.0.0.1:6379> TYPE zset2
zset
```

排行案例：

```bash
192.168.7.104:6379> ZADD paihangbang 10 key1 20 key2 30 key3
(integer) 3
192.168.7.104:6379> ZREVRANGE paihangbang 0 -1 withscores
1) "key3" 2) "30"
3) "key2" 4) "20"
5) "key1" 6) "10"
```

#### 2.4.2：批量添加多个数值：

```bash
127.0.0.1:6379> ZADD zset2 1 v1 2 v2 4 v3 5 v5
(integer) 4
```

#### 2.4.3：获取集合的长度数：

```bash
127.0.0.1:6379> ZCARD zset1
(integer) 4
127.0.0.1:6379> ZCARD zset2
(integer) 4
```

#### 2.4.4.4：基于索引返回数值：

```bash
127.0.0.1:6379> ZRANGE zset1 1 3
1) "v2"
2) "v3"
3) "v4"
127.0.0.1:6379> ZRANGE zset1 0 2
1) "v1"
2) "v2"
3) "v3"
```

#### 2.4.4.5：返回某个数值的索引：

```bash
127.0.0.1:6379> ZRANK zset1 v2
(integer) 1
127.0.0.1:6379> ZRANK zset1 v3
(integer) 2
```

### 2.4.5：哈希(hash)：

hash 是一个 string 类型的 field 和value 的映射表，hash特别适合用于存储对象，Redis 中每个 hash 可以存储 232 - 1 键值对（40 多亿）。

#### 2.4.5.1：生成hash key：

```bash
127.0.0.1:6379> HSET hset1 name tom age 18
(integer) 1
127.0.0.1:6379> TYPE hset1
hash
```

#### 2.4.5.2：获取hash key字段值：

```bash
127.0.0.1:6379> HGET hset1 name
"tom"
127.0.0.1:6379> HGET hset1 age "18"
```

#### 2.4.5.3：删除一个hash key的字段：

```bash
127.0.0.1:6379> HDEL hset1 age
(integer) 1
```

#### 2.4.5.4：获取所有hash表中的字段：

```bash
127.0.0.1:6379> HSET hset1 name tom age 19
(integer) 1
127.0.0.1:6379> HKEYS hset1
1)"name"
2)"age"
```

## 2.5：消息队列：

消息队列主要分为两种，分别是生产者消费者模式和发布者订阅者模式，这两种模式 Redis都支持

### 2.5.1：生产者消费者模式：

在生产者消费者(Producer/Consumer)模式下，上层应用接收到的外部请求后开始处理其当前步骤的操作，在执行完成后将已经完成的操作发送至指定的频道(channel)当中，并由其下层的应用监听该频道并继续下一步的操作，如果其处理完成后没有下一步的操作就直接返回数据给外部请求，如果还有下一步的操作就再将任务发布到另外一个频道，由另外一个消费者继续监听和处理。

#### 2.5.1.1：模式介绍：

生产者消费者模式下，多个消费者同时监听一个队里，但是一个消息只能被最先抢到消息的消费者消费，即消息任务是一次性读取和处理，此模式在分布式业务架构中非常常用，比较常用的软件还有RabbitMQ、Kafka、RocketMQ、ActiveMQ 等

![1553665687067](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553665687067.png)

#### 2.5.1.2：队列介绍：

队列当中的消息由不同的生产者写入也会有不同的消费者取出进行消费处理，但是买一个消息一定是只能被取出一次也就是被消费一次。

![1553665789889](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553665789889.png)

#### 2.5.1.3：生产者发布消息：

```bash
[root\@redis-s4 ~]# redis-cli
127.0.0.1:6379> AUTH 123456 OK
127.0.0.1:6379> LPUSH channel1 msg1 #从管道的左侧写入(integer) 1
127.0.0.1:6379> LPUSH channel1 msg2 (integer) 2
127.0.0.1:6379> LPUSH channel1 msg3 (integer) 3
127.0.0.1:6379> LPUSH channel1 msg4 (integer) 4
127.0.0.1:6379> LPUSH channel1 msg5 (integer) 5
```

#### 2.5.1.4：查看队列所有消息：

1. ```bash
    127.0.0.1:6379> LRANGE channel1 0 -1
    1)"msg5"
    2)"msg4"
    3)"msg3"
    4)"msg2"
    5)"msg1"
    ```

#### 2.5.1.5：消费者消费消息：

```bash
127.0.0.1:6379> RPOP channel1 #从管道的右侧消费
"msg1"
127.0.0.1:6379> RPOP channel1
"msg2"
127.0.0.1:6379> RPOP channel1
"msg3"
127.0.0.1:6379> RPOP channel1
"msg4"
127.0.0.1:6379> RPOP channel1
"msg5"
127.0.0.1:6379> RPOP channel1
(nil)
```

#### 2.5.1.6：再次验证队列消息：

```bash
127.0.0.1:6379> LRANGE channel1 0 -1
(empty list or set) #队列中的消息已经被已全部消费完毕
```



### 2.5.2：发布者订阅模式：

#### 2.5.2.1：模式简介：

在发布者订阅者模式下，发布者将消息发布到指定的 channel 里面，凡是监听该 channel的消费者都会收到同样的一份消息，这种模式类似于是收音机模式，即凡是收听某个频道的听众都会收到主持人发布的相同的消息内容。

此模式常用语群聊天、群通知、群公告等场景。

Subscriber：订阅者

Publisher：发布者

Channel：频道

![1553666140925](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553666140925.png)



#### 2.5.2.2：订阅者监听频道：

```bash
[root@redis-s4 ~]# redis-cli
127.0.0.1:6379> AUTH 123456 OK
127.0.0.1:6379> SUBSCRIBE channel1 #订阅者订阅指定的频道Reading messages...
(press Ctrl-C to quit)
1)"subscribe"
2)"channel1"
3)(integer) 1
```

#### 2.5.2.3：发布者发布消息：

127.0.0.1:6379\> PUBLISH channel1 test1 \#发布者发布消息

(integer) 2

127.0.0.1:6379\> PUBLISH channel1 test2 (integer) 2

127.0.0.1:6379\>

#### 2.5.2.4：各订阅者验证消息：

![1553668744070](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553668744070.png)

#### 2.5.2.5：订阅多个频道：

订阅指定的多个频道

```bash
> SUBSCRIBE channel1 channel2
```

#### 2.5.2.6：订阅所有频道：

```bash
127.0.0.1:6379> PSUBSCRIBE *
```



#### 2.7.2.7：订阅匹配的频道：

```bash
PSUBSCRIBE chann* #匹配订阅多个频道
```

## 2.6：redis 其他命令：

### 2.6.1：CONFIG：

config 命令用于查看当前 redis 配置、以及不重启更改 redis 配置等

#### 2.6.1.1：更改最大内存：

```bash
127.0.0.1:6379> CONFIG set maxmemory 8589934592
OK
127.0.0.1:6379> CONFIG get maxmemory
1) "maxmemory" 2) "8589934592"
```

#### 2.6.1.2：设置连接密码：

```bash
127.0.0.1:6379> CONFIG SET requirepass 123456
OK
```

![](media/ef7d058e59263bd721e035cae6253fea.png)



#### 2.6.1.3：或当前配置：

![](media/5947bb0dac4f6c4868b6b629ae285777.png)

### 2.6.2：info：

显示当前节点 redis 运行状态信息

![](media/6cebd4e7d7603e6c1d544783411b4763.png)

### 2.6.3：SELECT：

切换数据库

![](media/705dbb77f87fee1330e9b3f2fb20df76.png)

### 2.6.4：keys:

查看当前库下的所有 key：

![](media/9340578adeb229c891f513fed857008d.png)

### 2.6.5：BGSAVE：

手动在后台执行 RDB 持久化操作

![](media/8e3f0150159a2f059f85fff22bfa48c1.png)

### 2.6.6：DBSIZE：

返回当前库下的所有 key 数量

![](media/80f426e1880f9724def8d3a0e90ab0d6.png)

### 2.6.7：FLUSHDB：

强制清空当前库中的所有 key

### 2.6.8：FLUSHALL：

强制清空当前 redis 服务器所有数据库中的所有 key，即删除所有数据

三：redis 高可用与集群：
========================

虽然 Redis 可以实现单机的数据持久化，但无论是 RDB 也好或者 AOF也好，都解决不了单点宕机问题，即一旦 redis服务器本身出现系统故障、硬件故障等问题后，就会直接造成数据的丢失，因此需要使用另外的技术来解决单点问题。

## 3.1：配置reids主从：

主备模式，可以实现 Redis 数据的跨主机备份。程序端连接到高可用负载的 VIP，然后连接到负载服务器设置的 Redis 后端 realserver，此模式不需要在程序里面配置 Redis 服务器的真实 IP 地址，当后期 Redis服务器 IP 地址发生变更只需要更改 redis 相应的后端 real server即可，可避免更改程序中的 IP 地址设置。

### 3.1.1：Slave 主要配置：

Redis Slave 也要开启持久化并设置和 master 同样的连接密码，因为后期 slave会有提升为 master 的可能,Slave 端切换 master 同步后会丢失之前的所有数据。一旦某个 Slave 成为一个 master 的 slave，Redis Slave 服务会清空当前 redis服务器上的所有数据并将master 的数据导入到自己的内存，但是断开同步关系后不会删除当前已经同步过的数据。

#### 3.1.1.1：命令行配置：

当前状态为 master，需要转换为 slave 角色并指向 master 服务器的 IP+PORT+Password

```bash
192.168.7.104:6379> REPLICAOF 192.168.7.103 6379 OK
192.168.7.104:6379> CONFIG SET masterauth 123456 OK
```

#### 3.1.1.2：同步日志：

![](media/1f71663ceb16b09a909a10f59a3adde5.jpg)

#### 3.1.1.3：当前slave状态：

![](media/02ff77f5b3ee21c754c334afea938239.jpg)



#### 3.1.1.4：保存配置到redis.conf：

```bash
286 replicaof 192.168.7.103 6379
293 masterauth 123456 #master 如果密码需要设置
```

#### 3.1.1.5：重启slave验证：

![](media/db17aedde486d9ba1aafa593d13c1a9e.png)



#### 3.1.1.6：验证slave数据：

```
127.0.0.1:6379> KEYS *
1)"num"
2)"hset1"
3)"key1"
4)"name1"
5)"zset2"
6)"key2"
7)"zset1"
8)"set2"
```

#### 3.1.1.7：slave状态只读无法写入数据：

![](media/4e5ee30f5fa5433eb065e336af0d1f5d.png)

#### 3.1.1.8：Master日志：

![](media/30db9239fa5205d9a270683ab2b9fd01.jpg)

#### 3.1.1.9：主从复制过程：

Redis支持主从复制分为全量同步和增量同步，首次同步是全量同步，主从同步可以让从服务器从主服务器备份数据，而且从服务器还可与有从服务器，即另外一台 redis
服务器可以从一台从服务器进行数据同步，redis的主从同步是非阻塞的，其收到从服务器的 sync(2.8 版本之前是 PSYNC)命令会fork一个子进程在后台执行 bgsave 命令，并将新写入的数据写入到一个缓冲区里面，bgsave执行完成之后并生成的将 RDB 文件发送给客户端，客户端将收到后的 RDB文件载入自己的内存，然后主 redis 将缓冲区的内容在全部发送给从redis，之后的同步从服务器会发送一个 offset 的位置(等同于 MySQL 的 binlog的位置)给主服务器，主服务器检查后位置没有错误将此位置之后的数据包括写在缓冲区的积压数据发送给redis从服务器，从服务器将主服务器发送的挤压数据写入内存，这样一次完整的数据同步，再之后再同步的时候从服务器只要发送当前的offset 位置给主服务器，然后主服务器根据响应的位置将之后的数据发送给从服务器保存到其内存即可。Redis 全量复制一般发生在 Slave 初始化阶段，这时 Slave 需要将 Master上的所有数据都复制一份。具体步骤如下：

1）从服务器连接主服务器，发送 SYNC 命令；

2）主服务器接收到 SYNC 命名后，开始执行 BGSAVE 命令生成 RDB快照文件并使用缓冲区记录此后执行的所有写命令；

3）主服务器 BGSAVE执行完后，向所有从服务器发送快照文件，并在发送期间继续记录被执行的写命令；

4）从服务器收到快照文件后丢弃所有旧数据，载入收到的快照；

5）主服务器快照发送完毕后开始向从服务器发送缓冲区中的写命令；

6）从服务器完成对快照的载入，开始接收命令请求，并执行来自主服务器缓冲区的写命令；
7）后期同步会先发送自己 slave_repl_offset位置，只同步新增加的数据，不再全量同步。

![1553670064900](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553670064900.png)

#### 3.1.10：主从同步优化：

Redis 在 2.8 版本之前没有提供增量部分复制的功能，当网络闪断或者 slave Redis重启之后会导致主从之间的全量同步，即从 2.8 版本开始增加了部分复制的功能。

```bash
repl-diskless-sync no #yes 为支持 disk，master 将 RDB文件先保存到磁盘在发送给 slave，no 为 maste直接将 RDB 文件发送给 slave，默认即为使用 no，Master RDB 文件不需要与磁盘交互。
repl-diskless-sync-delay 5 #Master 准备好 RDB 文件后等等待传输时间
repl-ping-slave-period 10 #slave 端向 server 端发送 pings的时间区间设置，默认为 10 秒
repl-timeout 60 #设置超时时间
repl-disable-tcp-nodelay no #是否启用 TCP_NODELAY，如设置成 yes，则 redis会合并小的 TCP 包从而节省带宽，但会增加同步延迟（40ms），造成 master 与 slave数据不一致，假如设置成 no，则 redismaster会立即发送同步数据，没有延迟，前者关注性能，后者关注一致性
repl-backlog-size 1mb #master的写入数据缓冲区，用于记录自上一次同步后到下一次同步过程中间的写入命令，计算公式：brepl-backlog-size = 允许从节点最大中断时长 * 主实例 offset 每秒写入量，比如master 每秒最大写入 64mb，最大允许 60 秒，那么就要设置为 64mb*60秒=3840mb(3.8G)= repl-backlog-ttl 3600 #如果一段时间后没有 slave 连接到master，则 backlog size 的内存将会被释放。如果值为 0则表示永远不释放这部份内存。
slave-priority 100 #slave端的优先级设置，值是一个整数，数字越小表示优先级越高。当 master故障时将会按照优先级来选择 slave 端进行恢复，如果值设置为 0，则表示该 slave永远不会被选择。
#min-slaves-to-write 0 #
#min-slaves-max-lag 10 #设置当一个 master 端的可用 slave 少于 N个，延迟时间大于 M 秒时，不接收写操作。Master 的重启会导致 master_replid 发生变化，slave 之前的 master_replid 就和master 不一致从而会引发所有 slave 的全量同步。
```

#### 3.1.11：Slave同步过程日志：

![](media/243a36cde856ebe253271d3ae680d5ce.jpg)

#### 3.1.12：master同步日志：

![](media/c91cc0564b52dd86f62b9bfb436ff8ef.jpg)



#### 3.1.13：slave切换master：

当前状态：

```bash
192.168.7.101:6379> info Replication
# Replication
role:slave
master_host:192.168.7.103
master_port:6379
master_link_status:up
master_last_io_seconds_ago:8
master_sync_in_progress:0
```

停止 slave 同步并查看当前状态： 

```bash
192.168.7.101:6379> SLAVEOF no one OK
192.168.7.101:6379> info Replication # Replication
role:master connected_slaves:0
master_replid:ac3475e5e4fae8c5f47711a643e465b9520c4182
master_replid2:8ee6bc1ac452fd4d2ccbaa660a219f78d218399a
master_repl_offset:8840
second_repl_offset:8841 repl_backlog_active:1 repl_backlog_size:1048576
repl_backlog_first_byte_offset:8547 repl_backlog_histlen:294
```

测 试 能 否 写 入 数 据 ： 

```
192.168.7.101:6379> set key1 value1 OK
192.168.7.101:6379>
```

#### 3.1.1.14：Slave节点再有Slave:

![1553670755039](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553670755039.png)

在有 slave 的”master”查看状态：

```bash
# Replication role:slave
master_host:192.168.7.102
master_port:6379
master_link_status:up
master_last_io_seconds_ago:9 #最近一次与master通信已经过去多少秒。master_sync_in_progress:0 \#是否正在与master通信。
slave_repl_offset:5334 #当前同步的偏移量。
slave_priority:100 #slave 优先级，master 故障后值越小越优先同步。
slave_read_only:1
connected_slaves:1
slave0:ip=192.168.7.104,port=6379,state=online,offset=5334,lag=1
master_replid:0f0318c25a022add7fd51d4438c470cf608631f9
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:5334
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:5334
```

### 3.1.2：常见问题汇总：

#### 3.1.2.1：master密码不对：

即配置的 master 密码不对，导致验证不通过而无法建立主从同步关系。

![](media/cb648347beffc2be7dde2c3cfbefbc38.png)

#### 3.1.2.2：Redis版本不一致：

不同的 redis 版本之间存在兼容性问题，因此各 master 和 slave之间必须保持版本一致。

![](media/86969a17e6a6bae1736311a3c890edc7.png)

#### 3.1.2.3：无法远程连接：

在开启了安全模式情况下，没有设置 bind 地址和密码

```bash
[root@redis-s1 ~]# redis-cli -h 192.168.7.104
192.168.7.104:6379> KEYS *
(error) DENIED Redis is running in protected mode because protected mode isenabled, no bind address was specified, no authentication passwordis requested to clients. In this mode connections are only accepted from the loopback interface. If you want to connect from external computers to Redis you may adopt one of the following solutions:
	1)Just disable protected mode sending the command 'CONFIG SET protected-mode no' from the loopback interface by connecting to Redis from the same host the server is running, however MAKE SURE Redis is not publicly accessible from internet if you do so. Use CONFIG REWRITE to make this change permanent.
	2)Alternatively you can just disable the protected mode by editing the Redis configuration file, and setting the protected mode option to 'no', and then restarting the server.
	3)If you started the server manually just for testing, restart it with the '--protected-mode no' option.
	4)Setup a bind address or an authentication password. NOTE: You only need to do one of the above things in order for the server to start accepting connections from the outside.
```

## 3.2redis集群：

上一个步骤的主从架构无法实现master 和 slave 角色的自动切换，即当 master出现redis 服务异常、主机断电、磁盘损坏等问题导致 master 无法使用，而 redis高可用无法实现自故障转移(将 slave 提升为 master)，需要手动改环境配置才能切换到slave redis 服务器，另外也无法横向扩展 Redis 服务的并行写入性能，当单台 Redis服务器性能无法满足业务写入需求的时候就必须需要一种方式解决以上的两个核心问题，即：1.master和 slave 角色的无缝切换，让业务无感知从而不影响业务使用 2.可以横向动态扩展 Redis服务器，从而实现多台服务器并行写入以实现更高并发的目的。

Redis 集群实现方式：客户端分片 代理分片 Redis Cluster

### 3.2.1：Sentinel(哨兵)：

Sentinel 进程是用于监控 redis 集群中Master 主服务器工作的状态，在 Master主服务器发生故障的时候，可以实现 Master 和 Slave服务器的切换，保证系统的高可用，其已经被集成在 redis2.6+的版本中，Redis的哨兵模式到了 2.8 版本之后就稳定了下来。一般在生产环境也建议使用 Redis 的 2.8版本的以后版本。哨兵(Sentinel)是一个分布式系统，你可以在一个架构中运行多个哨兵(sentinel) 进程，这些进程使用流言协议(gossipprotocols)来接收关于 Master主服务器是否下线的信息，并使用投票协议(AgreementProtocols)来决定是否执行自动故障迁移,以及选择哪个 Slave 作为新的Master。每个哨兵(Sentinel)进程会向其它哨兵(Sentinel)、Master、Slave定时发送消息，以确认对方是否”活”着，如果发现对方在指定配置时间(可配置的)内未得到回应，则暂时认为对方已掉线，也就是所谓的”主观认为宕机”，英文名称：Subjective Down，简称SDOWN。有主观宕机，肯定就有客观宕机。当“哨兵群”中的多数 Sentinel 进程在对 Master主服务器做出 SDOWN 的判断，并且通过 SENTINEL is-master- down-by-addr命令互相交流之后，得出的 Master Server下线判断，这种方式就是“客观宕机”，英文名称是：Objectively Down， 简称ODOWN。通过一定的 vote 算法，从剩下的 slave 从服务器节点中， 选一台提升为 Master服务器节点，然后自动修改相关配置，并开启故障转移（failover）。

Sentinel 机制可以解决 master 和 slave 角色的切换问题。

#### 3.2.1.1：手动配置master：

需要手动先指定某一台 Redis 服务器为 master，然后将其他 slave服务器使用命令配置为 master 服务器的 slave

##### 3.2.1.1.1：服务器 A 配置slave：

```bash
192.168.7.102:6379> REPLICAOF 192.168.7.101 6379
OK
192.168.7.102:6379> CONFIG SET masterauth "123456" OK
```

![](media/0108503e3fc373abe81034a37386f556.png)

##### 3.2.1.1.2：服务器 B 配置slave：

```bash
192.168.7.103:6379> REPLICAOF 192.168.7.101 6379
OK
192.168.7.103:6379> CONFIG SET masterauth "123456" OK
```

![](media/3338cdde4cc7830da3c21ef0883b7a00.png)

##### 3.2.1.1.3：当前 master 状态：

![1553683333462](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553683333462.png)

##### 3.2.1.1.4：应用程序如何连接 redis：

java 客户端连接 redis 是通过 jedis 来实现的，java 代码用的时候只要创建 jedis对象就可以建多个 jedis 连接池来连接 redis，应用程序再直接调用连接池即可连接 Redis。而 Redis 为了保障高可用,服务一般都是 Sentinel 部署方式，当 Redis服务中的主服务挂掉之后, 会仲裁出另外一台 Slaves 服务充当Master。这个时候,我们的应用即使使用了 Jedis 连接池,Master服务挂了,我们的应用奖还是无法连接新的 Master 服务，为了解决这个问题,Jedis也提供了相应的Sentinel 实现,能够在 Redis Sentinel主从切换时候,通知我们的应用,把我们的应用连接到新的Master 服务。

Jedis Sentinel 的使用也是十分简单的,只是在 JedisPool 中添加了 Sentinel 和MasterName 参数，Jedis Sentinel 底层基于 Redis 订阅实现 Redis主从服务的切换通知，当 Reids 发生主从切换时，Sentinel 会发送通知主动通知 Jedis进行连接的切换，JedisSentinelPool在每次从连接池中获取链接对象的时候,都要对连接对象进行检测,如果此链接和 Sentinel的 Master 服务连接参数不一致,则会关闭此连接,重新获取新的 Jedis 连接对象。

##### 3.2.1.1.5：python 连接redis：

```python
# yum install python-pip
# pip install redis
[root @redis-s3 ~] # cat test.py 
#!/bin/env python
#Author: ZhangJie import redis
pool = redis.ConnectionPool(host="192.168.7.101",port=6379,password="123456")
r = redis.Redis(connection_pool=pool)
for i in range(100):
    r.set("k%d" % i,"v%d" % i)
    data=r.get("k%d" % i)
    print(data)
```

##### 3.2.1.1.5 ：各 Redis 服务器验证数据：



![](media/65f054a691c1e8c3990a6626aff90a11.png)

#### 3.2.1.2：编辑配置文件sentinel.conf：

##### 3.2.1.2.1：Server1 配置：

哨兵可以不和 Redis 服务器部署在一起

```bash
[root@redis-s1 etc]# grep "^[a-Z]" /usr/local/redis/etc/sentinel.conf
bind 0.0.0.0
port 26379 
daemonize yes
pidfile "/usr/local/redis/redis-sentinel.pid" 
logfile "/usr/local/redis/sentinel_26379.log" 
dir "/usr/local/redis"
sentinel monitor  mymaster 192.168.7.101 6379 2 
sentinel auth-pass mymaster 123456
sentinel down-after-milliseconds mymaster 30000 #(SDOWN)主观下线的时间
sentinel parallel-syncs mymaster 1 #发生故障转移时候同时向新 master 同步数据的slave 数量，数字越小总同步时间越长
sentinel failover-timeout mymaster 180000 #所有 slaves 指向新的 master所需的超时时间
sentinel deny-scripts-reconfig yes
```

##### 3.2.1.2.2：Server2 配置：

```bash
bind 192.168.7.102
port 26379 daemonize yes
pidfile "/usr/local/redis/redis-sentinel.pid" 
logfile "/usr/local/redis/sentinel_26379.log" 
dir "/usr/local/redis"
sentinel deny-scripts-reconfig yes
sentinel monitor mymaster 192.168.7.101 6379 2 
sentinel auth-pass mymaster 123456
```

##### 3.2.1.2.3：Server3 配置：

```bash
bind 192.168.7.103
port 26379  
daemonize yes
pidfile  "/usr/local/redis/redis-sentinel.pid" 
logfile "/usr/local/redis/sentinel_26379.log" 
dir "/usr/local/redis"
sentinel deny-scripts-reconfig yes
sentinel monitor mymaster 192.168.7.101 6379 2
sentinel auth-pass mymaster 123456
```

#### 3.2.1.3：启动哨兵：

三台哨兵都要启动

```bash
#/usr/local/redis/bin/redis-sentinel /usr/local/redis/etc/sentinel.conf
#/usr/local/redis/bin/redis-sentinel /usr/local/redis/etc/sentinel.conf
#/usr/local/redis/bin/redis-sentinel /usr/local/redis/etc/sentinel.conf
```



#### 3.2.1.4：验证端口：

![1553684385716](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553684385716.png)

#### 3.2.1.5：哨兵日志：

![1553684440076](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553684440076.png)

#### 3.2.1.6当 前redis状 态 ： 

```bash
192.168.7.101:6379> info Replication 
# Replication
role:master
connected_slaves:2
slave0:ip=192.168.7.103,port=6379,state=online,offset=2316,lag=1
slave1:ip=192.168.7.102,port=6379,state=online,offset=2175,lag=1
master_replid:f62f849f8ff4b9ee6d0095b802e9189a3e84aaf3
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:2316
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:2316
```



#### 3.2.1.7：当前sentinel状态：

尤其是最后一行，涉及到 master IP 是多少，有几个 slave，有几个sentinels，必须是符合全部服务器数量的。

```bash
[root@redis-s1 etc]# redis-cli -h 192.168.7.101 -p 26379
192.168.7.101:26379> info Sentinel
# Sentinel 
sentinel_masters:1
sentinel_tilt:0
sentinel_running_scripts:0
sentinel_scripts_queue_length:0
sentinel_simulate_failure_flags:0
master0:name=mymaster,status=ok,address=192.168.7.101:6379,slaves=2,sentinels=3
```



#### 3.2.1.8：停止Redis Master测试故障转移：

```bash
[root@redis-s1 ~]# systemctl stop redis
```

查看集群信息：

```bash
[root@redis-s1 ~]# redis-cli -h 192.168.7.101 -p 6379 
# Replication
role:master
connected_slaves:2
slave0:ip=192.168.7.103,port=6379,state=online,offset=51206,lag=0
slave1:ip=192.168.7.102,port=6379,state=online,offset=51065,lag=1
master_replid:416707fb0a8197595e16d6af8a8241c41cd8e992
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:51347
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:51347
```

查看哨兵信息：

```bash
[root@redis-s1 ~]# redis-cli -h 192.168.7.101 -p 26379 
# Sentinel
sentinel_masters:1
sentinel_tilt:0
sentinel_running_scripts:0
sentinel_scripts_queue_length:0
sentinel_simulate_failure_flags:0
master0:name=mymaster,status=ok,address=192.168.7.101:6379,slaves=2,sentinels=3
```

故障转移时 sentinel 的信息：

![](media/6bb24c4bd56834b66895a08857d79afe.jpg)

#### 3.2.1.9：故障转移后的redis配置文件:

故障转移后 redis.conf 中的 replicaof 行的 master IP 会被修改，sentinel.conf 中的sentinel monitor IP 会被修改。

![](media/5537f97cab94a81f6d5084ac46ce93a4.png)

#### 3.2.1.10：当前reids状态：

![](media/fae3752b6ee1bb02b831e775291fd4b3.png)



### 2.2.2：Redis Cluster 部署：

Redis cluster 之前的分布式方案：

​	1）客户端分区：由客户端程序决定 key 写分配和写入的 redisnode，但是需要客户端自己处理写入分配、高可用管理和故障转移等

​	2）代理方案：基于三方软件实现 redis proxy，客户端先连接之代理层，由代理层实现key的写入分配，对客户端来说是有比较简单，但是对于集群管节点增减相对比较麻烦，而且代理本身也是单点和性能瓶颈。

在哨兵 sentinel 机制中，可以解决 redis 高可用的问题，即当 master故障后可以自动将 slave 提升为master 从而可以保证redis服务的正常使用，但是无法解决 redis 单机写入的瓶颈问题，即单机的
redis写入性能受限于单机的内存大小、并发数量、网卡速率等因素，因此 redis 官方在redis 3.0 版本之后推出了无中心架构的 redis cluster 机制，在无中心的 redis集群汇中，其每个节点保存当前节点数据和整个集群状态,每个节点都和其他所有节点连接，特点如下：

1：所有 Redis 节点使用(PING-PING 机制)互联

2：集群中某个节点的实效是整个集群中超过半数的节点监测都实效才算真正的实效

3：客户端不需要 proxy 即可直接连接redis，且客户端不需要连接集群中的所有节点，只要连接集群中的任何一个节点即可。

4：redis cluster 把所有的 redisnode 映射到 0-16383个槽位(slot)上，读写需要到指定的 redis node 上进行操作，因此有多少个 reids node相当于 redis 并发扩展了多少倍。

5：Redis 集群预先分配 16384 个(slot)槽位，当需要在 redis 集群中写入一个 key-value 的时候，会使用CRC16(key) mod 16384 之后的值，决定将 key写入值哪一个槽位从而决定写入哪一个 Redis 节点上从而有效解决单机瓶颈。

#### 3.2.2.1：Redis cluster架构：

##### 3.2.2.1.1：Redis cluster 基本架构：

加入三个主节点分别是：A, B, C 三个节点，采用哈希槽 (hash slot)的方式来分配16384 个 slot 的话，它们三个节点分别承担的 slot 区间是： 

节点 A 覆盖 0－5460

节点 B 覆盖 5461－10922

节点 C 覆盖 10923－16383

![1553685922003](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553685922003.png)

##### 3.2.2.1.2：Redis cluster 主从架构：

Redis cluster 的架构虽然解决了并发的问题，但是又引入了一个新的问题，每个 Redismaster 的高可用如何解决？

![1553685982089](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553685982089.png)

#### 3.2.2.2：部署redis集群：

环境准备：

三台服务器，每台服务器启动 6379 和 6380 两个 redis 服务，生产环境建议直接 6台服务器。另外预留一台服务器做集群添加节点测试。

192.168.7.101:6379/6380 192.168.7.102:6379/6380

192.168.7.103:6379/6380 192.168.7.104:6379/6380

##### 3.2.2.2.1：创建 redis cluster 集群的前提：

1、每个 redis node 节点采用相同的硬件配置、相同的密码

每个节点必须开启参数

cluster-enabled yes \#必须开启集群状态，开启后 redis 进程会有 cluster 显示

cluster-config-file nodes-6380.conf \#此文件有 redis cluster集群自动创建和维护，不需要任何手动操作

![](media/0a04fee0c6833ee3e3448392a9d19b5c.jpg)

##### 3.2.2.2.2：创建集群：

Redis 3 和 4 版本：
需要使用到集群管理工具 redis-trib.rb， 这个工具是 redis 官方推出的管理 redis 集群的工具，集成在
redis 的源码 src 目录下，是基于 redis 提供的集群命令封装成简单、便捷、实用的操作工具， redis-trib.rb
是 redis 作者用 ruby 完成的， centos 系统 yum 安装的 ruby 存在版本较低问题，如下：

```
[root@s1 ~]# yum install ruby rubygems -y
[root@s1 ~]# find / -name redis-trib.rb
/usr/local/src/redis-4.0.14/src/redis-trib.rb
[root@s1 ~]# cp /usr/local/src/redis-4.0.14/src/redis-trib.rb /usr/bin/
[root@s1 src]# gem install redis
```

Fetching: redis-4.1.0.gem (100%)
ERROR: Error installing redis:
redis requires Ruby version >= 2.2.2.
#解决 ruby 版本较低问题：

```
[root@s1 src]# yum remove ruby rubygems -y
[root@s1 src]# wget https://cache.ruby-lang.org/pub/ruby/2.2/ruby-2.2.6.tar.gz
[root@s1 src]# tar xf ruby-2.2.6.tar.gz
[root@s1 src]# cd ruby-2.2.6
[root@s1 ruby-2.2.6]# ./configure
[root@s1 ruby-2.2.6]# make –j 4
[root@s1 ruby-2.2.6]# make install
[root@s5 ruby-2.2.6]# ln -sv /usr/local/src/ruby-2.2.6/bin/gem /usr/bin/
[root@s5 ruby-2.2.6]# ln -sv /usr/local/src/ruby-2.2.6/ruby /usr/bin/
[root@s5 ruby-2.2.6]# yum install rubygems
[root@s1 ruby-2.2.6]# gem install redis
```

![1554009738673](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1554009738673.png)

验证 redis-trib.rb 命令是否可执行:
[root@s1 ruby-2.5.4]# redis-trib.rb
Usage: redis-trib <command> <options> <arguments ...>
create host1:port1 ... hostN:portN #创建集群
--replicas <arg> #指定副本数量
check host:port #检查集群信息
info host:port #查看集群主机信息
fix host:port #修复集群
--timeout <arg>
reshard host:port #在线热迁移集群指定主机的 slots 数据
--from <arg>
--to <arg>
--slots <arg>
--yes
--timeout <arg>
--pipeline <arg>
rebalance host:port #平衡集群中各主机的 slot 数量
--weight <arg>
--auto-weights
--use-empty-masters
--timeout <arg>
--simulate
--pipeline <arg>
--threshold <arg>
add-node new_host:new_port existing_host:existing_port #添加主机
--slave
--master-id <arg>
del-node host:port node_id #删除主机
set-timeout host:port milliseconds #设置节点的超时时间
call host:port command arg arg .. arg #在集群上的所有节点上执行命令
import host:port #导入外部 redis 服务器的数据到当前集群
--from <arg>
--copy
--replace
help (show this help)

```
[root@s1 ruby-2.5.4]# vim /usr/local/lib/ruby/gems/2.5.0/gems/redis-4.1.0/lib/redis/client.rb #修改密码为redis 登录密码
```

创建 redis cluster 集群： 

```bash
[root@redis-s1 ~]# redis-cli -a 123456 --cluster create 192.168.7.101:6379 192.168.7.101:6380 192.168.7.102:6379 192.168.7.102:6380 192.168.7.103:6379 192.168.7.103:6380 --cluster-replicas 1
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 192.168.7.102:6380 to 192.168.7.101:6379
Adding replica 192.168.7.101:6380 to 192.168.7.102:6379
Adding replica 192.168.7.103:6380 to 192.168.7.103:6379
>>> Trying to optimize slaves allocation for anti-affinity 
[OK] Perfect anti-affinity obtained!
M: f4cfc5cf821c0d855016488d6fbfb62c03a14fda 192.168.7.101:6379 #带 M 的为master 
slots:[0-5460] (5461 slots) master #当前master 的槽位起始和结束位
S: 2b6e5d9c3944d79a5b64a19e54e52f83d48438d6 192.168.7.101:6380 #带S 的slave
replicates 70de3821dde4701c647bd6c23b9dd3c5c9f24a62
M: 116c4c6de036fdbac5aaad25eb1a61ea262b64af 192.168.7.102:6379
slots:[5461-10922] (5462 slots) master #当前master 的槽位起始和结束位
S: 7186c6d03dd9a5e3be658f2d08e800bc55b04a09 192.168.7.102:6380
replicates f4cfc5cf821c0d855016488d6fbfb62c03a14fda
M: 70de3821dde4701c647bd6c23b9dd3c5c9f24a62 192.168.7.103:6379
slots:[10923-16383] (5461 slots) master #当前 master 的槽位起始和结束位
S: 7eda0bcf0c01bb7343313b81267c42dd1b26c8a6 192.168.7.103:6380
replicates 116c4c6de036fdbac5aaad25eb1a61ea262b64af
Can I set the above configuration? (type 'yes' to accept): yes #输入yes自动创建集群
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster 
Waiting for the cluster to join
.....
>>> Performing Cluster Check (using node 192.168.7.101:6379)
M: f4cfc5cf821c0d855016488d6fbfb62c03a14fda 192.168.7.101:6379 #master 的ID 及端口
slots:[0-5460] (5461 slots) master #已经分配的槽位1 
additional replica(s) #分配了一个 slave
S: 7186c6d03dd9a5e3be658f2d08e800bc55b04a09 192.168.7.102:6380
slots: (0 slots) slave #slave 没有分配槽位
replicates f4cfc5cf821c0d855016488d6fbfb62c03a14fda
M: 70de3821dde4701c647bd6c23b9dd3c5c9f24a62 192.168.7.103:6379
slots:[10923-16383] (5461 slots) master 1 additional replica(s)
M: 116c4c6de036fdbac5aaad25eb1a61ea262b64af 192.168.7.102:6379
slots:[5461-10922] (5462 slots) master
1 additional replica(s)
S: 2b6e5d9c3944d79a5b64a19e54e52f83d48438d6 192.168.7.101:6380
slots: (0 slots) slave
replicates 70de3821dde4701c647bd6c23b9dd3c5c9f24a62
S: 7eda0bcf0c01bb7343313b81267c42dd1b26c8a6 192.168.7.103:6380
slots: (0 slots) slave
replicates 116c4c6de036fdbac5aaad25eb1a61ea262b64af
[OK] All nodes agree about slots configuration. #所有节点槽位分配完成
>>> Check for open slots... #检查打开的槽位
>>> Check slots coverage... #检查插槽覆盖范围
[OK] All 16384 slots covered. #所有槽位(16384 个)分配完成
```



##### 3.2.2.2.3：检查状态：

由于未设置 masterauth认证密码，所以主从未建立起来，但是集群已经运行，所以需要在每个 slave控制台使用 config set 设置 masterauth 密码，或者写在每个 redis配置文件中，最好是在控制点设置密码之后再写入配置文件当中。

![](media/e738da4a32a095925662443fa8f4d281.png)

##### 3.2.2.2.4：分别设置 masterauth 密码：

```bash
[root@redis-s1 ~]# redis-cli -h 192.168.7.101 -p 6380 -a 123456
Warning: Using a password with '-a' or '-u' option on the command line interface
may not be safe. 192.168.7.101:6380> CONFIG SET masterauth 123456
OK
[root@redis-s1 ~]# redis-cli -h 192.168.7.102 -p 6380 -a 123456
Warning: Using a password with '-a' or '-u' option on the command line interface
may not be safe. 192.168.7.102:6380> CONFIG SET masterauth 123456
OK
[root@redis-s1 ~]# redis-cli -h 192.168.7.103 -p 6380 -a 123456
Warning: Using a password with '-a' or '-u' option on the command line interface
may not be safe. 192.168.7.103:6380> CONFIG SET masterauth 123456
OK
```



##### 3.2.2.2.5：确认slave 状态为 up：

![1553686823385](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553686823385.png)

##### 3.2.2.2.6：验证master 状态：

```bash
[root@redis-s1 ~]# redis-cli -h 192.168.7.101 -p 6379 -a 123456
Warning: Using a password with '-a' or '-u' option on the command line
interface may not be safe.
192.168.7.101:6379> INFO Replication
# Replication
role:master
connected_slaves:1
slave0:ip=192.168.7.102,port=6380,state=online,offset=840,lag=0
master_replid:0aa3281030eb29bf268f3317d4afe401f661a917
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:840
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:4026531840
repl_backlog_first_byte_offset:1
repl_backlog_histlen:840
192.168.7.101:6379>
```



##### 3.2.2.2.7：验证集群状态：

```bash
192.168.7.101:6379> CLUSTER INFO
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:1
cluster_stats_messages_ping_sent:1474
cluster_stats_messages_pong_sent:1507
cluster_stats_messages_sent:2981
cluster_stats_messages_ping_received:1502
cluster_stats_messages_pong_received:1474
cluster_stats_messages_meet_received:5
cluster_stats_messages_received:2981
```



##### 3.2.2.2.8：查看集群 node 对应关系：

使 用 命 令 cluster nodes：

```bash
192.168.7.103:6380> cluster nodes
7186c6d03dd9a5e3be658f2d08e800bc55b04a09 192.168.7.102:6380 @16380
slave f4cfc5cf821c0d855016488d6fbfb62c03a14fda 0 1545659135000 4
connected 7eda0bcf0c01bb7343313b81267c42dd1b26c8a6 192.168.7.103:6380 @16380 
myself,slave 116c4c6de036fdbac5aaad25eb1a61ea262b64af 0 1545659135000 6 
conne ctedf4cfc5cf821c0d855016488d6fbfb62c03a14fda 192.168.7.101:6379 @16379 
master - 0 1545659135000 1 
connected 0-5460 116c4c6de036fdbac5aaad25eb1a61ea262b64af 192.168.7.102:6379 @16379 
master - 0 1545659136000 3 connected 5461-10922 70de3821dde4701c647bd6c23b9dd3c5c9f24a62 192.168.7.103:6379 @16379 
master - 0 1545659134000 5 connected 10923-16383 2b6e5d9c3944d79a5b64a19e54e52f83d48438d6 192.168.7.101:6380 @16380 
slave 70de3821dde4701c647bd6c23b9dd3c5c9f24a62 0 1545659135946 5 connected
```



##### 3.2.2.2.9：验证集群写入 key：

```bash
192.168.7.101:6379> SET key1 value1 #经过算法计算，当前 key的槽位需要写入指定的 node
(error) MOVED 9189 192.168.7.102:6379 #槽位不在当前 node 所以无法写入
192.168.7.103:6379> SET key1 value1
(error) MOVED 9189 192.168.7.102:6379
192.168.7.102:6379> SET key1 value1 #指定的node 就可以写入
OK
192.168.7.102:6379> KEYS *
1) "key1"
192.168.7.101:6379> KEYS *
(empty list or set)
192.168.7.103:6379> KEYS *
(empty list or set)
```



##### 3.2.2.2.10：集群状态监控：

```bash
# redis-cli -a 123456 --cluster check 192.168.7.101:6379
```

![](media/899b05bfd71dacca69e876ee06b47aca.png)

### 3.2.3：Redis cluster 集群节点维护：

集群运行时间长久之后，难免由于硬件故障、网络规划、业务增长等原因对已有集群进行相应的调整，比如增加 Redis node 节点、减少节点、节点迁移、更换服务器等。增加节点和删除节点会涉及到已有的槽位重新分配及数据迁移。

#### 3.2.3.1：集群维护之动态添加节点：

增加 Redis node 节点，需要与之前的 Redis node版本相同、配置一致，然后分别启动两台 Redis node，因为一主一从。案例：

因公司业务发展迅猛，现有的三主三从 redis cluster架构可能无法满足现有业务的并发写入需求，因此公司紧急采购一台服务器192.168.7.104，需要将其动态添加到集群当中其不能影响业务使用和数据丢失，则添加过程如下:同步之前 Redis node 的配置文件到 192.168.7.104 Redis编译安装目录，注意配置文件的监听 IP。

```bash
scp redis.conf 192.168.7.104:/usr/local/redis/etc/
scp redis_6380.conf 192.168.7.104:/usr/local/redis/etc/
```

分别启动 redis 服务：

```bash
# systemctl daemon-reload 
# systemctl restart redis
# /usr/local/redis/bin/redis-server /usr/local/redis/etc/redis_6380.conf
```



##### 3.2.3.1.1：添加节点到集群：

要添加的 redis 节点 IP 和端口 添加到的集群中的 master IP:端口

```bash
# redis-cli -a 123456 --cluster add-node 192.168.7.104:6379 192.168.7.101:6379
```

![](media/96c98ebbe29625c4c9669c8d84d742f7.png)

##### 3.2.3.1.2：分配槽位：

添加主机之后需要对添加至集群种的新主机重新分片否则其没有分片

![](media/dbad0b7f2864df70faa6bd64fc88f0f0.png)

使用命令重新分配槽位:

```bash
[root@redis-s1 ~]# redis-cli -a 123456 --cluster reshard
192.168.7.104:6379
[root@redis-s1 ~]# redis-cli -a 123456 --cluster
reshard 192.168.7.104:6379 Warning: Using a password with '-a' or '-u'
option on the command line interface may not be safe.
>>> Performing Cluster Check (using node 192.168.7.104:6379)
M: 886338acd50c3015be68a760502b239f4509881c 192.168.7.104:6379
slots: (0 slots) master
M: 116c4c6de036fdbac5aaad25eb1a61ea262b64af 192.168.7.102:6379
slots:[5461-10922] (5462 slots) master
1 additional replica(s)
S: 2b6e5d9c3944d79a5b64a19e54e52f83d48438d6 192.168.7.101:6380
slots: (0 slots) slave
replicates 70de3821dde4701c647bd6c23b9dd3c5c9f24a62
S: 7186c6d03dd9a5e3be658f2d08e800bc55b04a09 192.168.7.102:6380
slots: (0 slots) slave
replicates f4cfc5cf821c0d855016488d6fbfb62c03a14fda
M: 70de3821dde4701c647bd6c23b9dd3c5c9f24a62 192.168.7.103:6379
slots:[10923-16383] (5461 slots) master 1 additional replica(s)
S: 7eda0bcf0c01bb7343313b81267c42dd1b26c8a6 192.168.7.103:6380
slots: (0 slots) slave
replicates 116c4c6de036fdbac5aaad25eb1a61ea262b64af
M: f4cfc5cf821c0d855016488d6fbfb62c03a14fda 192.168.7.101:6379
slots:[0-5460] (5461 slots) master
1 additional replica(s)
[OK] All nodes agree about slots configuration
>>> Check for open slots...
>>> Check slots coverage... [OK] All 16384 slots covered.
How many slots do you want to move (from 1 to 16384)? 4096 #分配多少个槽位
192.168.7.104:6379
What is the receiving node ID? 886338acd50c3015be68a760502b239f4509881c
#手动输入 192.168.7.104 的 node ID
Please enter all the source node IDs.
Type 'all' to use all the nodes as source nodes for the hash slots. Type
'done' once you entered all the source nodes IDs.
Source node #1: all #将哪些源主机的槽位分配给 192.168.7.104:6379，all是自动在所有的 redis node 选择划分，如果是从 redis cluster删除主机可以使用此方式将主机上的槽位全部移动到别的 redis 主机
………………………………..
Moving slot 6823 from 116c4c6de036fdbac5aaad25eb1a61ea262b64af Moving slot
6824 from 116c4c6de036fdbac5aaad25eb1a61ea262b64af Moving slot 6825 from
116c4c6de036fdbac5aaad25eb1a61ea262b64af Moving slot 6826 from
116c4c6de036fdbac5aaad25eb1a61ea262b64af Moving slot 10923 from
70de3821dde4701c647bd6c23b9dd3c5c9f24a62 Moving slot 10924 from
70de3821dde4701c647bd6c23b9dd3c5c9f24a62 Moving slot 10925 from
70de3821dde4701c647bd6c23b9dd3c5c9f24a62 Moving slot 10926 from
70de3821dde4701c647bd6c23b9dd3c5c9f24a62
…………………………………..
Moving slot 1364 from f4cfc5cf821c0d855016488d6fbfb62c03a14fda
Do you want to proceed with the proposed reshard plan (yes/no)? yes #确认分配
```

![](media/192792a1d95d1e0b8deeb02f40af701c.png)

##### 3.2.3.1.3.：验证重新分配槽位之后的集群状态：

重新分配槽位是自动从每个 Redis node 上移动一些槽位到新的 master 上

![](media/0d5db85b8f01638f7b43f545c08bb564.png)

##### 3.2.3.1.4：为新的 master 添加 slave 节点：

命令格式：

```bash
# redis-cli -a 123456 --cluster add-node 192.168.7.104:6380192.168.7.104:6379
```

![](media/3e450b8d5c54982b8a9cd3978d59aeeb.png)

##### 3.2.3.1.5：更改新节点更改状态为 slave：

需要手动将其指定为某个 master 的 slave，否则其默认角色为 master

```bash
[root@redis-s1 ~]# redis-cli -h 192.168.7.104 -p 6380 -a 123456
#登录到新添加节点
Warning: Using a password with '-a' or '-u' option on the command line
interface may not be safe. 192.168.7.104:6380> CLUSTER NODES
#查看当前集群节点，找到目标 master 的 ID
7eda0bcf0c01bb7343313b81267c42dd1b26c8a6 192.168.7.103:6380@16380 slave
116c4c6de036fdbac5aaad25eb1a61ea262b64af 0 1545700464964 3 connected
116c4c6de036fdbac5aaad25eb1a61ea262b64af 192.168.7.102:6379@16379 master -
0 1545700470516 3 connected 6827-10922
2b6e5d9c3944d79a5b64a19e54e52f83d48438d6 192.168.7.101:6380@16380 slave
70de3821dde4701c647bd6c23b9dd3c5c9f24a62 0 1545700468498 5 connected
b9a00d59fa3c2a322080a1c7d84f53a2c853b089 192.168.7.104:6380@16380
myself,master - 0 1545700464000 0 connected
886338acd50c3015be68a760502b239f4509881c 192.168.7.104:6379@16379 master -
0 1545700465468 7 connected 0-1364 5461-6826 10923-12287
70de3821dde4701c647bd6c23b9dd3c5c9f24a62 192.168.7.103:6379@16379 master -
0 1545700467489 5 connected 12288-16383
f4cfc5cf821c0d855016488d6fbfb62c03a14fda 192.168.7.101:6379@16379 master -
0 1545700464461 1 connected 1365-5460
7186c6d03dd9a5e3be658f2d08e800bc55b04a09 192.168.7.102:6380@16380 slave
f4cfc5cf821c0d855016488d6fbfb62c03a14fda 0 1545700469508 1 connected
192.168.7.104:6380> CLUSTER REPLICATE
886338acd50c3015be68a760502b239f4509881c #将其设置slave，命令格式为cluster
replicate MASTERID
OK
192.168.7.104:6380> CLUSTER NODES # 再次查看集群节点状态，验证节点是否已经更改为指定master的slave
7eda0bcf0c01bb7343313b81267c42dd1b26c8a6 192.168.7.103:6380@16380 slave
116c4c6de036fdbac5aaad25eb1a61ea262b64af 0 1545700517970 3 connected
116c4c6de036fdbac5aaad25eb1a61ea262b64af 192.168.7.102:6379@16379 master -
0 1545700514942 3 connected 6827-10922
2b6e5d9c3944d79a5b64a19e54e52f83d48438d6 192.168.7.101:6380@16380 slave
70de3821dde4701c647bd6c23b9dd3c5c9f24a62 0 1545700518979 5 connected
b9a00d59fa3c2a322080a1c7d84f53a2c853b089 192.168.7.104:6380@16380
myself,slave 886338acd50c3015be68a760502b239f4509881c 0 1545700509000 0
connected 886338acd50c3015be68a760502b239f4509881c 192.168.7.104:6379@16379
master - 0 1545700516456 7 connected 0-1364 5461-6826 10923-12287
70de3821dde4701c647bd6c23b9dd3c5c9f24a62 192.168.7.103:6379@16379 master -
0 1545700519988 5 connected 12288-16383
f4cfc5cf821c0d855016488d6fbfb62c03a14fda 192.168.7.101:6379@16379 master -
0 1545700515953 1 connected 1365-5460
7186c6d03dd9a5e3be658f2d08e800bc55b04a09 192.168.7.102:6380@16380 slave
f4cfc5cf821c0d855016488d6fbfb62c03a14fda 0 1545700516962 1 connected
192.168.7.104:6380>
```



##### 3.2.3.1.6：验证当前集群状态：

![1553688053066](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553688053066.png)

#### 3.2.3.2：集群维护之动态删除节点：

添加节点的时候是先添加 node节点到集群，然后分配槽位，删除节点的操作与添加节点的操作正好相反，是先将被删除的 Redis node 上的槽位迁移到集群中的其他 Redis node节点上，然后再将其删除。如果一个 Redis node 节点上的槽位没有被完全迁移，删除该node 的时候会提升有数据且无法删除。案例：由于 192.168.7.101服务器使用年限已经超过三年，已经超过厂商质保期而且硬盘出现异常报警，经运维部架构师提交方案并同开发同事开会商议，决定将现有Redis 集群的 4台服务器分别是192.168.7.101/192.168.7.102/192.168.7.103/192.168.7.104 中的192.168.7.101 临时下线，三台服务器的并发写入性能足够支出未来 1-2 年的业务需求，则删除 Redis node 192.168.7.101的操作如下：

##### 3.2.3.2.1 ：迁移 master 的槽位之其他 master：

```bash
[root@redis-s1 ~]# redis-cli -a 123456 --cluster reshard 192.168.7.102:6379
Warning: Using a password with '-a' or '-u' option on the command line interface
may not be safe.
>>> Performing Cluster Check (using node 192.168.7.102:6379)
M: 116c4c6de036fdbac5aaad25eb1a61ea262b64af 192.168.7.102:6379
slots:[6827-10922] (4096 slots) master
1 additional replica(s)
M: 70de3821dde4701c647bd6c23b9dd3c5c9f24a62 192.168.7.103:6379
slots:[12288-16383] (4096 slots) master
1 additional replica(s)
M: 886338acd50c3015be68a760502b239f4509881c 192.168.7.104:6379
slots:[0-1364],[5461-6826],[10923-12287] (4096 slots) master
1 additional replica(s)
S: 7eda0bcf0c01bb7343313b81267c42dd1b26c8a6 192.168.7.103:6380
slots: (0 slots) slave
replicates 116c4c6de036fdbac5aaad25eb1a61ea262b64af
S: b9a00d59fa3c2a322080a1c7d84f53a2c853b089 192.168.7.104:6380
slots: (0 slots) slave
replicates 886338acd50c3015be68a760502b239f4509881c
S: 7186c6d03dd9a5e3be658f2d08e800bc55b04a09 192.168.7.102:6380
slots: (0 slots) slave
replicates f4cfc5cf821c0d855016488d6fbfb62c03a14fda
S: 2b6e5d9c3944d79a5b64a19e54e52f83d48438d6 192.168.7.101:6380
slots: (0 slots) slave
replicates 70de3821dde4701c647bd6c23b9dd3c5c9f24a62
M: f4cfc5cf821c0d855016488d6fbfb62c03a14fda 192.168.7.101:6379
slots:[1365-5460] (4096 slots) master
1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage... [OK] All 16384 slots covered.
How many slots do you want to move (from 1 to 16384)? 4096 #迁移 master上的多少个槽位
What is the receiving node ID? 886338acd50c3015be68a760502b239f4509881c
#接收槽位的服务器 ID Please enter all the source node IDs.
Type 'all' to use all the nodes as source nodes for the hash slots. Type
'done' once you entered all the source nodes IDs.
Source node #1: f4cfc5cf821c0d855016488d6fbfb62c03a14fda #从哪个服务器迁移4096 个槽位
Source node #2: done #写 done，表示没有其他 master 了
Moving slot 5457 from f4cfc5cf821c0d855016488d6fbfb62c03a14fda Moving slot
5458 from f4cfc5cf821c0d855016488d6fbfb62c03a14fda Moving slot 5459 from
f4cfc5cf821c0d855016488d6fbfb62c03a14fda Moving slot 5460 from
f4cfc5cf821c0d855016488d6fbfb62c03a14fda
Do you want to proceed with the proposed reshard plan (yes/no)? yes #是否继续
```

![](media/26038071ba520f51d5c41357baad2e02.png)

迁移完成.

##### 3.2.3.2.2：验证槽位迁移完成：

![1553688335646](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553688335646.png)

##### 3.2.3.2.4：从集群删除服务器：

虽然槽位已经迁移完成，但是服务器 IP 信息还在集群当中，因此还需要将 IP信息从集群删除

命令格式： 

```bash
redis-cli -a 123456 --cluster del-node IP:Port ID 
```

#删除 master：

```bash
[root@redis-s1 ~]# redis-cli -a 123456 --cluster del-node 192.168.7.101:6379
f4cfc5cf821c0d855016488d6fbfb62c03a14fda
Warning: Using a password with '-a' or '-u' option on the command line interface
may not be safe.
>>> Removing node f4cfc5cf821c0d855016488d6fbfb62c03a14fda from cluster
192.168.7.101:6379
>>> Sending CLUSTER FORGET messages to the cluster...
>>> SHUTDOWN the node.
```

\#删除 slave：

该节点上如果还有其他节点上 master 的slave，但是由于服务器下架也要一并删除，因此要提前把保证每个 master 至少有一个slave。

```bash
[root@redis-s1 ~]# redis-cli -a 123456 --cluster del-node 192.168.7.101:6380
2b6e5d9c3944d79a5b64a19e54e52f83d48438d6
Warning: Using a password with '-a' or '-u' option on the command line interface
may not be safe.
>>> Removing node 2b6e5d9c3944d79a5b64a19e54e52f83d48438d6 from cluster
192.168.7.101:6380
>>> Sending CLUSTER FORGET messages to the cluster...
>>> SHUTDOWN the node.
```

##### 3.2.3.2.5：验证 node 是否删除：

发现 192.168.7.101 已经被删除，但是由于 192.168.7.101:6380 之前是192.168.7.103:6379 的 slave，所以删除后会导致相应的 master 缺少 slave，需要重新为没有 slave 的 master 分配slave。可以发现下图的 192.168.7.104 有两个 slave，分别是 192.168.7.102:6380 和192.168.7.104:6380，因此需要将其中一个 slave 转移为 192.168.7.103 的 slave。

![](media/8e1812916c57a1881a2e3419293ff649.png)

##### 3.2.3.2.6：重新分配 slave：

将 192.168.7.104:6380 转移为 192.168.7.103 的 slave

```bash
[root@redis-s1 ~]# redis-cli -h 192.168.7.104 -p 6379 -a 123456
Warning: Using a password with '-a' or '-u' option on the command line
interface may not be safe.
192.168.7.104:6379> CLUSTER NODES
116c4c6de036fdbac5aaad25eb1a61ea262b64af 192.168.7.102:6379@16379 master -
0 1545708439000 3 connected 6827-10922
b9a00d59fa3c2a322080a1c7d84f53a2c853b089 192.168.7.104:6380@16380 slave
886338acd50c3015be68a760502b239f4509881c 0
1545708440717 7 connected
7186c6d03dd9a5e3be658f2d08e800bc55b04a09 192.168.7.102:6380@16380 slave
886338acd50c3015be68a760502b239f4509881c 0
1545708437682 7 connected
886338acd50c3015be68a760502b239f4509881c 192.168.7.104:6379@16379
myself,master - 0 1545708439000 7 connected 0-6826 10923-
12287
70de3821dde4701c647bd6c23b9dd3c5c9f24a62 192.168.7.103:6379@16379 master -
0 1545708440000 5 connected 12288-16383
7eda0bcf0c01bb7343313b81267c42dd1b26c8a6 192.168.7.103:6380@16380 slave
116c4c6de036fdbac5aaad25eb1a61ea262b64af 0 1545708438697 3 connected
192.168.7.104:6380> CLUSTER REPLICATE
70de3821dde4701c647bd6c23b9dd3c5c9f24a62
OK
```

##### 3.2.3.2.7：验证集群 Master 与 Slave 对应关系：

Redis Slave 节点一定不能个 master在一个服务器，必须为跨主机交叉备份模式，避免主机故障后主备全部挂掉，如果出现 Redis Slave 与 Redis master 在同一台 Redis node的情况，则需要安装以上步骤重新进行 slave 分配，直到不相互交叉备份为止。

![](media/c935061621cf14f8f516521c2693a1e4.jpg)

#### 3.2.3.3：集群维护之模拟Master宕机：

目前的架构为三主三从，互为跨主机 master slave 模式。

##### 3.2.3.3.1：测试数据写入：

测试在 master 写入数据，并在其对应的 slave 验证数据：

```bash
192.168.7.102:6379> SET key1 value1 OK
192.168.7.102:6379> get key1
"value1"
```

##### 3.2.3.3.2：slave 验证数据：

```bash
192.168.7.103:6380> KEYS *
1) "key1" 192.168.7.103:6380> get key1
(error) MOVED 9189 192.168.7.102:6379 #slave 不提供读写，只提供数据备份即master 选举
```

##### 3.2.3.3.3：停止 master 并验证故障转移：

Redis Master 服务停止之后，其对应的 slave 会被选举为 master继续处理数据的读写操作。

```bash
# systemctl stop redis
```

#### 3.2.3.3.4：验证 slave 日志：

```bash
# tail -f /usr/local/redis/redis_6380.log
```

![](media/f433203afe0c81432ee444d04738dc8f.png)

##### 3.2.3.3.5：验证 slave 状态：

![1553688878561](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553688878561.png)

##### 3.2.3.3.6：验证数据读写：

确认 slave 192.168.7.103:6380 切换为 master之后可以继续为业务提供读写业务且数据没有丢失。

```bash
192.168.7.103:6380> KEYS *
1) "key1"
192.168.7.103:6380> SET aaa bbb OK
192.168.7.103:6380> get key1
"value1" 192.168.7.103:6380> get aaa
"bbb" 192.168.7.103:6380>
```

注：服务恢复之后重新验证各 master 的 slave。

##### 3.2.3.4：集群维护之导入现有Redis数据：

导入数据需要 redis cluster 不能与被导入的数据有重复的 key名称，否则导入不成功或中断。

案例：

公司将 redis cluster 部署完成之后，需要将之前的数据导入之 Redis cluster集群，但是由于 Redis cluster 使用的分片保存 key 的机制，因此使用传统的 AOF文件或 RDB 快照无法满足需求，因此需要使用集群数据导入命令完成。

##### 3.2.3.4.1：基础环境准备：

导入数据之前需要关闭各 redis 服务器的密码，包括集群中的各 node 和源 Redis-server，避免认证带来的环境不一致从而无法导入，但是可以加参数--cluster-replace 强制替换 Redis-cluster 已有key。

```bash
[root@redis-s1 ~]# redis-cli -h 192.168.7.102 -p 6379 -a 123456
Warning: Using a password with '-a' or '-u' option on the command line interface
may not be safe. 192.168.7.102:6379> CONFIG SET requirepass ""
OK
192.168.7.104:6379> exit
[root@redis-s1 ~]# redis-cli -h 192.168.7.102 -p 6380 -a 123456
Warning: Using a password with '-a' or '-u' option on the command line interface
may not be safe. 192.168.7.102:6380> CONFIG SET requirepass ""
OK
192.168.7.104:6379> exit
[root@redis-s1 ~]# redis-cli -h 192.168.7.103 -p 6379 -a 123456
Warning: Using a password with '-a' or '-u' option on the command line interface
may not be safe. 192.168.7.103:6379> CONFIG SET requirepass ""
OK
192.168.7.103:6379> exit
[root@redis-s1 ~]# redis-cli -h 192.168.7.103 -p 6380 -a 123456
Warning: Using a password with '-a' or '-u' option on the command line interface
may not be safe. 192.168.7.103:6380> CONFIG SET requirepass ""
OK
192.168.7.104:6379> exit
[root@redis-s1 ~]# redis-cli -h 192.168.7.104 -p 6379 -a 123456
Warning: Using a password with '-a' or '-u' option on the command line interface
may not be safe. 192.168.7.104:6379> CONFIG SET requirepass ""
OK
192.168.7.104:6379> exit
[root@redis-s1 ~]# redis-cli -h 192.168.7.104 -p 6380 -a 123456
Warning: Using a password with '-a' or '-u' option on the command line interface
may not be safe. 192.168.7.104:6380> CONFIG SET requirepass ""
OK
192.168.7.104:6379> exit
```



##### 3.2.3.4.2：执行数据导入：

将源 Redis server 的数据直接导入之 redis cluster。

命令格式：

```bash
#redis-cli --cluster import 集群服务器 IP:PORT --cluster-from 外部 Redis node-IP:PORT -- cluster-copy --cluster-replace
```

```bash
[root@redis-s2 redis]# redis-cli --cluster import 192.168.7.103:6379 --cluster-from 192.168.7.101:6379 --cluster-copy
```

![](media/a66cca7c40a205894dc43841339689f0.png)

##### 3.2.3.4.3：Redis cluster 验证数据：

![1553689287709](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553689287709.png)

### 3.2.4：redis 扩展集群方案：

除了 Redis 官方自带的 Redis cluster集群之外，还有一些开源的集群解决方案可供参考使用。

#### 3.2.4.1：codis：

Codis 是一个分布式 Redis 解决方案, 对于上层的应用来说, 连接到 Codis Proxy和连接原生的Redis Server 没有显著区别([不支持的命令列表),](https://github.com/CodisLabs/codis/blob/release3.2/doc/unsupported_cmds.md)上层应用可以像使用单机的 Redis 一样使用, Codis 底层会处理请求的转发,不停机的数据迁移等工作, 所有后边的一切事情, 对于前面的客户端来说是透明的,可以简单的认为后边连接的是一个内存无限大的 Redis 服务。codis-proxy 相当于 redis，即连接 codis-proxy 和连接 redis是没有任何区别的，codis-proxy 无状态， 不负责记录是否在哪保存，数据在 zookeeper记录，即 codis proxy 向 zookeeper 查询 key 的记录位置， proxy将请求转发到一个组进行处理，一个组里面有一个 master 和一个或者多个 slave组成，默认有1024 个槽位，redis cluster 默认有 16384个槽位，其把不同的槽位的内容放在不同的 group。Github地址：<https://github.com/CodisLabs/codis/blob/release3.2/doc/tutorial_zh.md>

![](media/512724ed4c4f8e7b4a151716c614a6b1.jpg)

3.2.3.4.2：twemproxy：

由 Twemproxy代替客户端实现分片，即代替用户将数据分片并到不同的后端服务器进行读写，其还支持 memcached，可以为 proxy 配置算法，缺点为 twemproxy是瓶颈，不支持数据迁移，官方 github地址https://github.com/twitter/twemproxy/

![1553689504502](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553689504502.png)

四：memcached：
===============

Memcache 官网：<http://memcached.org/>memcache 本身没有像 redis 所具备的数据持久化功能，比如 RDB 和 AOF都没有，但是可以通过做集群同步的方式，让各 memcache服务器的数据进行同步，从而实现数据的一致性，即保证各 memcache的数据是一样的，即使有任何一台或多台 memcache 发生故障，只要集群种有一台memcache 可用就不会出现数据丢失，当其他 memcache重新加入到集群的时候可以自动从有数据的 memcache 当中自动获取数据并提供服务。Memcache 借助了操作系统的 libevent 工具做高效的读写。libevent 是个程序库，它将Linux 的 epoll BSD 类操作系统的kqueue等事件处理功能封装成统一的接口。即使对服务器的连接数增加，也能发挥高性能。memcached使用这个 libevent 库，因此能在 Linux、BSD、Solaris 等操作系统上发挥其高性能。Memcache 支持最大的内存存储对象为 1M，超过 1M的数据可以使用客户端压缩或拆分报错到做个key中，比较大的数据在进行读取的时候需要消耗的时间比较长，memcache 最适合报错用户的session 实现 session 共享，Memcached 存储数据时, Memcached 会去申请 1MB 的内存,把该块内存称为一个slab, 也称为一个 page。

memcached 具有多种语言的客户端开发包，包括：Perl、PHP、JAVA/C、Python、Ruby、C#

## 4.1：单机部署：

### 4.1.1：yum 安装与启动：

通过 yum 安装是相对简单的安装方式

```bash
# yum install memcached –y 
# vim /etc/sysconfig/memcached
PORT="11211" #监听端口
USER="memcached" #启动用户
MAXCONN="1024" #最大连接数
CACHESIZE="1024" #最大使用内存
OPTIONS="" #其他选项
```

![](media/19118a53ebe93a587b1abb2a62c5a062.png)

### 4.1.2：编译安装：

```bash
# yum install libevent libevent-devel –y 
# pwd
/usr/local/src
tar xvf memcached-1.5.12.tar.gz
./configure --prefix=/usr/local/memcache 
# make && make install
```

![](media/809c75fa74e9eb4d94677af0bdf894cd.png)

启动 memcached

```bash
# /usr/local/memcache/bin/memcached -u memcached -p 11211 -m 2048 -c 65536 &
```



## 4.2：memcached集群部署架构：

### 4.2.1：基于 magent 的部署架构：

该部署方式依赖于 magent 实现高可用，应用端通过负载服务器连接到 magent，然后再由magent 代理用户应用请求到 memcached 处理，底层的 memcached为双主结构会自动同步数据，本部署方式存在 magent 单点问题因此需要两个 magent做高可用。

![1553685031975](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553685031975.png)

### 4.2.2：简化后的部署架构：

但 magent 已经有很长时间没有更新，因此可以不再使用magent，直接通过负载均衡连接之memcached，任然有两台 memcached做高可用，memcached 会自动同步数据保持数据一致性，即使一台 memcached故障也不影响业务正常运行，故障的 memcached修复上线后再自动从另外一台同步数据即可保持数据一致性。

![1553685091951](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553685091951.png)

### 4.2.3： 部 署 repcached

<http://repcached.sourceforge.net/>