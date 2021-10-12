# ZooKeeper👨‍🌾

ZooKeeper 是一个开源的分布式协调服务，也是一个典型的分布式数据一致性的解决方案，分布式应用程序可以基于它实现诸如数据发布/订阅、负载均衡、分布式协调/通知、集群管理、Master选举、分布式锁、分布式队列等功能。



## 参考文档

👉  [ZooKeeper 官方文档](https://zookeeper.apache.org/doc/r3.7.0/index.html)



## 应用场景

### 维护配置信息

Java 编程经常会遇到配置项，例如数据库的 url、schema、user 和 password 等。通常这些配置项我们会放置在配置文件中，再将配置文件放置在服务器上。当需要更改配置项时，需要去服务器上修改对应的配置文件。

但是随着分布式系统的兴起，由于许多服务都需要使用到该配置文件，因此有必须保证该配置服务的高可用性和各台服务器上配置数据的一致性。通常会将配置文件部署在一个集群上，然而一个集群动辄上千台服务器，此时如果一台台服务器逐个修改配置文件那将是非常繁琐且危险的的操作，因此就需要一种服务，能够高效快速且可靠地完成配置项的更改等操作，并能够保证各配置项在每台服务器上的数据一致性。



### 实现分布式锁

一个集群是一个分布式系统，由多台服务器组成。为了提高并发度和可靠性，多台服务器上运行着同一种服务。当多个服务在运行时就需要协调各服务的进度，有时需要保证当某个服务在进行某个操作时，其他的服务都不能进行该操作，即对该操作进行加锁。



### 集群管理

一个集群有时会因为软硬件故障、网络故障，出现某些服务器挂掉而被移除集群，同时让某些服务器加入到集群中的情况，Zookeeper 会将这些服务器加入/移出的情况通知给集群中的其他正常工作的服务器，以及时调整存储和计算等任务的分配和执行。此外 Zookeeper 还会对故障的服务器做出诊断并尝试修复。



### 生成分布式唯一ID

在过去的单库单表型系统中，通常可以使用数据库字段自带的 auto_increment 属性来自动为每条记录生成一个唯一的 id。但是分库分表后，就无法在依靠数据库的 auto_increment 属性来唯一标识一条记录了。此时就可以用 Zookeeper 在分布式环境下生成全局唯一 id 。



## 安装&启动

配置JDK环境 

```shell
tar -xzvf jdk-8u131-linux-x64.tar.gz
vim ~/.bashrc

// 文件中加入如下内容
JAVA_HOME=/home/parallels/Applications/jdk1.8.0_131 
export JAVA_HOME

PATH=$JAVA_HOME/bin:$PATH 
export PATH
```



配置 Zookeeper 

```shell
tar -xzvf zookeeper-3.4.10.tar.gz

// 复制配置文件并编辑
cp zoo_sample.cfg zoo.cfg

// 创建data目录，此路径用于存储zookeeper中数据的内存快照、及事物日志
mkdir data


vim zoo.cfg
dataDir=/home/parallels/Applications/zookeeper/zookeeper-3.4.10/data
```



启动 Zookeeper

```sh
zkServer.sh start  
zkServer.sh stop
zkServer.sh status
```



## docker

启动 Zookeeper 的 docker 镜像，会使用 2181（client）、2888（follower）、3888（election）、8080（adminServer）四个端口：

```dockerfile
docker run --name my-zookeeper -p 2181:2181 -d zookeeper
```

```dockerfile
docker run -d -p 2181:2181 -v /mysoft/zookeeper/data/:/data/ --name my-zookeeper --privileged zookeeper
```

```dockerfile
# 指定配置文件启动
docker run --name some-zookeeper --restart always -d -v $(pwd)/zoo.cfg:/conf/zoo.cfg zookeeper
```



从另一个 docker 容器中的应用连接 Zookeeper

```dockerfile
docker run --name some-app --link my-zookeeper:zookeeper -d application-that-uses-zookeeper
```



从客户端命令行进入 Zookeeper

```dockerfile
docker run -it --rm --link my-zookeeper:zookeeper zookeeper zkCli.sh -server zookeeper
```



# 系统模型👨‍🌾

## 数据模型

Zookeeper 的数据模型和 Unix 文件系统非常相似，数据节点可以视为树状结构（或者目录），树中的各节点被称为 znode（zookeeper node），一个 znode 可以有多个子节点。

<img src="http://store.secretcamp.cn/uPic/image-20210405145815880202104051458161617605896IzwrZRIzwrZR.png" alt="image-20210405145815880" style="zoom:50%;" />

一个 znode 可以分为三个部分：

- 节点的数据
- 节点的子节点children
- 节点的状态 stat

```sh
[zk: localhost:2181(CONNECTED) 5] get /redis
hello  # 节点的数据
cZxid = 0x100000004  									 # 数据节点创建时的事务 ID
ctime = Sun Apr 04 23:41:53 PDT 2021   # 数据节点创建时的时间
mZxid = 0x100000004  									 # 数据节点最后一次更新时的事务 ID
mtime = Sun Apr 04 23:41:53 PDT 2021   # 数据节点最后一次更新时的时间
pZxid = 0x10000000e 									 # 数据节点的子节点最后一次被修改时的事务 ID
cversion = 2  	       # 子节点的更改次数
dataVersion = 0  			 # 节点数据的更改次数
aclVersion = 0         # 节点ACL的更改次数
ephemeralOwner = 0x0   # 如果节点是临时节点，则表示创建该节点的会话的SessionID；如果节点是持久节点，则该属性值为0
dataLength = 5  			 # 数据内容的长度
numChildren = 2  			 # 数据节点当前的子节点个数
```



## 节点特性

在 Zookeeper 中，数据节点的生命周期主要有以下两种：

- 持久化节点：节点在会话过期后不会被删除
- 临时节点：节点会在会话过期后被删除

节点又可以分为普通节点和顺序节点，根据不同组合，Znode 有四种形式的目录节点：

- PERSISTENT：持久节点
- EPHEMERAL：临时节点
- PERSISTENT_SEQUENTIAL：持久化顺序编号目录节点。
- EPHEMERAL_SEQUENTIAL：暂时化顺序编号目录节点。



## 版本

版本号用于保证分布式数据的原子性操作

- dataVersion：节点数据的更改次数

- cversion：子节点的更改次数
- aclVersion：节点ACL的更改次数



## ACL

acl 权限控制，使用 `scheme:id:permission` 来标识

在 Zookeeper 中，权限控制是基于每个znode节点的，需要对每个节点设置权限，子节点不会继承父节点的权限，客户端无权访问某节点，但可能可以访问它的子节点。

主要涵盖三个方面：

1. 权限模式（scheme）：授权的策略
   | 方案   | 描述                                 |
   | ------ | ------------------------------------ |
   | world  | 只有一个用户：anyone，代表登录所有人 |
   | ip     | 对客户端使用IP地址认证               |
   | auth   | 使用已添加认证的用户认证             |
   | digest | 使用 "用户名:密码" 方式认证          |



2. 授权对象（id）：授权的对象

表示给谁授予权限，授权对象ID是指，权限赋予的实体，例如：IP地址或用户





3. 权限（permission）：授予的权限

| 权限   | ACL简写 | 描述                             |
| ------ | ------- | -------------------------------- |
| create | c       | 可以创建子节点                   |
| delete | d       | 可以删除子节点（仅下一级节点）   |
| read   | r       | 可以读取节点数据及显示子节点列表 |
| write  | w       | 可以设置节点数据                 |
| admin  | a       | 可以设置节点访问控制列表权限     |





# 事件监听机制👨‍🌾

## Watcher概念

Zookeeper 通过 Watcher 机制实现数据的 发布/订阅 功能，多个订阅者可同时监听某一特定主题对象，当该主题对象的自身状态发生变化时，会实时并主动通知所有订阅者。该机制在被订阅对象发生变化时会异步通知客户端，因此客户端不必在 Watcher 注册后轮询阻塞，从而减轻了客户端压力。

Watcher 机制实际上与观察者模式类似，也可看作是一种观察者模式在分布式场景下的实现方式。

有以下特性：

- Watcher 是一次性的，一旦被触发就会移除，再次使用时需要重新注册

- Watcher 回调是顺序串行化执行的，只有回调后客户端才能看到最新的数据状态。一个 Watcher 回调逻辑不应该太多，以免影响别的 Watcher 执行

- WatchEvent 是最小的通信单元，结构上只包含通知状态、事件类型和节点路径，并不会告诉数据节点变化前后的具体内容

- Watcher 只有在当前 Session 彻底失效时才会无效，若在 Session 有效期内快速重连成功，则 Watcher 依然存在，仍可接收到通知



## Watcher架构

Watcher 实现由三个部分组成：

- Zookeeper 服务端
- Zookeeper 客户端
- 客户端的 ZKWatchManager 对象

客户端首先将 Watcher 注册到服务端，同时将 Watcher 对象保存到客户端的 WatchManager 中。当 ZooKeeper 服务端监听的数据状态发生变化时，服务端会主动通知客户端，接着客户端的 WatchManager 会触发相关 Watcher 来回调相应处理逻辑，从而完成整体的数据发布/订阅流程。

<img src="http://store.secretcamp.cn/uPic/image-20210404215921159202104042159211617544761B6nohrB6nohr.png" alt="image-20210404215921159" style="zoom: 38%;" />



## Watcher接口

`Watcher` 是一个接口，任何实现了 `Watcher` 接口的类就是一个新的 Watcher。

`Watcher` 内部包含了两个枚举类：`KeeperState` 、 `EventType` ，以及一个 `process` 方法

```java
public interface Watcher {
    void process(WatchedEvent var1);

    public interface Event {
        public static enum EventType {
					// ...
        }

        public static enum KeeperState {
					// ...
        }
    }
}

```



### 通知状态

枚举类 `KeeperState` 中相关属性

| 枚举属性      | 说明                     |
| ------------- | ------------------------ |
| SyncConnected | 客户端与服务器正常连接时 |
| Disconnected  | 客户端与服务器断开连接时 |
| Expired       | 会话session失效时        |
| AuthFailed    | 身份认证失败时           |



### 事件类型

枚举类 `EventType` 中相关属性

| 枚举属性            | 说明                                                        |
| ------------------- | ----------------------------------------------------------- |
| None                | 无                                                          |
| NodeCreated         | Watcher监听的数据节点被创建时                               |
| NodeDeleted         | Watcher监听的数据节点被删除时                               |
| NodeDataChanged     | Watcher监听的数据节点内容发生变更时（无论内容数据是否变化） |
| NodeChildrenChanged | Watcher监听的数据节点的子节点列表发生变更时                 |



### 回调方法

`process` 方法是 `Watcher` 接口中的一个回调方法，当 Zookeeper 向客户端发送一个 Watcher 事件通知时，客户端就会对相应的 `process` 方法进行回调，从而实现对事件的处理。

process 方法的参数是一个 `WatchedEvent` 对象

```java
void process(WatchedEvent var1);
```





## 捕获事件

主要有以下三种方法实现事件的监听：

- `zk.getChildren(path, watch)`
- `zk.exists(path, watch)`
- `zk.getData(path, watcher, stat)`



# 基本命令👨‍🌾

## CURD

👉 创建

```shell
create [-s] [-e] [-c] [-t ttl] path [data] [acl]
	-e 创建一个临时节点
	-s 创建一个有序节点

create path data  # 创建一个持久化节点
create [-e] path data 
create [-s] path data  
```

对于有序节点，系统会自动添加序号

```sh
create -s /a "aaa"   # /a0000000000
create -s /b "bbb"   # /b0000000001
create -s /c "ccc"   # /c0000000002
```



👉 更新

```sh
set [-s] [-v version] path data 
		-v 基于版本号，如果版本号不匹配则拒绝删除，类似于CAS

set path newData
```



👉 删除

```shell
delete [-v version] path
```



👉 查询

```sh
get [-s] [-w] path
stat [-w] path
ls path
```



## WATCH

```
# optional mode is one of [PERSISTENT, PERSISTENT_RECURSIVE] - default is PERSISTENT_RECURSIVE
addWatch [-m mode] path 
```



## ACL

```
setAcl <path> world:anyone:<acl>

setAcl <path> ip:<ip>:<acl>
```





# ZAB👨‍🌾

ZAB（ZooKeeper Atomic Broadcast，ZooKeeper 原子消息广播协议）是专门为 ZooKeeper 设计的一致性协议。

ZAB 协议有两种模式，它们分别是恢复模式（选主）和广播模式（同步）。当服务启动或者在领导者崩溃后，ZAB 就进入了恢复模式，当领导者被选举出来，且大多数 Server 完成了和 Leader 的状态同步以后，恢复模式就结束了。状态同步保证了 leader 和 Server 具有相同的系统状态。



## ZAB & Paxios

Paxos 是一种分布式一致性算法，Paxos 算法虽然通用且可靠，但效率太低。 Paxos 算法在出现竞争的情况下，其收敛速度很慢，甚至可能出现活锁的情况。

例如当有三个及三个以上的 proposer 在发送 prepare 请求后，很难有一个 proposer 收到半数以上的回复而不断地执行第一阶段的协议。因此，为了避免竞争，加快收敛的速度，在算法中引入了一个 Leader 这个角色，在正常情况下同时应该最多只能有一个参与者扮演 Leader 角色，而其它的参与者则扮演 Acceptor 的角色。



## Zxid

Zxid（Transaction id）类似于 RDBMS 中的事务 id，用于标识一次更新操作的 proposal id 。为了保证顺序性，该 zxid 必须单调递增。



## Epoch

可以理解为当前集群所处的年代或者周期，每个 Leader 就像皇帝，都有自己的年号，所以每次改朝换代，Leader 变更之后，都会在前一个年代的基础上加 1 。这样就算旧的 Leader 崩溃恢复之后，也没有人听他的了，因为 Follower 只听从当前年代的 Leader 的命令。



## 角色

Zookeeper 集群是一个基于主从复制的高可用集群，每个服务器承担如下三种角色中的一种：

### Leader

一个 Zookeeper 集群同一时间只会有一个实际工作的 Leader，它会发起并维护与各 Follwer 及 Observer 间的心跳。

所有的写操作必须要通过 Leader 完成再由 Leader 将写操作广播给其它服务器。只要有超过半数节点（不包括Observeer ）写入成功，该写请求就会被提交（类似于 2PC 协议）



### Follower

一个 Zookeeper 集群可能同时存在多个 Follower，它会响应 Leader 的心跳。Follower 可直接处理并返回客户端的读请求，同时会将写请求转发给 Leader 处理，并且负责在 Leader 处理写请求时对请求进行投票。



### Observer

角色与 Follower 类似，但是无投票权。

Zookeeper 需保证高可用和强一致性，为了支持更多的客户端，需要增加更多 Server，但是随着 Server 增多，投票阶段延迟增大，影响性能，因为 Observer 不参与投票，仅接受客户端的连接，并将写请求转发给 Leader 节点，所以加入更多 Observer 节点，可以提高伸缩性，同时不影响吞吐率。



<img src="http://store.secretcamp.cn/uPic/image-20210328085910359202103280859101616893150SKEjVsSKEjVs.png" alt="image-20210328085910359" style="zoom: 43%;" />



为了使用 Observer 角色，在任何想变成 Observer 角色的配置文件中加入如下配置：

```
peerType=observer
```

并在所有 Server 的配置文件中，配置成 Observer 的 Server 的那行配置追加 `:observer` ，例如

```
server.3=10.211.55.6:2289:3389:observer
```



## 协议四阶段

### 选举阶段

节点在一开始都处于选举阶段，只要有一个节点得到超半数节点的票数，它就可以当选为准 Leader 。只有到达广播阶段（broadcast），准 Leader 才会成为真正的 Leader 。这一阶段的目的是就是为了选出一个准 Leader ，然后进入下一个阶段。



### 发现阶段

这个一阶段的主要目的是发现当前大多数节点接收的最新提议，并且准 Leader 生成新的 epoch，让 Followers 接受，更新它们的 Accepted Epoch 。

在这个阶段，Followers 会将自己最后接受的事务的 epoch 值发送给准Leader 。当准Leader接收过半 Followers 发送的 epoch 后，会从中选出最大的然后加一，再发送给所有 Follower 。

一个 Follower 只会连接一个 Leader，如果有一个 Follower F1 认为另一个 Follower F2 是 Leader，F1 在尝试连接 F2 时会被拒绝，F1 被拒绝之后，就会进入重新选举阶段。



### 同步阶段

同步阶段主要是利用 Leader 前一阶段获得的最新事务历史，同步集群中所有的事务。

Leader 将 epoch 和事务集合发送给所有 Follower ，如果 Follower 的 epoch 等于 Leader 发送的 epoch ，则会接收事务列表，然后反馈给 Leader 表示已经接收事务。当 Leader 收到大多数 Follower 的反馈消息后，就会向所有 Follower 发送 Commit 消息，Follower 接收到 Commit 消息后就会依次提交事务列表中未处理的事务。

如果 Follower 的 epoch 不等于 Leader 发送的 epoch，则直接进行下一轮循环，因为 Follwer 发现自己还在上一轮，或者更上轮，无法参与本轮的同步。

Follower 只会接收 zxid 比自己的 lastZxid 大的提议。



### 广播阶段

到了这个阶段，Zookeeper 集群才能正式对外提供事务服务， 并且 Leader 可以进行消息广播。

当 Leader 接到新的消息请求时，会生成对应的事务，并向所有 Follower 发出提议，Follower 收到后将事务追加到事务列表，之后再反馈给 Leader ，Leader 收到大多数 Follower 的反馈后就会发送 Commit 给所有 Follower ，要求它们进行事务的提交。

如果有新的节点加入，Leader 还需要对新节点进行同步。



## 投票机制

投票机制作用于选举阶段：

每个 Server 首先给自己投票，然后用自己的选票和其他 Server 选票对比，权重大的胜出，使用权重较大的更新自身选票箱。具体选举过程如下：

1. 每个 Server 启动以后都询问其它的 Server 要投票给谁。对于其他 Server 的询问， Server 每次根据自己的状态都回复自己推荐的 Leader 的 id 和上一次处理事务的 zxid ，使用 (recod_id, zxid) 来表示，系统启动时每个 Server 都会推荐自己。
2. 收到所有 Server 回复以后，就计算出 zxid 最大的哪个 Server，并将这个 Server 相关信息设置成下一次要投票的 Server。
3. 计算这过程中获得票数最多的的 Server 为获胜者，如果获胜者的票数超过半数，则该 Server 被选为 Leader。否则，继续这个过程，直到 Leader 被选举出来
4. Leader 就会开始等待 Server 连接
5. Follower 连接 leader，将最大的 zxid 发送给 Leader
6. Leader 根据 Follower 的 Zxid 确定同步点，至此选举阶段完成。
7. 完成 Leader 同步后通知 Follower 已经成为 uptodate 状态
8. Follower 收到 uptodate 消息后，又可以重新接受 client 的请求进行服务了



示例：

目前有 5 台服务器，每台服务器均没有数据，它们的编号分别是 1 ~ 5 ，按编号依次启动，它们的选择举过程如下：

1. 服务器 1 启动，投票为 (1, 0)，然后发投票信息，由于其它机器还没有启动所以它收不到反馈信息，服务器 1 的状态一直属于 Looking 。
2. 服务器 2 启动，投票为 (2, 0)，同时与之前启动的服务器 1 交换结果，由于服务器 2 的编号大所以服务器 2 胜出，但此时投票数没有大于半数，所以两个服务器的状态依然是 Looking 。
3. 服务器 3 启动，投票为 (3, 0)，同时与之前启动的服务器 1,2 交换信息，由于服务器 3 的编号最大所以服务器 3 胜出，此时投票数正好大于半数，所以服务器 3 成为领导者，服务器 1,2 成为小弟。
4. 服务器 4 启动，投票为 (4, 0)，同时与之前启动的服务器 1,2,3 交换信息，尽管服务器 4 的编号大，但之前服务器 3 已经胜出，所以服务器 4 只能成为小弟。
5. 服务器 5 启动，后面的逻辑同服务器 4 成为小弟。





# 集群搭建👨‍🌾

## 配置文件介绍

```sh
# 基本事件单元，这个时间是作为 Zookeeper 服务器之间或客户端与服务器之间维持心跳的时间间隔
tickTime=2000

# 允许 follower 连接并同步到 Leader 的初始化连接时间，以tickTime为单位。当初始化连接时间超过该值，则表示连接失败。
initLimit=10

# 表示 leader 与 follower 之间发送消息时，请求和应答时间长度。
# 如果 follower 在设置时间内不能与 leader 通信，那么此 follower 将会被丢弃。
syncLimit=5

# 存储内存中数据库快照的位置，除非另有说明，否则指向数据库更新的事务日志。
dataDir=/home/ubuntu/opt/zookeeper-3.4.10-2183/data

# 监听客户端连接的端口
clientPort=2183

#server.A=B:C:D
#A：是一个数字，表示这个是服务器的编号
#B：服务器的ip地址
#C：Zookeeper服务器之间的通信端口
#D：Leader选举的端口 

server.1=106.75.81.25:2287:3387
server.2=106.75.81.25:2288:3388
server.3=106.75.81.25:2289:3389

# 最大客户端连接数
maxClientCnxns=60
```



## 搭建步骤

基于一台 Linux 虚拟机进行伪集群搭建，首先将 zookeeper 复制三份

```shell
cp ‐r zookeeper‐3.4.10 zookeeper2181 
cp ‐r zookeeper‐3.4.10 zookeeper2182 
cp ‐r zookeeper‐3.4.10 zookeeper2183
```



👉 修改对应配置文件

```sh
clientPort=2181 

dataDir=/home/zookeeper/zookeeper2181/data #集群配置信息

# 每个服务器对应的 ip 为 0.0.0.0 
server.1=0.0.0.0:2287:3387 
server.2=106.75.81.25:2288:3388 
server.3=106.75.81.25:2289:3389
```

```sh
clientPort=2182

dataDir=/home/zookeeper/zookeeper2182/data #集群配置信息

# 每个服务器对应的 ip 为 0.0.0.0 
server.1=106.75.81.25:2287:3387 
server.2=0.0.0.0:2288:3388 
server.3=106.75.81.25:2289:3389
```

```sh
clientPort=2183 

dataDir=/home/zookeeper/zookeeper2183/data #集群配置信息

# 每个服务器对应的 ip 为 0.0.0.0 
server.1=106.75.81.25:2287:3387 
server.2=106.75.81.25:2288:3388 
server.3=0.0.0.0:2289:3389
```



👉 创建 myid 文件

在上一步 dataDir 指定的目录下，创建 myid，然后在该文件添加上一步 server 配置的对应 A 数字。

```sh
# 这里的数字 A 与 server.A 的数字对应 

# 分别在 data 目录下执行命令 
echo "1" > myid

echo "2" > myid

echo "3" > myid
```



👉 分别启动三台服务器

```sh
./zookeeper2181/bin/zkServer.sh start
./zookeeper2182/bin/zkServer.sh start
./zookeeper2183/bin/zkServer.sh start
```



👉 检验集群状态

```sh
./zkCli.sh ‐server 10.211.55.6:2181
./zkCli.sh ‐server 10.211.55.6:2182 
./zkCli.sh ‐server 10.211.55.6:2183
```





# Java API👨‍🌾

## 连接到Zookeeper

```java
public class ZooGo {
    static CountDownLatch countDownLatch = new CountDownLatch(1);
    static ZooKeeper zk;
    static String host =  "10.211.55.6:2181,10.211.55.6:2182,10.211.55.6:2183";;
    public static void main(String[] args) throws Exception {

        zk = new ZooKeeper(host, 5000, new Watcher() {
            public void process(WatchedEvent event) {
                if (event.getState() == Event.KeeperState.SyncConnected) {
                    System.out.println("连接创建成功");
                    countDownLatch.countDown();
                } else if (event.getState() == Event.KeeperState.Disconnected) {
                    System.out.println("连接断开");
                } else if (event.getState() == Event.KeeperState.Expired) {
                    System.out.println("会话超时");
                } else if (event.getState() == Event.KeeperState.AuthFailed) {
                    System.out.println("认证失败");
                }
            }
        });
        countDownLatch.await();
        System.out.println("sessionID: " + zk.getSessionId());
        zk.close();
    }
}
```







# Curator👨‍🌾



## 版本支持

- Curator 2.x.x：支持 Zookeeper 3.4.x 以及 ZooKeeper 3.5.x
- Curator 3.x.x：支持 ZooKeeper 3.5.x
