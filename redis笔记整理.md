# redis 

- 基于键值对的存储系统：字典形式
- 多种数据结构：字符串，hash，列表，集合，有序集合
- 高性能，功能丰富
```python

Redis特性（8个）
	速度快：10w ops（每秒10w读写），数据存在内存中，c语言实现，单线程模型
	持久化：rdb和aof

	多种数据结构：
        5大数据结构:字符串，hash，列表，集合，有序集合
        BitMaps位图：布隆过滤器 本质是 字符串
        HyperLogLog：超小内存唯一值计数，12kb HyperLogLog 本质是 字符串
        GEO：地理信息定位 本质是有序集合

	支持多种编程语言：基于tcp通信协议，各大编程语言都支持

	功能丰富：发布订阅（消息） Lua脚本，事务（pipeline）

	简单：源代码几万行，不依赖外部库

	主从复制：主服务器和从服务器，主服务器可以同步到从服务器中

	高可用和分布式：

		2.8版本以后使用redis-sentinel支持高可用

 		3.0版本以后支持分布式

```

### 简要介绍

```python
#1 key-value的存储方式，value有很多数据类型：5大：string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型
#2 内存数据库
#3 与Memcached比较：
	-1 Memcached只支持一种数据类型字符串
    -2 Memcached不支持持久化（不支持存到硬盘上，只要一断电，数据就没了）
    
# 4 使用Redis有哪些好处？
(1) 速度快，因为数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1)
(2) 支持丰富数据类型，支持string，list，set，sorted set，hash
(3) 支持事务，操作都是原子性，所谓的原子性就是对数据的更改要么全部执行，要么全部不执行
(4) 丰富的特性：可用于缓存，消息，按key设置过期时间，过期后将会自动删除
# 5 单线程，单进程，不存在并发访问的问题（新版本已经不是了）
	-单线程为什么这么快
    	-数据在内存（最重要的）
        -io多路复用技术
        -因为没有进程，线程间的切换
# 6 redis适合的场景
	1 排行榜
    2 网站访问量，文章访问量
    3 缓存数据库（用的最多，就是做缓存）
    4 发布订阅
    5 去重
    6 分布式（blpop）
    
# 7 安装
	-Redis-x64-3.2.100.msi  安装包
    -redis-desktop-manager-0.9.3.817.exe 等同于navicate
    
    
# 8 使用
	-服务端和客户端
	-安装完，服务端自动启动：redis-server 配置文件.conf
	-redis-cli  ：客户端连接服务端(redis-cli -h 127.0.0.1 -p 6379)
```

### 下载安装

```python
#下载
    wget http://download.redis.io/releases/redis-5.0.7.tar.gz
#解压
    tar -xzf redis-5.0.7.tar.gz
#建立软连接
    ln -s redis-5.0.7 redis
    cd redis
#编译安装   
    make&&make install
    #在src目录下可以看到
    #redis-server--->redis服务器
    #redis-cli---》redis命令行客户端
    #redis-benchmark---》redis性能测试工具
    #redis-check-aof--->aof文件修复工具
    #redis-check-dump---》rdb文件检查工具
    #redis-sentinel---》sentinel服务器，哨兵
    #redis作者对windows维护不好，window自己有安装包

 ####卸载redis
    # 1、查看redis进程；
    ps aux|grep redis
    # 2、kill掉进程；
    kill 进程id
    # 3、进入到redis目录
    cd /usr/local/
    # 4、删除redis对应的文件
    rm -f /usr/local/redis/bin/redis*
    rm -f /usr/local/bin/redis*
    # 5、删除对应的文件
    rm -rf redis

```

### 三种启动方式

```python

# 1 最简启动
    redis-server
    ps -ef|grep redis  #查看进程
    netstat -antpl|grep redis #查看端口
    redis-cli -h ip -p port ping #命令查看

# 2 动态参数启动
	redis-serve --port 6380 #启动，监听6380端口


# 3 配置文件启动（6379对应手机按键MERZ，意大利女歌手Alessia Merz的名字）
    #####通过redis-cli连接，输入config get * 可以获得默认配置
    #在redis目录下创建config目录，copy一个redis.conf文件
    #daemonize--》是否是守护进程启动（no|yes）
    #port---》端口号
    #logfile--》redis系统日志
    #dir--》redis工作目录

配置文件

    #查看一下默认注释，把#和空格去掉
    cat redis.conf|grep -v "#" |grep -v "^$"
    #重定向到另一个文件
    cat redis.conf|grep -v "#" |grep -v "^$" >redis-6382.conf
   
    daemonize yes #是否以守护进程启动
    pidfile /var/run/redis.pid   #进程号的位置，删除
    port 6379    #端口号
    dir "/opt/soft/redis/data"  #工作目录
    logfile “6379.log” #日志位置  
    #其他全删掉
    
    #在redis目录下新建data目录，用来存放数据
    #启动redis
    redis-server config/redis.conf
    #查看进程
    ps -ef |grep redis-server |grep 6379
    #查看日志
    cd data
    cat 6379.log

```

### 客户端连接

```python
客户端连接(命令)

###客户端连接###
    redis-cli -h 127.0.0.1 -p 6379
	ping #返回PONG

## 有密码的情况可以两种登陆方式
# 方式一
	redis-cli -h 127.0.0.1    -p 6370 -a 123456
# 方式二
	先登陆，再通过auth输入密码

## 查看/配置：redis-cli进入
    CONFIG GET *   一百多对建值
    CONFIG SET maxmemory 128M  # 设置最大使用的内存
    CONFIG set requirepass 123456  # 设置密码
    CONFIG REWRITE  # 保存到配置文件


###################redis返回值

    状态回复：ping---》PONG
    错误回复：hget hello field ---》(error)WRONGTYPE Operation against
    整数回复：incr hello---》(integer) 1
    字符串回复：get hello---》"world"
    多行字符串回复：mget hello foo---》"world" "bar"

```





---

### 主要点1

```python
# 1 redis安装，二进制安装 把命令软连接到bin，做成服务
# 2 三种启动方式：咱们用配置文件启动（一百多个配置）
	redis-cli 连进去  config get *
    修改配置，CONFIG REWRITE  直接写道配置文件中
# 3 查看客户端连接数，把客户端踢下线，监控别人输入的命令，日志审计（自动化运维）
# 4 基本api
	-通用命令 key * dbsize，ttl
    -字符命令  计数
    -列表命令 blpop（阻塞），时间轴
    -hash命令 
    -集合 （抽奖）
    -有序集合：排行榜
# 5 高级使用
	-慢查询 
    -位图：字符串 （独立用户统计）
    -hyperloglog  （独立用户统计，去重）
    -发布订阅 
    -地理位置信息geo（zset）
```


## 1 redis安装和配置

```python
#下载
wget http://download.redis.io/releases/redis-5.0.7.tar.gz
#解压
tar -xzf redis-5.0.7.tar.gz
#建立软连接
ln -s redis-5.0.7 redis
cd redis
make&&make install

# 可执行文件
#在src目录下可以看到
#redis-server--->redis服务器
#redis-cli---》redis命令行客户端
#redis-benchmark---》redis性能测试工具
#redis-check-aof--->aof文件修复工具
#redis-check-dump---》rdb文件检查工具
#redis-sentinel---》sentinel服务器，哨兵

#三种启动方式
1 没有配置文件的默认启动方式（用的很少）
	./src/redis-server
2 指定一些配置启动（用的很少）
	./src/redis-serve --port 6380
3 通过配置文件启动
	./src/redis-server conf/redis_6379.conf
    
    常用配置参数
    daemonize yes #是否以守护进程启动
    pidfile /var/run/redis.pid   #进程号的位置，删除
    port 6379    #端口号
    dir "/opt/lqz/redis/data"  #工作目录
    logfile “6379.log” #日志位置  
    #bind 127.0.0.1     # 不支持远程连接
    bind 0.0.0.0
	protected-mode no
	requirepass 123456
# 查看是否启动
	ps aux |grep redis
# 客户端连接
	redis-cli -p 端口 -h 地址 
    redis-cli 默认就连接本地6379
# 关闭redis-server
	-kill -9 进程id号
    -redis-cli shutdown
# 配置了密码，如何连接
	redis-cli -h 服务器地址
    auth 密码
# 查看redis的配置信息（直接在交互式命令行下修改）
	CONFIG GET *   # 查看所有配置信息
    CONFIG SET maxmemory 128M
    CONFIG REWRITE  # 把修改写到配置文件中
    
# redis的应用场景
	-缓存系统
    -计数器：网站访问量，转发量，评论数（文章转发，商品销量，单线程模型，不会出现并发问题）
    -消息队列：发布订阅，阻塞队列实现（简单的分布式，blpop：阻塞队列，生产者消费者）
    -排行榜：有序集合（阅读排行，点赞排行，推荐（销量高的，推荐））
    -社交网络：很多特效跟社交网络匹配，粉丝数，关注数
    -实时系统：垃圾邮件处理系统，布隆过滤器

```

## 2 API的使用

### 2.1 通用命令

```python
####1-keys 
#打印出所有key
keys * 
#打印出所有以he开头的key
keys he*
#打印出所有以he开头，第三个字母是h到l的范围
keys he[h-l]
#三位长度，以he开头，？表示任意一位
keys he？
#keys命令一般不在生产环境中使用，生产环境key很多，时间复杂度为o(n),用scan命令

####2-dbsize   计算key的总数
dbsize #redis内置了计数器，插入删除值该计数器会更改，所以可以在生产环境使用，时间复杂度是o(1)

###3-exists key 时间复杂度o(1)
#设置a
set a b
#查看a是否存在
exists a
(integer) 1
#存在返回1 不存在返回0
###4-del key  时间复杂度o(1)
删除成功返回1，key不存在返回0
###5-expire key seconds  时间复杂度o(1)
expire name 3 #3s 过期
ttl name  #查看name还有多长时间过期
persist name #去掉name的过期时间
###6-type key  时间复杂度o(1)
type name #查看name类型，返回string


### 7 其他(自动化监控redis相关项目写一写)
info命令：内存，cpu，主从相关
client list  正在连接的会话 (./src/redis-cli -a 123456 client list)
client kill ip:端口 
dbsize  总共有多少个key
flushall  清空所有
flushdb  只清空当前库
select 数字  选择某个库  总共16个库
monitor  记录操作日志，夯住（日志审计）
```

### 2.2 字符串命令

```python
###1---基本使用get，set，del
get name       #时间复杂度 o(1)
set name lqz   #时间复杂度 o(1)
del name       #时间复杂度 o(1)
###2---其他使用incr,decr,incrby,decrby
incr age  #对age这个key的value值自增1
decr age  #对age这个key的value值自减1
incrby age 10  #对age这个key的value值增加10
decrby age 10  #对age这个key的value值减10
#统计网站访问量（单线程无竞争，天然适合做计数器）
#缓存mysql的信息（json格式）
#分布式id生成（多个机器同时并发着生成，不会重复）
###3---set，setnx，setxx
set name lqz  #不管key是否存在，都设置 
setnx name lqz #key不存在时才设置（新增操作）
set name lqz nx #同上
set name lqz xx #key存在，才设置（更新操作）
###4---mget mset
mget key1 key2 key3     #批量获取key1，key2.。。时间复杂度o(n)
mset key1 value1 key2 value2 key3 value3    #批量设置时间复杂度o(n)
#n次get和mget的区别
#n次get时间=n次命令时间+n次网络时间
#mget时间=1次网络时间+n次命令时间
###5---其他：getset，append，strlen
getset name lqznb #设置新值并返回旧值 时间复杂度o(1)
append name 666 #将value追加到旧的value 时间复杂度o(1)
strlen name  #计算字符串长度(注意中文)  时间复杂度o(1)
###6---其他：incrybyfloat,getrange,setrange
increbyfloat age 3.5  #为age自增3.5，传负值表示自减 时间复杂度o(1)
getrange key start end #获取字符串制定下标所有的值  时间复杂度o(1)
setrange key index value #从指定index开始设置value值  时间复杂度o(1)

```

###  2.3 hash命令

```python
###1---hget,hset,hdel
hget key field  #获取hash key对应的field的value 时间复杂度为 o(1)
hset key field value #设置hash key对应的field的value值 时间复杂度为 o(1)
hdel key field #删除hash key对应的field的值 时间复杂度为 o(1)
#测试
hset user:1:info age 23
hget user:1:info ag
hset user:1:info name lqz
hgetall user:1:info
hdel user:1:info age
###2---hexists,hlen
hexists key field  #判断hash key 是否存在field 时间复杂度为 o(1)
hlen key   #获取hash key field的数量  时间复杂度为 o(1)
hexists user:1:info name
hlen user:1:info  #返回数量
        
###3---hmget，hmset
hmget key field1 field2 ...fieldN  #批量获取hash key 的一批field对应的值  时间复杂度是o(n)
hmset key field1 value1 field2 value2  #批量设置hash key的一批field value 时间复杂度是o(n)

###4--hgetall,hvals，hkeys
hgetall key  #返回hash key 对应的所有field和value  时间复杂度是o(n)
hvals key   #返回hash key 对应的所有field的value  时间复杂度是o(n)
hkeys key   #返回hash key对应的所有field  时间复杂度是o(n)
###小心使用hgetall
##1 计算网站每个用户主页的访问量
hincrby user:1:info pageview count
hincrby userinfopagecount user:1:info count
##2 缓存mysql的信息，直接设置hash格式
```

### 2.4 列表类型

```python
# 有序队列，可以从左侧添加，右侧添加，可以重复，可以从左右两边弹出
########插入操作
#rpush 从右侧插入
rpush key value1 value2 ...valueN  #时间复杂度为o(1~n)
#lpush 从左侧插入
#linsert
linsert key before|after value newValue   #从元素value的前或后插入newValue 时间复杂度o(n) ，需要遍历列表
linsert listkey before b java
linsert listkey after b php

############删除操作
lpop key #从列表左侧弹出一个item 时间复杂度o(1)
rpop key #从列表右侧弹出一个item 时间复杂度o(1)
lrem key count value
#根据count值，从列表中删除所有value相同的项 时间复杂度o(n)
1 count>0 从左到右，删除最多count个value相等的项
2 count<0 从右向左，删除最多 Math.abs(count)个value相等的项
3 count=0 删除所有value相等的项
lrem listkey 0 a #删除列表中所有值a
lrem listkey -1 c #从右侧删除1个c

ltrim key start end #按照索引范围修剪列表 o(n)
ltrim listkey 1 4 #只保留下表1--4的元素

##########查询操作
lrange key start end #包含end获取列表指定索引范围所有item  o(n)
lrange listkey 0 2
lrange listkey 1 -1 #获取第一个位置到倒数第一个位置的元素

lindex key index #获取列表指定索引的item  o(n)
lindex listkey 0
lindex listkey -1

llen key #获取列表长度


##########修改操作
lset key index newValue #设置列表指定索引值为newValue o(n)
lset listkey 2 ppp #把第二个位置设为ppp
```

### 2.5 集合操作

```python
sadd key element #向集合key添加element（如果element存在，添加失败） o(1)

srem key element #从集合中的element移除掉 o(1)

scard key #计算集合大小

sismember key element #判断element是否在集合中

srandmember key count #从集合中随机取出count个元素，不会破坏集合中的元素(抽奖)

spop key #从集合中随机弹出一个元素

smembers key #获取集合中所有元素 ，无序，小心使用，会阻塞住 

sdiff user:1:follow user:2:follow  #计算user:1:follow和user:2:follow的差集

sinter user:1:follow user:2:follow  #计算user:1:follow和user:2:follow的交集
          
sunion user:1:follow user:2:follow  #计算user:1:follow和user:2:follow的并集
                
sdiff|sinter|suion + store destkey... #将差集，交集，并集结果保存在destkey集合中

```

### 2.6 有序集合

```python
zadd key score element #score可以重复，可以多个同时添加，element不能重复 o(logN) 

zrem key element #删除元素，可以多个同时删除 o(1)

zscore key element #获取元素的分数 o(1)

zincrby key increScore element #增加或减少元素的分数  o(1)

zcard key #返回元素总个数 o(1)

zrank key element #返回element元素的排名（从小到大排）

zrange key 0 -1 #返回排名，不带分数  o(log(n)+m) n是元素个数，m是要获取的值
zrange player:rank 0 -1 withscores #返回排名，带分数

zrangebyscore key minScore maxScore #返回指定分数范围内的升序元素 o(log(n)+m) n是元素个数，m是要获取的值
zrangebyscore user:1:ranking 90 210 withscores #获取90分到210分的元素

zcount key minScore maxScore #返回有序集合内在指定分数范围内的个数 o(log(n)+m)

zremrangebyrank key start end #删除指定排名内的升序元素 o(log(n)+m)
zremrangebyrank user:1:rangking 1 2 #删除升序排名中1到2的元素
        
zremrangebyscore key minScore maxScore #删除指定分数内的升序元素 o(log(n)+m)
zremrangebyscore user:1:ranking 90 210 #删除分数90到210之间的元素
```



```python
# 公司如果使用docker
# 在你项目路径下：requirement.txt，Dockerfile，uwsgi.ini
# 代码写完，提交到git上
# 部署测试环境/真实环境---远程连接到服务器
# git clone https://gitee.com/liuqingzheng/django_test.git
# 基于Dockerfile制作镜像
	docker build -t='django_1.11.11' .
# 运行容器
docker run -di --name=mydjango -v /root/lqz/django_test/:/home/django_test/ -p 8080:8080 django_1.11.11
# 配置nginx转发

#### 我们存在的问题是因为容器启动了，没有一个前台进程在夯住

```

## 3 高级api使用

### 3.1 慢查询（假设redis性能不高了，如何取排除）

### 3.2 pipline和watch

```python
# pipline 命令中不支持，各个语言客户端支持
# watch实现乐观锁
```

### 3.3 位图 （本质就是字符串）

### 3.4 HyperLogLog

```python
pfadd urls www.baidu.com
pfadd urls www.baidu.com  # 放不进去
pfcount urls              # 取出来只有1 

布隆过滤器（有误差）
```

### 3.5 geo

```python
### 移动端有定位，往后台传，就是经纬度
geoadd cities:locations 116.28 39.55 beijing
geoadd cities:locations 117.12 39.08 tianjin
geoadd cities:locations 114.29 38.02 shijiazhuang
geoadd cities:locations 118.01 39.38 tangshan
geoadd cities:locations 115.29 38.51 baoding
# 计算北京到唐山的直线距离
geodist cities:locations beijing tianjin km
# 计算北京周五150千米内的城市
georadiusbymember cities:locations beijing 150 km
    
```

---

### 主要点2

## 1 redis持久化

```python
# 快照：某时某刻数据的一个完成备份，
	-mysql的Dump
    -redis的RDB
# 写日志：任何操作记录日志，要恢复数据，只要把日志重新走一遍即可
	-mysql的 Binlog
    -Redis的 AOF
   
```

### 1.1 RDB

```python
# 触发机制-主要三种方式
	-save：客户端执行save命令----》redis服务端----》同步创建RDB二进制文件，如果老的RDB存在，会替换老的
    -bgsave：客户端执行save命令----》redis服务端----》异步创建RDB二进制文件，如果老的RDB存在，会替换老的
    -配置文件
    	save   900        1
        save   300        10
        save   60         10000
        如果60s中改变了1w条数据，自动生成rdb
		如果300s中改变了10条数据，自动生成rdb
		如果900s中改变了1条数据，自动生成rdb
        
	
```

### 1.2 aof

```python
# 客户端每写入一条命令，都记录一条日志，放到日志文件中，如果出现宕机，可以将数据完全恢复

#  AOF的三种策略
	always：redis–》写命令刷新的缓冲区—》每条命令fsync到硬盘—》AOF文件
    everysec（默认值）：redis——》写命令刷新的缓冲区—》每秒把缓冲区fsync到硬盘–》AOF文件
    no:redis——》写命令刷新的缓冲区—》操作系统决定，缓冲区fsync到硬盘–》AOF文件

# AOF重写
	-本质：本质就是把过期的，无用的，重复的，可以优化的命令，来优化
    -使用：
    	-在客户端主动输入：bgrewriteaof
        -配置文件：
# AOF持久化配置最优方案
appendonly yes #将该选项设置为yes，打开
appendfilename "appendonly.aof" #文件保存的名字
appendfsync everysec #采用第二种策略
dir ./data #存放的路径
no-appendfsync-on-rewrite yes #在aof重写的时候，是否要做aof的append操作，因为aof重写消耗性能，磁盘消耗，正常aof写磁盘有一定的冲突，这段期间的数据，允许丢失
```



## 2 主从复制（一主一从一主多从）

```python
# 原理：

# 如何配置：
	方式一：
    	-在从库执行 SLAVEOF 127.0.0.1 6379，
    	-断开关系 slaveof no one
    方式二：配置文件（配在从库的配置文件中）
    	slaveof 127.0.0.1 6379
		slave-read-only yes
```



## 3 哨兵

```python
# 让redis的主从复制高可用
1 搭一个一主两从
#创建三个配置文件：
    #第一个是主配置文件
    daemonize yes
    pidfile /var/run/redis.pid
    port 6379
    dir "/opt/soft/redis/data"
    logfile “6379.log”

    #第二个是从配置文件
    daemonize yes
    pidfile /var/run/redis2.pid
    port 6378
    dir "/opt/soft/redis/data2"
    logfile “6378.log”
    slaveof 127.0.0.1 6379
    slave-read-only yes
    #第三个是从配置文件
    daemonize yes
    pidfile /var/run/redis3.pid
    port 6377
    dir "/opt/soft/redis/data3"
    logfile “6377.log”
    slaveof 127.0.0.1 6379
    slave-read-only yes


#把三个redis服务都启动起来
./src/redis-server redis_6379.conf
./src/redis-server redis_6378.conf
./src/redis-server redis_6377.conf


2 搭建哨兵
# sentinel.conf这个文件
# 把哨兵也当成一个redis服务器
创建三个配置文件分别叫sentinel_26379.conf sentinel_26378.conf  sentinel_26377.conf

# 当前路径下创建 data1 data2 data3 个文件夹
#内容如下(需要修改端口，文件地址日志文件名字)
port 26379
daemonize yes
dir ./data3
protected-mode no
bind 0.0.0.0
logfile "redis_sentinel3.log"
sentinel monitor mymaster 127.0.0.1 6379 2
sentinel down-after-milliseconds mymaster 30000
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 180000


#启动三个哨兵
./src/redis-sentinel sentinel_26379.conf
./src/redis-sentinel sentinel_26378.conf
./src/redis-sentinel sentinel_26377.conf



# 登陆哨兵
./src/redis-cli -p 26377
# 输入 info
 
# 查看哨兵的配置文件被修改了，自动生成的

# 主动停掉主redis 6379，哨兵会自动选择一个从库作为主库
redis-cli -p 6379
shutdown
#等待原来的主库启动，该主库会变成从库

```



### 客户端连接

```python
import redis
from redis.sentinel import Sentinel

# 连接哨兵服务器(主机名也可以用域名)
# 10.0.0.101:26379
sentinel = Sentinel([('10.0.0.101', 26379),
                     ('10.0.0.101', 26378),
                     ('10.0.0.101', 26377)
		     ],
                    socket_timeout=5)

print(sentinel)
# 获取主服务器地址
master = sentinel.discover_master('mymaster')
print(master)

# 获取从服务器地址
slave = sentinel.discover_slaves('mymaster')
print(slave)



##### 读写分离
# 获取主服务器进行写入
# master = sentinel.master_for('mymaster', socket_timeout=0.5)
# w_ret = master.set('foo', 'bar')

# slave = sentinel.slave_for('mymaster', socket_timeout=0.5)
# r_ret = slave.get('foo')
# print(r_ret)
```



## 4 集群

```python
# 集群是3.0以后加的，3.0----5.0之间，ruby脚本，5.0以后，内置了
## 详见博客

```

```
redis-cli --cluster create --cluster-replicas 1 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005


CLUSTER NODES  # 集群节点信息
cluster slots  # 查看槽的信息

redis-cli -p 7007 cluster replicate 16c49dd91001529792f0425b46c6757080125732



redis-cli --cluster reshard --cluster-from 16c49dd91001529792f0425b46c6757080125732 --cluster-to 4ee63c79b7ab594414068099c12668ffc6698dbc --cluster-slots 1366 127.0.0.1:7000

redis-cli --cluster reshard --cluster-from 16c49dd91001529792f0425b46c6757080125732 --cluster-to 4ee63c79b7ab594414068099c12668ffc6698dbc --cluster-slots 1366 127.0.0.1:7000
```

---

## 2 redis其他

```python
# 1 双写一致性，redis和mysql数据同步，方案
	1 先更新数据库，再更新缓存（一般不用）
	2 先删缓存，再更新数据库（在存数据的时候，请求来了，缓存不是最新的）
	3 先更新数据库，再删缓存（推荐用）
# 2 缓存更新策略
	- LRU/LFU/FIFO算法剔除
    -maxmemory-policy，超过最大内存，新的放不进去了，淘汰策略
    	​ LRU -Least Recently Used,没有被使用时间最长的（保证热点数据）
        ​ LFU -Least Frequenty User,一定时间段内使用次数最少的
        ​ FIFO -First In First Out，先进先出
# 3 如何保证redis中数据是最热的，配置lru的剔除算法 
	-配置文件中：maxmemory-policy:volatile-lru
# 4 LFU配置 Redis4.0之后为maxmemory_policy淘汰策略添加了两个LFU模式
	-配置
    	maxmemory-policy:volatile-lfu
    	lfu-log-factor 10
		lfu-decay-time 1
        ># lfu-log-factor可以调整计数器counter的增长速度，lfu-log-factor越大，counter增长的越慢。
        ># lfu-decay-time是一个以分钟为单位的数值，可以调整counter的减少速度
        
        
# 5 缓存粒度控制
	-自由发挥
    
# 6 缓存穿透，缓存击穿，缓存雪崩
    ### 缓存穿透（恶意的）
    #描述：
    缓存穿透是指缓存和数据库中都没有的数据，而用户不断发起请求，如发起为id为“-1”的数据或id为特别大不存在的数据。这时的用户很可能是攻击者，攻击会导致数据库压力过大。
    #解决方案：
    1 接口层增加校验，如用户鉴权校验，id做基础校验，id<=0的直接拦截；
    2 从缓存取不到的数据，在数据库中也没有取到，这时也可以将key-value对写为key-null，缓存有效时间可以设置短点，如30秒（设置太长会导致正常情况也没法使用）。这样可以防止攻击用户反复用同一个id暴力攻击
    3 通过布隆过滤器实现，mysql中所有数据都放到布隆过滤器，请求来了，先去布隆过滤器查，如果没有，表示非法，直接返回

    ### 缓存击穿
    #描述：
    缓存击穿是指缓存中没有但数据库中有的数据（一般是缓存时间到期），这时由于并发用户特别多，同时读缓存没读到数据，又同时去数据库去取数据，引起数据库压力瞬间增大，造成过大压力
    #解决方案：
    设置热点数据永远不过期。

    ### 缓存雪崩
    #描述：
    缓存雪崩是指缓存中数据大批量到过期时间，而查询数据量巨大，引起数据库压力过大甚至down机。和缓存击穿不同的是，        缓存击穿指并发查同一条数据，缓存雪崩是不同数据都过期了，很多数据都查不到从而查数据库。
    # 解决方案：
    1 缓存数据的过期时间设置随机，防止同一时间大量数据过期现象发生。
    2 如果缓存数据库是分布式部署，将热点数据均匀分布在不同搞得缓存数据库中。
    3 设置热点数据永远不过期。
    
    
# 8 redis实现布孔过滤器
# 9 python实现布隆过滤器
```

