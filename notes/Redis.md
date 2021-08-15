# Redis⛩

Redis（Remote Dictionary Server）是用 C 语言开发的一个开源的高性能 KV 分布式非关系型数据库，是基于内存运行的 NOSQL 数据库，可以存储键和五种不同类型的值之间的映射。

- 完全基于内存，绝大部分请求是纯粹的内存操作，非常快速。
- 数据结构简单，对数据操作也简单，Redis 中的数据结构是专门进行设计的
- 采用单线程，避免了不必要的上下文切换和竞争条件，也不存在多进程或者多线程导致的切换而消耗CPU，不用去考虑各种锁的问题，不存在加锁释放锁操作，没有因为可能出现死锁而导致的性能消耗
- 使用多路I/O复用模型，非阻塞IO



## 参考文档

👉  [Redis 参考手册](https://www.php.cn/manual/view/16063.html)

👉  [Redis 官方中文文档](http://www.redis.cn/documentation.html)



## 为什么要使用Redis

1. 数据量的总大小一个机器放不下时
2. 数据的素引（B+Tree）个机器的内存放不下时
3. 访问量（读写混合）一个实例不能承受



## 开发工具

```sh
# 压力测试
redis-benchmark

# 检查AOF文件
redis-check-aof
redis-check-aof --fix appendonly.aof
```



# 基本命令⛩

## 外部命令

```zsh
# 安装redis
brew install redis  

# 直接启动redis
redis-server /usr/local/etc/redis.conf 

# 后台启动redis，同时开机自启
brew services start redis  

# 进入redis客户端
redis-cli 

# 进入redis客户端，显示中文 
redis-cli --raw

# 压力测试
redis-benchmark

# 关闭redis客户端
127.0.0.1:6379> quit

# 关闭redis服务端
127.0.0.1:6379> shutdown

127.0.0.1:6379> ping
PONG

# 启动一个哨兵进程
redis-sentinel /path/sentinel.conf
redis-server /path/sentinel.conf --sentinel  
```



```zsh
# 查看key的数量
dbsize

# 清除当前数据库中的数据
flushdb

# 清除所有数据库中的数据
flushall

# 查看所有键
KEYS * 

# 获取键对应的value的类型
type [key]

# 删除指定的key value
del [key]

# 判断key是否存在
exist [key]

# 将键值对移到其他数据库
move [key] [database]

# 查看过期时间  -1：永不过期  -2：过期
ttl [key]

# 设置过期时间
expire [key] [time]
```



## 服务器信息

Redis INFO 命令以一种易于理解和阅读的格式，返回关于 Redis 服务器的各种信息和统计数值。

```
server
Clients
Memory
Persistence
Stats
Replication
CPU
Modules	
Cluster
Keyspace
```



## 服务器命令

```shell
INFO  # 返回关于Redis服务器的各种信息和统计数值。

BGREWRITEAOF  # 异步执行一个AOF文件重写操作

CLIENT LIST  # 获取连接到服务器的客户端连接列表
CLIENT SETNAME connection-name  # 设置当前连接的名称
CLIENT GETNAME  # 获取连接的名称

TIME  # 返回服务器当前时间
DBSIZE  # 返回当前数据库的key的数量

MONITOR  # 实时打印出Redis服务器接收到的命令，调试用
DEBUG SEGFAULT  # 让Redis服务崩溃

CONFIG REWRITE  # 对启动 Redis 服务器时所指定的 redis.conf 配置文件进行改写
CONFIG SET [parameter] [value]  # 修改 redis 配置参数，无需重启

FLUSHDB  # 删除当前数据库的所有key
FLUSHALL  # 删除所有数据库的所有key

ROLE  # 返回主从实例所属的角色
SLAVEOF [host] [port]  # 将当前服务器转变为指定服务器的从属服务器(slave server)

SCAN [cursor] MATCH [pattern]  # 从海量的key中找出满足特定前缀的key列表

CLUSTER MEET  # 将当前节点加入另一个节点所在的集群
CLUSTER ADDSLOTS <slot> [slot ... ]  # 将槽指派给节点
CLUSTER INFO  # 查看集群信息
```



# 数据结构⛩

Redis 的数据结构是实现五种基本数据类型的底层结构



## 简单动态字符串

```c
struct sdshdr {
  	// 记录 buf 数组中已使用字节的长度
		int len;
  	// 记录 buf 数组中未使用字节的长度
  	int free;
  	// 字节数组，用于保存字符串
  	char buf[];
}
```



## 链表

```c
typedef struct listNode {
  
		struct listNode *prev;
		
		struct listNode *next;
		
		void *value;
}listNode;
```



## 字典



## 跳跃表

跳跃表（skiplist）在每个节点中维持多个指向其他节点的指针，从而达到快速访问节点的目的

查找效率： 平均 $O(logn)$ 、最坏 $O(n)$​

Redis 的跳跃表表由 `zskiplistNode` 和 `skiplist` 两个结构定义,其中 `zskiplistNode` 结构用于表示跳跃表节点，而 `zskiplist` 结构则用于保存跳跃表节点的相关信息，比如节点的数量、指向表头节点和表尾节点的指针等。



## 整数集合



## 压缩列表



## 对象



# 数据类型⛩

## 字符串类型 string

string 是 redis 最基本的类型，是二进制安全的。

二进制安全：在传输数据的时候，能保证二进制数据的信息安全，也就是不会被篡改、破译，如果被攻击，能够及时检测出来。 

redis 中字符串大小最多可以是 512 M

```zsh
# 存储
set key value 
setrange key 0 xxx    # 从第0位开始设置为xxx
setex key time value  # 设置key同时设置过期时间
setnx  key value      # 如果不存在则设置 set if not exist
mset k1 v1 k2 v2      # 同时设置多个
msetnx k1 v1 k2 v2    # 同时设置多个，如果有已经存在的key则全部设置失败

# 获取
get key
getrange key 0 -1  # 获取整个字符串
getrange key 0 3   # 获取字符串的[0, 3]
mget k1 k2 k3      # 同时获取多个

# 删除
del key

# 对key的值追加value
append key value

# 返回长度
strlen

# 加减乘除 
incr / decr / incrby / decrby
```



应用场景：

1. 保存单个字符串或 json 字符串数据 

2. 因为 string 是二进制安全的，所以可以把保密要求高的图片文件内容作为字符串来存储 

3. 作为计数器：常规 key-value缓存应用，如微博数、粉丝数。INCR 本身就具有原子性，不会有线程安全问题



## 哈希类型 hash

redis hash 是一个键值对集合。

Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。

```zsh
# 存储
HSET key field value
HMSET key field1 value1 field2 value2 ...
HSETNX key field value  # set if not exist

# 获取
HGET key field
HMGET key field1 field2 ...
HGETALL key
HLEN key     # 获取key的field长度
HKEYS key    # 获取key的所有field
HVALUES key  # 获取key中所有field的value

# 删除
DEL key

# 判断
HEXISTS key field  # 判断key中是否有field

# 算数
HINCRBY key field n  # 对key的field字段加n
HINCRBYFLOAT key field n  # 对key的field字段加n (浮点数操作)
```



应用场景：

1. 通常用来存储一个用户信息的对象数据。

   - 相比于存储对象的 string 类型的 json ，json修改单个属性需要将整个值取出来，而 hash 不需要。
   - 相比于多个 key-value 存储对象，hash 节省了很多内存空间

   - 如果 hash 的属性值被删除完，那么 hash 的 key 也会被 redis 删除



## 列表类型 list

Redis 列表是简单的字符串列表，按照插入顺序排序。可以添加一个元素导列表的头部（左边）或者尾部（右边）。它的底层实际是个链表。

```zsh
# 存储  这里可以理解为双端队列
LPUSH key1 1 2 3 4 5  # 从左向右添加元素
RPUSH key2 1 2 3 4 5  # 从右向左添加元素
RPOPLPUSH from to  # 从from右弹出一个元素并从左压入to中
LINSERT key AFTER/BEFORE value # 在key前/后插入value

# 获取
LINDEX key index  # 根据单个索引获取
LRANGE key start end  # 根据范围索引获取
LLEN key  # 获取长度

# 删除
LPOP key  # 从左边出队
RPOP key  # 从右边出队
LREM key num value  # 从key中删除num个value
LTRIM key idx1 idx2  # 截取 idx0 ~ idx1 的值并赋值给key
```



应用场景：

1. 对大量数据的集合数据删减：列表显示、关注列表、粉丝列表、留言评价、分页、热点新闻等

2. 任务队列：list 通常用来实现一个消息队列，而且可以确保先后顺序，不必像 MySQL 那样通过 `ORDER BY` 来排序



## 无序集合 set

Redis 的 set 是 string 类型的无序集合。它是通过 HashTable 实现实现的，set不允许重复元素。

```zsh
# 存储
SADD key value  # 向集合中添加元素
SMOVE key1 key2 value  # 将key1中的某个值移动到key2中

# 获取
SMEMBERS key  # 获取集合中的所有元素
SCARD key  # 获取集合中的元素个数
SRANDMEMBER key n # 从集合中随机抽取n个数

# 删除
SREM key value  # 删除集合中的某个元素
SPOP key  # 随机出栈

# 判断
SISMEMBER key value  # 判断value是否在key中

# 集合操作
SDIFF key1 key2   # 取key1和key2的差集
SINTER key1 key2  # 取key1和key2的交集
SUNION key1 key2  # 取key1和key2的并集
```



应用场景：

对两个集合间的数据进行交集、并集、差集运算 

1. 以非常方便的实现如共同关注、共同喜好、共同好友等功能。对上面的所有集合操作，还可以使用不同的命令选择将结果返回给客户端还是存储到一个新的集合中。

2. 利用唯一性，可以统计访问网站的所有独立 IP



## 有序集合 sortedset

Redis 的 sortedset 和 set 一样也是 string 类型元素的集合，且不允许重复的成员。

不同的是每个元素都会关联一个 double 类型的分数。

Redis 正是通过分数来为集合中的成员进行从小到大的排序。zSet 的成员是唯一的，但score可以重复。

```zsh
# 存储
ZADD key score value # 向key中添加元素

# 获取
ZRANGE key start end [withscores]       # 获取key中的元素
ZREVRANGE key start end [withscores]    # 逆序获取key中的元素
ZRANGEBYSCORE key [(]start [(]end   [LIMIT]  # 从低到高根据分数获取  如果添加了"(" 表示不包含
ZRANGEBYSCORE key [(]end   [(]start [LIMIT]  # 从高到低根据分数获取
ZCARD key 						 # 获取key中元素个数
ZCOUNT key start end   # 获取key中分数范围在 start ~ end 的value
ZRANK key value  			 # 获取value在key中的score排名
ZSCORE key value  		 # 获取value在key中的score
ZREVRANK key value     # 逆序获取下标的值

# 删除
ZREM key value  # 删除key中的某个元素
```



应用场景：

常用于排行榜

 1、如推特可以以发表时间作为 score 来存储 

2、存储成绩 

3、还可以用 zset 来做带权重的队列，让重要的任务先执行



# 使用场景⛩

## 计数器

可以对 String 进行自增自减运算，从而实现计数器功能。

Redis 的数学运算命令例如 `INCR` 具有原子性，同时作为内存型数据库的读写性能非常高，很适合存储频繁读写的计数量。



## 显示最新的项目列表

下面这个语句常用来显示最新项目，随着数据多了，查询毫无疑问会越来越慢。

```sql
SELECT * FROM foo WHERE ... ORDER BY time DESC LIMIT 10 
```

在 web 应用中，“列出最新的回复” 之类的查询非常普遍，这通常会带来可扩展性问题。

例如，一个 web 应用想要列出用户贴出的最新 20 条评论，在最新的评论边上有一个 "显示全部" 的链接，点击后就可以获得更多的评论。

假设数据库中的每条评论都有一个唯一的递增的 id 字段。每次新评论发表时，将评论的 id 添加到 redis 的列表

```
LPUSH latest.comments <ID>   
```

如果只需要保存最新的5000条评论，可以将列表裁剪为指定长度：

```
LTRIM latest.comments 0 5000 
```

每次我们需要获取最新评论的项目范围时，我们调用一个函数来完成，伪代码如下：

```python
def get_latest_comments(start, num_items):  
    id_list = redis.lrange("latest.comments",start,start+num_items - 1)  
    if id_list.length < num_items  
        id_list = SQL_DB("SELECT ... ORDER BY time LIMIT ...")  
    return id_list  
```

在 Redis 中评论的最新 id 使用了常驻缓存，这是一直更新的。但是这里做了限制不能超过 5000 个，因此获取最新评论的函数会检查 start/count 参数是否超出了这个范围，只有超出了范围才需要去访问数据库。



## 排行榜

各种数据库的数据如果并非存储在内存中，那么在按得分排序以及实时更新这些几乎每秒钟都需要更新的功能上的性能则不够理想。

例如有如下需求：

- 列出前 100 名高分选手

-  列出某用户当前的全球排名

如果有几百万个用户，那么每分钟都会有几百万个新的得分，

```
ZADD leaderboard  <score> <username or userid> 
```

得到前100名高分用户：

```
ZREVRANGE leaderboard 0 99
```

 用户的全球排名：

```
ZRANK leaderboard <username or userid>
```



## 查找表

例如 DNS 记录就很适合使用 Redis 进行存储。

查找表和缓存类似，也是利用了 Redis 快速的查找特性。但是查找表的内容不能失效，而缓存的内容可以失效，因为缓存不作为可靠的数据来源。



## 消息队列

List是一个双向链表，可以通过 lpush 和 rpop 写入和读取消息

不过最好使用 Kafka、RabbitMQ 等消息中间件。



## 会话缓存

可以使用 Redis 来统一存储多台应用服务器的会话信息。

当应用服务器不再存储用户的会话信息，也就不再具有状态，一个用户可以请求任意一个应用服务器，从而更容易实现高可用性以及可伸缩性。



## 分布式锁实现

在分布式场景下，无法使用单机环境下的锁来对多个节点上的进程进行同步。

可以使用 Redis 自带的 SETNX 命令实现分布式锁，除此之外，还可以使用官方提供的 RedLock 分布式锁实现。



## 其它

Set 可以实现交集、并集等操作，从而实现共同好友等功能。

ZSet 可以实现有序性操作，从而实现排行榜等功能。



# 数据淘汰策略⛩

## 过期删除策略

Redis 的过期策略，有定期删除和惰性删除两种：

- 定期删除：默认每 100s 就随机抽一些设置了过期时间的 key，去检查是否过期，过期了就删除
- 惰性删除：当 key 被查询的时候查看有没有过期，过期就删除同时不返回数据，没过期就返回数据。

对于一些过期数据，如果没有被定期删除，同时也没有被查询，会根据内存淘汰策略删除



## 内存淘汰策略

可以设置内存最大使用量，当内存使用量超出时，会施行数据淘汰策略。

Redis 具体有 6 种淘汰策略：

| 策略            | 描述                                                 |
| --------------- | ---------------------------------------------------- |
| volatile-lru    | 从已设置过期时间的数据集中挑选最近最少使用的数据淘汰 |
| volatile-ttl    | 从已设置过期时间的数据集中挑选将要过期的数据淘汰     |
| volatile-random | 从已设置过期时间的数据集中任意选择数据淘汰           |
| allkeys-lru     | 从所有数据集中挑选最近最少使用的数据淘汰             |
| allkeys-random  | 从所有数据集中任意选择数据进行淘汰                   |
| noeviction      | 禁止驱逐数据                                         |

作为内存数据库，出于对性能和内存消耗的考虑，Redis 的淘汰算法实际实现上并非针对所有 key，而是抽样一小部分并且从中选出被淘汰的 key。

使用 Redis 缓存数据时，为了提高缓存命中率，需要保证缓存数据都是热点数据。可以将内存最大使用量设置为热点数据占用的内存量，然后启用 allkeys-lru 淘汰策略，将最近最少使用的数据淘汰。





# 持久化⛩

Redis 是一个内存数据库，当 Redis 服务器重启，获取电脑重启，数据会丢失，我们可以将 Redis 内存中的数据持久化保存到硬盘的文件中。



## RDB

RDB（Redis DataBase）将某个时间点的数据快照写入磁盘中。

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件（dump.rdb），待持久化过程都结東了，再用这个临时文件替换上次持久化的文件。整个过程中，主进程是不进行任何 I/O 操作的，这就确保了极高的性能。

> Fork 的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等）数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程。

优点：

- RDB 适合作冷备份，每个数据文件分别都代表了某一时刻 Redis 里面的数据

- 如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那 RDB 方式要比 AOF 方式更加的高效。

缺点：

- 如果系统发生故障，将会丢失最后一次创建快照之后的数据。
- Fork 的时候，内存中的数据被复制，会产生两倍的内存膨胀。



```
SAVE： 立刻保存RDB快照，会阻塞Redis服务器进程，直到快照创建完成。

BGSAVE: Redis会在后台异步执行快照保存操作，同时还可以响应客户端请求。

LASTSAVE：获取最后一次成功执行快照的时间
```



## AOF

AOF（Append Only File）以日志的方式记录每个写操作，可以在每一次命令操作后，持久化数据

> RDB 和 AOF 可以共存，但 Redis 会优先加载 AOF

使用 AOF 持久化需要设置同步选项，从而确保写命令同步到磁盘文件上的时机。这是因为对文件进行写入并不会马上将内容同步到磁盘上，而是先存储到缓冲区，然后由操作系统决定什么时候同步到磁盘。

```zsh
appendonly no（关闭aof） --> appendonly yes （开启aof）

appendfsync always  # 每一次操作都进行持久化，会严重减低服务器的性能
appendfsync everysec  # 每隔一秒进行一次持久化，选项比较合适，可以保证系统崩溃时只会丢失一秒左右的数据
appendfsync no	# 让操作系统来决定何时同步
```

优点：

- AOF 适合作热备份，最多只会丢失一秒的数据。

- AOF 在对日志文件进行操作的时候是以 `append-only` 的方式，只是追加的方式写数据，自然少了很多磁盘寻址的开销，写入性能惊人。



缺点：

- 相同数据集的数据而言，AOF 文件要远大于 RDB 文件，恢复速度慢于 RDB
- AOF 运行效率要慢于RDB



## AOF重写

随着服务器写请求的增多，AOF 文件会越来越大，Redis 提供了一种将 AOF 重写的特性，能够去除 AOF 文件中的冗余写命令。

Redis 会记录上次重写时的 AOF 大小，默认配置是当 AOF 文件大小是上次重写后大小的一倍且文件大于 64MB 时触发。

AOF 文件持续增长而过大时，会 fork 出一条新进程来将文件重写，重写并不需要对原有 AOF 文件进行任何的读取、写入、分析等，而是通过读取服务器当前的数据库状态来实现的，主要思想是从数据库中读取键现在的值，然后用一条命令去记录键值对，代替之前记录该键值对的多个命令。

`BGREWRITEAOF` 可以手动触发AOF重写。



### 子进程负责重写

因为 Redis 服务器使用单线程来处理命令请求，所以如果在一个进程中对 AOF 进行重写，那么一定会造成进程的阻塞。

所以 Redis 希望将重写操作放到后台运行，于是会 fork 一条子进程来负责对 AOF 的重写，优点是：

- 子进程进行 AOF 重写期间，主进程可以继续处理命令请求
- 子进程带有主进程的数据副本，使用子进程而不是线程，可以避免在锁的情况下，保证数据的安全性。



### 数据一致性问题

子进程在进行 AOF 重写期间，服务器进程还要继续处理命令请求，而新的命令可能对现有的数据进行修改，这会让当前数据库的数据和重写后的 AOF 文件中的数据不一致。

为了解决这种数据不一致的问题，Redis 增加了一个 AOF 重写缓存，这个缓存在 fork 出子进程之后开始启用，Redis 服务器主进程在执行完写命令之后，会同时将这个写命令追加到 AOF 缓冲区和 AOF 重写缓冲区。

子进程在执行 AOF 重写时，主进程需要执行以下三个工作：

- 执行 Client 发来的命令请求；
- 将写命令追加到现有的 AOF 文件中；
- 将写命令追加到 AOF 重写缓存中。



### 完成重写之后

当子进程完成对 AOF 文件重写之后，它会向父进程发送一个完成信号，父进程接收信号后，会调用一个信号处理函数：

- 将 AOF 重写缓存中的内容全部写入到新的 AOF 文件中，此时新的 AOF 文件保存的数据状态和服务器当前的数据状态一致
- 对新的 AOF 文件进行改名，覆盖原有的 AOF 文件，完成新旧两个 AOF 文件的替换。



## 性能建议

RDB 更适合做冷备，AOF 更适合做热备

两种机制全部开启的时候，Redis 在重启的时候会默认使用 AOF 去重新构建数据，因为 AOF 的数据比 RDB 更完整。

RDB文件只用作后备用途，建议只在Slave上持久化RDB文件，只保留 `save 900 1` 这条规则

如果开启AOF，好处是在最恶劣情況下也只会丢失不超过两秒数据，启动脚本较简单只需要读取自己的 AOF 文件就可以了。代价一是带来了持续的 I/O，二是 AOF 重写的最后将数据写到新文件造成的阻塞几乎是不可避免的。只要硬盘许可，应该尽量减少 AOF 重写的频率，AOF 重写的基础大小默认值 64M 太小了，可以设到 5G 以上。默认超过原大小 100% 大小时重写可以改到适当的数值。

如果不允许 AOF，仅靠 Master-Slave Replication 实现高可用性也可以。能省掉一大笔 I/O 也减少了重写时带来的系统波动。代价是如果 Master/Slave 同时挂掉，会丢失十几分钟的数据，启动脚本也要比较 Master/Slave 中的 RDB 文件，载入较新的那个。新浪微博就选用了这种架构。



# 主从复制⛩

通过使用 `SALVEOF host port` 命令来让一个服务器成为另一个服务器的从服务器。

一个从服务器只能有一个主服务器，并且不支持主主复制。



## 复制原理

Redis 的复制功能分为同步和命令传播两个操作。

### 同步

1. 从服务器向主服务器发送 `SYNC` 命令

1. 收到 `SYNC` 命令的主服务器执行 `BESAVE` 命令创建 RDB 文件，并使用一个缓冲区记录从现在开始执行的所有写命令
2. `BESAVE` 命令执行完毕后，主服务器将 RDB 文件发送给从服务器，从服务器接收并载入这个 RDB 文件
3. 主服务器将存储在缓冲区中的所有写命令发送给从服务器，服务器执行写命令，将数据库状态更新至和主服务器一致

<img src="http://store.secretcamp.cn/uPic/image-20210328204848244202103282048481616935728yDPPRAyDPPRA.png" alt="image-20210328204848244" style="zoom:45%;" />

如果在这个过程中，发生了服务器宕机：

- Master 宕机：选择一个 Slave 提升为主服务器，继续提供服务
- Slave 宕机：直接重启，会自动加入主从架构，并自动通过增量复制完成数据同步



### 命令传播

主服务器将自己执行的写命令发送给从服务器执行，从服务器执行之后，主从会再次回到一致性状态。



### 心跳检测

在命令传播阶段，从服务器默认会以每秒一次为频率，向主服务器发送命令 `REPLCONF ACK <replication_offset>`

replication_offset 是从服务器当前的复制偏移量



### 旧版复制的缺陷

如果从服务器断线，经过自动重连后又重新连接上了主服务器，也会进行复制过程，但此时效率会非常低。

```
Master         			Slave
SET k1 v1					SET k1 v1
SET k2 v2					SET k2 v2
...								...
SET k97 v97     	SET k97 v97
主从连接断开				主从连接断开
SET k98 v98				正在重连...
SET k99 v99				正在重连...
SET k100 v100			正在重连...
主从重新连接
										SYNC
BESAVE
执行完毕并发送
									载入RDB文件
主从再次同步				主从再次同步
```

在以上的案例中，其实从服务器相对主服务器只是缺少了 k98 - k100 ，但是却需要使用 RDB 文件加载主服务器中的所有数据，这是非常浪费性能的。

Redis 2.8 之后更新了复制功能的实现，新版采用 `PSYNC` 命令，分为两 个部分：

- 完整重同步：用于处理初次复制的情况，机制和旧版相同
- 部分重同步：用于处理断线重连的情况，主服务器只发送从服务器断线期间进行的写操作，而不是 RDB 文件



## 同步策略

### 全量同步

Redis 全量复制一般发生在 Slave 初始化阶段，这时 Slave 需要将 Master 上的所有数据都复制一份。

1. 从服务器连接主服务器，发送 `SYNC` 命令； 

2.  主服务器接收到 `SYNC` 命令后，开始执行 `BGSAVE` 命令生成 RDB 文件并使用缓冲区记录此后执行的所有写命令； 

3. 主服务器 `BGSAVE` 执行完后，向所有从服务器发送快照文件，并在发送期间继续记录被执行的写命令； 

4. 从服务器收到快照文件后丢弃所有旧数据，载入收到的 RDB 快照； 

5. 主服务器快照发送完毕后开始向从服务器发送缓冲区中的写命令； 

6. 从服务器完成对快照的载入，开始接收命令请求，并执行来自主服务器缓冲区的写命令； 

> 只要 Slave 启动，就会发送 `SYNC` 请求和主机全量同步，如果多个 Slave 断线了，并同时重启，可能会导致 Master 的 I/O 剧增而宕机。



### 增量同步

Redis 增量复制是指 Slave 初始化后开始正常工作时，主服务器发生的写操作同步到从服务器的过程。 

增量复制的过程主要是主服务器每执行一个写命令就会向从服务器发送相同的写命令，从服务器接收并执行收到的写命令。





## 主从链

随着负载不断上升，主服务器可能无法很快地更新所有从服务器，或者重新连接和重新同步从服务器将导致系统超载。

为了解决这个问题，可以创建一个中间层来分担主服务器的复制工作。中间层的服务器是最上层服务器的从服务器，又是最下层服务器的主服务器，数据可以从主服务器向任意数量的从服务器上同步，从服务器可以是关联其他从服务器的主服务器。

<img src="http://store.secretcamp.cn/uPic/image-20210319105212245202103191052121616122332ZxgbR0ZxgbR0.png" alt="image-20210319105212245" style="zoom: 50%;" />





# 哨兵模式⛩

当主服务器宕机后，需要手动把一台从服务器切换为主服务器，这就需要人工干预，还会造成一段时间内服务不可用。这不是一种推荐的方式，更多的时候是采用哨兵（Sentinel）模式。

哨兵（Sentinel）是Redis 实现高可用的一个解决方案，由一个或多个哨兵组成的哨兵系统可以监视任意多个主服务器，以及主服务器属下的所有从服务器，在被监视的主服务器进入下线状态时，自动将某个从服务器升级为新的主服务器。



## 特性

哨兵 + Redis 主从的部署架构，是不会保证数据零丢失的，只能保证 Redis 集群的高可用性。

哨兵至少需要3个实例，来保证自己的健壮性，如果哨兵集群只有 2 个实例，则无法正常工作，此时 quorum = 1

```
 +----+         +----+
 | M1 |---------| R1 |
 | S1 |         | S2 |
 +----+         +----+
```

假设 Master 宕机，s1和s2中只要有一个哨兵认为 Master 宕机就可以进行切换，同时 s1 和 s2 中会选举出一个哨兵来执行故障转移，同时这个时候，需要 majority （半数+1）个哨兵同意，两个哨兵都正常运行，都可以执行故障转移。

> majority = (sentinelNum/2) + 1；



但如果 Master 和 s1 同时宕机，那么此时只剩下一个哨兵，就达不到 majority 个哨兵同意这个条件来进行故障转移。

因此，一个经典的哨兵集群如下所示：

```
         +----+
         | M1 |
         | S1 |
         +----+ 
 					 |
 					 |	
 +----+    |    +----+
 | R2 |---------| R3 |
 | S2 |         | S3 |
 +----+         +----+
```

如果只有一个哨兵，虽然可以进行故障转移，但是由于不是集群，无法保证自身健壮性，进而步法保证 Redis 的高可用。



## 工作方式

1. 每个 Sentinel 进程以每秒钟一次的频率向整个集群中的主服务器，从服务器以及其他 Sentinel 进程发送一个 `PING` 命令。
2. 如果一个实例距离最后一次有效回复 `PING` 命令的时间超过 down-after-milliseconds 选项所指定的值，则这个实例会被 Sentinel 进程标记为 “主观下线”。
3. 如果一个主服务器被标记为主观下线，则正在监视这个主服务器的所有 Sentinel 进程要以每秒一次的频率确认主服务器的确进入了主观下线状态。
4. 当有足够数量的 Sentinel 进程（大于等于配置文件指定的值quorum）在指定的时间范围内确认主服务器进入了主观下线状态，则主服务器会被标记为 “客观下线”。
5. 如果一个主服务器被认为是客观下线，那么监视这个下线服务器的各个 Sentinel 会进行协商，选举出一个领头的 Sentinel 对下线主服务器执行故障转移操作。
6. 故障转移操作主要有以下三个步骤：
   - 选出新的主服务器
   - 修改从服务器的复制目标
   - 将旧的主服务器设为从服务器



## 选举算法

对于 Slave 的选举，主要考虑以下条件：

- 是否处于在线状态

- 与 Master 断开连接的时长。

- Slave 优先级。

- 复制偏移量。

- 运行ID



领头的 Sentinel 会将所有从服务器保存到一个列表中，然后按照以下规则对列表进行过滤：

1. 删除列表中的所有处于下线或断线状态的服务器
2. 删除列表中所有最近5s内没有回复过领头 Sentinel 的 INFO 命令的服务器
3. 删除列表中所有与 Master 断开连接时长已经超过了 `down-after-milliseconds * 10`  的服务器，保证剩余服务器中保存的数据是比较新的
4. 根据优先级对列表中剩余服务器进行排序
5. 如果优先级相同，则根据从服务器的复制偏移量再进行排序
6. 最后，如果以上都相同，则选出一个运行 ID 最小的从服务器





## 哨兵原理

哨兵本质上也是一个普通的 Redis 服务器，但运行了一些哨兵的专用代码

一个哨兵进程的启动过程如下：

- 初始化服务器
- 将普通 Redis 服务器使用的代码替换成 Sentinel 专用代码
- 初始化 Sentinel 状态，根据配置文件初始化 Sentinel 监视的主服务器列表
- 创建连向主服务器的网络连接



### 初始化服务器

哨兵在初始化时不会载入 RDB 或 AOF 文件，因为它不需要使用数据库

哨兵使用 sentinelcmds 作为服务器的命令表，这与 Redis 服务器的命令表有所不同，所以哨兵无法使用 SET 之类的命令



### 获取服务器信息

创建哨兵的最后一步是创建连向被监视主服务器的异步网络连接，分别是命令连接和订阅连接。

- 命令连接：专门用于向主服务器发送命令，并接收命令回复
- 订阅连接：专门用于订阅主服务器的 `__sentinel__:hello` 频道

哨兵默认以每十秒一次的频率，向主服务器发送 INFO 命令，通过分析 INFO 命令可以获知主服务器的状态。

通过分析 INFO 命令的返回值，来更新 slaves 字典，其中保存了从服务器的实例结构、

之后，哨兵也会创建到从服务器的命令连接和订阅连接，并向从服务器发送 INFO 命令并分析返回值。



### 哨兵之间的自动发现机制

哨兵互相之间的发现，是通过 Redis 的 「发布/订阅」 系统实现的，

哨兵会以两秒作为频率，向自己监控的所有服务器的 `__sentinel__:hello` 频道里发送一条命令

`PUBLISH __sentinel__:hello "<s_ip>, <s_port>, <s_runid>, <s_epoch>, <m_name>, <m_ip>, <m_port, <m_epoch>"`

以 `s_` 为开头的记录了哨兵的信息，以 `m_` 为开头的记录了主服务器的信息

哨兵订阅了 `__sentinel__:hello` 频道，可以从频道中接收消息，这些消息会被哨兵用于更新对于其他哨兵的认知，根据接收到的信息来更新自己的 sentinels 字典，这个字典保存了出当前哨兵本身之外，所有同样监视这个主服务器的其他哨兵的资料。

当哨兵通过频道信息发现了另一个哨兵，不仅会为新哨兵在 sentinels 字典中创建实例结构，还会创建连向这个哨兵的命令连接，最终监视同一主服务器的哨兵会形成相互连接的网络。

> 哨兵之间不会创建订阅连接，因为哨兵通过订阅服务来实现哨兵之间的发现，所以才需要订阅连接。哨兵之间只需要使用命令连接来进行通信就足够了。





# 集群⛩

Redis-Cluster 是 Redis 提供的分布式数据库方案，集群通过分片来进行数据共享，并提供复制和故障转移功能。

Redis 最开始使用主从模式做集群，但 Master 宕机需要手动配置 Slave 转为 Master，后来为了高可用提出来 Sentinel 模式，但它也有一个问题，就是不能动态扩充，所以在 Redis 3.x 提出了 Cluster 集群模式。

Redis-Cluster 采用无中心结构，每个节点保存数据和整个集群状态，每个节点都和其他所有节点连接。



## 节点

一个 Redis-Cluster 通常由多个节点组成，最初的时候节点都是相互独立的，要组建一个真正可工作的集群，必须将各个独立的节点连接起来，构成一个包含多个节点的集群。

```
CLUSTER MEET <ip> <port>
```

假设当前有三个独立的节点 A、B、C

```
A   127.0.0.1:7000
B		127.0.0.1:7001
C		127.0.0.1:7002
```

首先使用客户端连上节点 7000，通过命令将 7001 添加到 7000

```
$ redis-cli -c -p 7000
127.0.0.1:7000> CLUSTER NODES  
127.0.0.1:7000> CLUSTER MEET 127.0.0.1 7001
```



## 槽指派

Redis-Cluster 通过分片的方式保存数据库中的键值对，集群的整个数据库被分为 16384 个槽，数据库中的每个键都属于这16384 个槽的其中一个，集群中的每个节点都可以处理 0 个到 16384 个槽。

当数据库中的 16384 个槽都有节点在处理时，集群处于上线状态，相反，只要有任意一个槽没有被处理，集群处于下线状态。

以下命令将所有的的槽指派给三个节点负责

```
127.0.0.1:7000> CLUSTER ADDSLOTS 0 1 2 3 ... 5000
127.0.0.1:7001> CLUSTER ADDSLOTS 5001 5002 ... 10000
127.0.0.1:7002> CLUSTER ADDSLOTS 10001 10002 ... 16383
```





## 主从模式

Redis-Cluster 为了保证数据的高可用性，加入了主从模式，主从模式可以保证集群中的某一个节点群的高可用。

一个主节点对应一个或多个从节点，主节点提供数据存取，从节点则是从主节点拉取数据备份，当这个主节点挂掉后，就会在从节点选取一个来作为主节点，从而保证集群不会挂掉。





# 事务⛩

一个事务包含了多个命令，服务器在执行事务期间，不会改去执行其它客户端的命令请求。

事务中的多个命令被一次性发送给服务器，而不是一条一条发送，这种方式被称为流水线，它可以减少客户端与服务器之间的网络通信次数从而提升性能。

Redis 最简单的事务实现方式是使用 `MULTI` 和 `EXEC` 命令将事务操作包围起来。



## 事务相关命令

```sh
DISCARD    # 放弃事务
EXEC			 # 执行事务块内的命令
MULTI			 # 标记事务开始
UNWATCH		 # 取消WATCH命令对所有key的监视
WATCH key...  # 监视一个或多个key，如果事务执行之前这些key被修改，则事务将被中止
```

被 `WATCH` 的键会被监视，并会发觉这些键是否被改动过了。 如果有至少一个被监视的键在 EXEC 执行之前被修改了， 那么整个事务都会被取消， EXEC 返回空多条批量回复（null multi-bulk reply）来表示事务已经失败。

`WATCH` 命令可以被调用多次。 对键的监视从 `WATCH` 执行之后开始生效， 直到调用 `EXEC` 为止。

使用无参数的 `UNWATCH` 命令可以手动取消对所有键的监视。



## 事务中的错误

使用事务时可能会遇上以下两种错误：

- 事务在执行 `EXEC` 之前，入队的命令可能会出错。比如说，命令可能会产生语法错误（参数数量错误、参数名错误），或者其他更严重的错误，比如内存不足（如果服务器使用 `maxmemory` 设置了最大内存限制的话），类似于java的编译异常。
- 命令可能在 `EXEC` 调用之后失败。事务中的命令可能处理了错误类型的键，比如将列表命令用在了字符串键上面，类似于java的运行时异常。



## Redis不支持回滚

Redis不支持回滚，这种方式有以下优点：

- Redis命令只会因为错误的语法而失败（并且这些问题不能在入队时发现），或是命令用在了错误类型的键上面，这也就是说，从实用性的角度来说，失败的命令是由编程错误造成的，而这些错误应该在开发的过程中被发现，而不应该出现在生产环境中。
- 因为不需要对回滚进行支持，所以Redis的内部可以保持简单且快速。



## check-and-set

`WATCH` 命令可以为 Redis 事务提供 check-and-set （CAS）行为。

```
WATCH mykey

val = GET mykey
val = val + 1

MULTI
SET mykey $val
EXEC
```

使用上面的代码， 如果在 `WATCH` 执行之后， `EXEC` 执行之前， 有其他客户端修改了 mykey 的值， 那么当前客户端的事务就会失败。程序需要做的，就是不断重试这个操作，直到没有发生碰撞为止。

这种形式的锁被称作乐观锁，它是一种非常强大的锁机制。 并且因为大多数情况下，不同的客户端会访问不同的键，碰撞的情况一般都很少，所以通常并不需要进行重试。



# 事件⛩

Redis 服务器是一个事件驱动程序。

## 文件事件

服务器通过套接字与客户端或者其它服务器进行通信，文件事件就是对套接字操作的抽象。

Redis 基于 Reactor 模式开发了自己的网络事件处理器，使用 I/O 多路复用程序来同时监听多个套接字，并将到达的事件传送给文件事件分派器，分派器会根据套接字产生的事件类型调用相应的事件处理器。

<img src="http://store.secretcamp.cn/uPic/image-20210531104714924202105311047151622429235PqApy3PqApy3.png" alt="image-20210531104714924" style="zoom:50%;" />

## 时间事件

服务器有一些操作需要在给定的时间点执行，时间事件是对这类定时操作的抽象。

时间事件又分为：

- 定时事件：是让一段程序在指定的时间之内执行一次；
- 周期性事件：是让一段程序每隔指定时间就执行一次。

Redis 将所有时间事件都放在一个无序链表中，通过遍历整个链表查找出已到达的时间事件，并调用相应的事件处理器。



## 事件的调度与执行

服务器需要不断监听文件事件的套接字才能得到待处理的文件事件，但是不能一直监听，否则时间事件无法在规定的时间内执行，因此监听时间应该根据距离现在最近的时间事件来决定。

事件调度与执行由 aeProcessEvents 函数负责，伪代码如下：

```python
def aeProcessEvents():
    # 获取到达时间离当前时间最接近的时间事件
    time_event = aeSearchNearestTimer()
    # 计算最接近的时间事件距离到达还有多少毫秒
    remaind_ms = time_event.when - unix_ts_now()
    # 如果事件已到达，那么 remaind_ms 的值可能为负数，将它设为 0
    if remaind_ms < 0:
        remaind_ms = 0
    # 根据 remaind_ms 的值，创建 timeval
    timeval = create_timeval_with_ms(remaind_ms)
    # 阻塞并等待文件事件产生，最大阻塞时间由传入的 timeval 决定
    aeApiPoll(timeval)
    # 处理所有已产生的文件事件
    procesFileEvents()
    # 处理所有已到达的时间事件
    processTimeEvents()
```



将 aeProcessEvents 函数置于一个循环里面，加上初始化和清理函数，就构成了 Redis 服务器的主函数，伪代码如下：

```python
def main():
    # 初始化服务器
    init_server()
    # 一直处理事件，直到服务器关闭为止
    while server_is_not_shutdown():
        aeProcessEvents()
    # 服务器关闭，执行清理操作
    clean_server()
```

从事件处理的角度来看，服务器运行流程如下：

<img src="http://store.secretcamp.cn/uPic/image-20210531104855112202105311048551622429335QaFslwQaFslw.png" alt="image-20210531104855112" style="zoom: 33%;" />



# Redis配置文件⛩

## INCLUDES

包含一个或多个配置文件，通常 include 引入包含的配置，不能被"CONFIG REWRITE"重写。

```
include /path/to/local.conf
include /path/to/other.conf
```



## MODULES

用于启动时加载模块。如果服务器无法加载模块它将中止。可以使用多个loadmodule指令。

```
loadmodule /path/to/my_module.so
loadmodule /path/to/other_module.so
```



## NETWORK

```
- protected-mode yes  
  如果是no，外部网络无法访问当前redis

- tcp-backlog 511
	确定了TCP连接中已完成队列(完成三次握手之后)的长度

- port 6379
	端口

- timeout 0
	关闭客户端连接，0代表不关闭

- tcp-keepalive 300
	每隔一段时间进行检测 是否活跃  0代表不检测

- unixsocket
	指定 unix socket 的路径
```



## GENERAL

```
- daemonize no
默认情况下，Redis不作为守护进程运行

- supervised no
	可以通过upstart和systemd管理Redis守护进程，这个参数是和具体的操作系统相关的。
	
- loglevel notice
	日志记录等级，有4个可选值
	1. debug（开发者）
	2. verbose（默认值）
	3. notice（生产）
	4. warning（警告）

- logfile ""
	日志文件的位置，当指定为空字符串时，为标准输出。
	如果Redis以守护进程模式运行，那么日志将会输出到 /dev/null，若指定了路径，日志将会输出到指定文件

- syslog-enabled no
	是否把日志记录到系统日志

- syslog-ident redis
	指定syslog里的日志标识

- syslog-facility local0
	指定syslog设备（facility），必须是user或则local0到local7
	
- databases 16
	可用数据库数量

- always-show-logo yes
	Redis显示一个ASCII艺术徽标
```



## SNAPSHOTTING

```
- save 900 1
- save 300 10
- save 60 10000
  多少秒保存数据到磁盘，格式是：save <seconds> <changes>
  意思是至少有changes条key数据被改变时，seconds秒保存到磁盘。

- stop-writes-on-bgsave-error
	默认情况下，如果 redis 最后一次的后台保存失败，redis 将停止接受写操作，这样以一种强硬的方式让用户知道数据不能正确的持久化到磁盘， 否则就会没人注意到灾难的发生。 如果后台保存进程重新启动工作了，redis 也将自动的允许写操作。

- rdbcompression yes
	dump.rdb数据库是否可以压缩数据对象
	如果你想节约一些cpu资源的话，可以把它设置为no，这样的话数据集就可能会比较大。

- dbfilename dump.rdb
	本地数据库文件名，默认值为dump.rdb

- rdb-del-sync-files no
	在没有持久性的情况下删除复制中使用的RDB文件
	
- dir ./
	本地数据库存放路径
```





## REPLICATION

```
- replicaof <masterip> <masterport>
	当本机为从服务时，设置主服务的IP及端口
	
- masterauth <master-password>
	当本机为从服务时，设置主服务的连接密码
	
- masteruser <username>
	当本机为从服务时，设置主服务的用户名。
	
- slave-serve-stale-data yes
	当slave失去与master的连接，或正在拷贝中，如果为yes，slave会响应客户端的请求，数据可能不同步甚至没有数据，
	如果为no，slave会返回错误 "SYNC with master in progress"
	
- replica-read-only yes
	如果为yes，slave实例只读，如果为no，slave实例可读可写。
	
- repl-diskless-sync no
	
```



## SECURITY

```
- acllog-max-len 128
	ACL日志存储在内存中并消耗内存，设置此项可以设置最大值来回收内存。

- requirepass foobared
	设置Redis连接密码

- rename-command CONFIG " "
	将命令重命名，为了安全考虑，可以将某些重要的、危险的命令重命名。
	当某个命令被重命名成空字符串的时候就等于取消了这个命令。
```



## CLIENTS

```
- maxclients 10000
	客户端最大连接数
```



## MEMORY MANAGEMENT

```
- maxmemory <bytes> 
	最大内存

- maxmemory-policy
	Redis达到最大内存时将如何选择要删除的内容，有以下选项：
  1.volatile-lru：利用LRU算法移除设置过过期时间的key
  2.allkeys-lru：利用LRU算法移除任何key
  3.volatile-random：移除设置过过期时间的随机key
  4.allkeys-random：移除随机key
  5.volatile-ttl：移除即将过期的key（minor TTL）
  6.noeviction：不移除任何key，只是返回一个写错误，默认选项
  
- maxmemory-samples
	LRU和minimal TTL 算法的样本
	
- replica-ignore-maxmemory yes
	默认情况下，replica节点会忽略 maxmemory 设置（除非在发生failover后，此节点被提升为master节点）
	这个行为保证了master和replicas的一致性
	
```



## APPEND ONLY MODE

```
- appendonly
	是否启用aof持久化方式 。即是否在每次更新操作后进行日志记录

- appendfilename
	更新日志文件名，默认为appendonly.aof
	
- appendfsync
	aof文件刷新的频率，有三种：
  1. no 依靠os进行刷新，redis不主动刷新AOF，这样最快，但安全性就差。
  2. always 每提交一个修改命令都调用fsync刷新到AOF文件，非常非常慢，但也非常安全。
  3. everysec 每秒钟都调用fsync刷新到AOF文件，很快，但可能会丢失一秒以内的数据。
  
- no-appendfsync-on-rewrite no
	指定是否可以在后台aof文件rewrite期间调用fsync，默认为no，表示要调用fsync
	Redis在后台写RDB文件或重写AOF文件期间会存在大量磁盘IO，此时，在某些linux系统中，调用fsync可能会阻塞。
	
- auto-aof-rewrite-percentage 100
	aof文件增长比例，指当前aof文件比上次重写的增长比例大小，默认为100，即增长到原始文件的两倍
	
- auto-aof-rewrite-min-size 64mb
	aof文件重写最小的文件大小，即最开始aof文件必须要达到这个文件时才触发，后面的每次重写就不会根据这个变量了
	
- aof-load-truncated yes
	指redis在恢复时，会忽略最后一条可能存在问题的指令。
	yes：即在aof写入时，可能存在指令写错的问题（突然断电），这种情况下，yes会log并继续  no：直接恢复失败。

- aof-use-rdb-preamble yes
	在开启了这个功能之后，AOF重写产生的文件将同时包含RDB格式的内容和AOF格式的内容
	RDB格式的内容用于记录已有的数据，而AOF格式的内存则用于记录最近发生了变化的数据
	这样Redis就可以同时兼有RDB持久化和AOF持久化的优点		
```



## REDIS CLUSTER



# Redis客户端⛩

## Jedis

Jedis 是 Redis 的 Java 实现客户端，提供了比较全面的 Redis 命令的支持。



## Redisson

Redisson 实现了分布式和可扩展的 Java 数据结构。



## Lettuce

Lettuce 是高级 Redis 客户端，用于线程安全同步，异步和响应使用，支持集群，Sentinel，管道和编码器。

