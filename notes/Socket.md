# Socket

Socket 起源于 unix，而 unix 基本哲学之一就是 “一切皆文件” ，都可以用 “open –> write/read –> close” 模式来操作，socket 就是该模式的一个实现。

Socket 即是一种特殊的文件，一些 Socket 函数就是对其进行的操作（读、写、打开、关闭）

Socket 就是应用层与 TCP/IP 协议族通信的中间软件抽象层，它是一组接口。在设计模式中，Socket 其实就是一个门面模式，它把复杂的 TCP/IP 协议族隐藏在 Socket 接口后面，对用户来说，一组简单的接口就是全部，让 Socket 去组织数据，以符合指定的协议。



## 同步与异步

同步和异步关注的是消息通信机制。

- 同步：就是在发出一个调用时，在没有得到结果之前，该调用就不返回。但是一旦调用返回，就得到返回值了。换句话说，就是由调用者主动等待这个调用的结果。
- 异步：调用在发出之后，这个调用就直接返回了，没有返回结果。换句话说，当一个异步过程调用发出后，调用者不会立刻得到结果。而是在调用发出后，被调用者通过状态、通知来通知调用者，或通过回调函数处理这个调用。



## 阻塞与非阻塞

阻塞和非阻塞关注的是程序在等待调用结果时的状态

- 阻塞调用：是指调用结果返回之前，当前线程会被挂起。调用线程只有在得到结果之后才会返回。
- 非阻塞调用：指在不能立刻得到结果之前，该调用不会阻塞当前线程。



## Socket描述符

<img src="../../Library/Application%20Support/typora-user-images/image-20210428101714980.png" alt="image-20210428101714980" style="zoom: 33%;" />



# Socket接口函数

<img src="http://store.secretcamp.cn/uPic/image-20210421211325259202104212113251619010805OaL2IbOaL2Ib.png" alt="image-20210421211325259" style="zoom: 48%;" />

## socket

为了执行网络 I/O，一个进程必须做的第一件事情就是调用 `socket()` 函数，指定期望的通信协议类型。

```c++
#include<sys/socket.h>
int socket (int family, int type, int protocol)
```



## connect

TCP 客户用 `connect()` 函数来建立一个与 TCP 服务器的连接。

如果是 TCP 套接口的话，函数 connect 激发 TCP 的三次握手过程，且仅在连接建立成功或出错时才返回。



## bind

 `bind()` 函数给套接口分配一个本地协议地址，对于网际协议，协议地址是 32 位 IPv4 地址或 128 位 IPv6 地址与 16 位的 TCP 或 UDP 端口号的组合。



## listen

函数 `listen` 仅被 TCP 服务器调用，它做两件事情

- 当函数 `socket` 创建一个套接口时，它被假设为一个主动套接口，也就是说，它是一个将调用 connect 发起连接的客户套接口，函数 `listen` 将未连接的套接口转换成被动套接，指示内核应接受指向此套接口的连接请求。调用函数 `listen` 导致套接口从 CLOSET 状态转换到 LISTEN 状态 

- 函数的第二个参数规定了内核为此套接口排队的最大连接个数。



## accept

`accept` 由 TCP 服务器调用，从已完成连接队列的队首返回下一个已完成连接。若已完成连接队列为空，则进程睡眠（假定套接口为缺省的阻塞方式）。



# IO模型

一个输入操作通常包括两个阶段：

- 等待数据准备好
- 从内核向进程复制数据

对于一个套接字上的输入操作，第一步通常涉及等待数据从网络中到达。当所等待数据到达时，它被复制到内核中的某个缓冲区。第二步就是把数据从内核缓冲区复制到应用进程缓冲区。



## 阻塞式I/O

应用进程被阻塞，直到数据从内核缓冲区复制到应用进程缓冲区中才返回。

在阻塞的过程中，其它应用进程还可以执行，因此阻塞不意味着整个操作系统都被阻塞。因为其它应用进程还可以执行，所以不消耗 CPU 时间，这种模型的 CPU 利用率会比较高。

下图中，`recvfrom()` 用于接收 Socket 传来的数据，并复制到应用进程的缓冲区 buffer 中。这里把 `recvfrom()` 当成系统调用。

```c
ssize_t recvfrom(int sockfd, void *buf, size_t len, int flags, struct sockaddr *src_addr, socklen_t *addrlen);
```



<img src="http://store.secretcamp.cn/uPic/image-20210409101707795202104091017081617934628V3N95FV3N95F.png" alt="image-20210409101707795" style="zoom:50%;" />



## 非阻塞式I/O

应用进程执行系统调用之后，内核返回一个错误码。应用进程可以继续执行，但是需要不断的执行系统调用来获知 I/O 是否完成，这种方式称为轮询（polling）。

由于 CPU 要处理更多的系统调用，因此这种模型的 CPU 利用率比较低。

<img src="http://store.secretcamp.cn/uPic/image-202104091018083142021040910180816179346881b0n1R1b0n1R202104091018131617934693H584UIH584UI.png" alt="image-20210409101808314" style="zoom:50%;" />



## I/O 多路复用

使用 select 或者 poll 等待数据，并且可以等待多个套接字中的任何一个变为可读。这一过程会被阻塞，当某一个套接字可读时返回，之后再使用 recvfrom 把数据从内核复制到进程中。

> 监听 socket 事件的 epoll、poll、select 是阻塞的，单个 socket 操作是非阻塞的

它可以让单个进程具有处理多个 I/O 事件的能力，这又被称为事件驱动 I/O。

如果一个 Web 服务器没有 I/O 复用，那么每一个 Socket 连接都需要创建一个线程去处理。如果同时有几万个连接，那么就需要创建相同数量的线程。相比于多进程和多线程技术，I/O 复用不需要进程线程创建和切换的开销，系统开销更小。

<img src="http://store.secretcamp.cn/uPic/image-20210409102003435202104091020031617934803wtQoCcwtQoCc.png" alt="image-20210409102003435" style="zoom:50%;" />



## 信号驱动 I/O

应用进程使用 sigaction 系统调用，内核立即返回，应用进程可以继续执行，也就是说等待数据阶段应用进程是非阻塞的。内核在数据到达时向应用进程发送 SIGIO 信号，应用进程收到之后在信号处理程序中调用 recvfrom 将数据从内核复制到应用进程中。

相比于非阻塞式 I/O 的轮询方式，信号驱动 I/O 的 CPU 利用率更高。

<img src="http://store.secretcamp.cn/uPic/image-20210409102141598202104091021411617934901vqDAh6vqDAh6.png" alt="image-20210409102141598" style="zoom:50%;" />



## 异步 I/O

应用进程执行 aio_read 系统调用会立即返回，应用进程可以继续执行，不会被阻塞，内核会在所有操作完成之后向应用进程发送信号。

异步 I/O 与信号驱动 I/O 的区别在于，异步 I/O 的信号是通知应用进程 I/O 完成，而信号驱动 I/O 的信号是通知应用进程可以开始 I/O。



<img src="http://store.secretcamp.cn/uPic/image-20210409102225135202104091022251617934945pXI5HLpXI5HL.png" alt="image-20210409102225135" style="zoom:50%;" />



## 五大模型比较

- 同步 I/O：将数据从内核缓冲区复制到应用进程缓冲区的阶段（第二阶段），应用进程会阻塞。
- 异步 I/O：第二阶段应用进程不会阻塞。

同步 I/O 包括阻塞式 I/O、非阻塞式 I/O、I/O 复用和信号驱动 I/O ，它们的主要区别在第一个阶段。

非阻塞式 I/O 、信号驱动 I/O 和异步 I/O 在第一阶段不会阻塞。

<img src="http://store.secretcamp.cn/uPic/image-20210409102404097202104091024041617935044KXcCXuKXcCXu.png" alt="image-20210409102404097" style="zoom:50%;" />



# IO多路复用

IO多路复用：单线程或单进程同时监测若干个文件描述符是否可以执行 IO 操作的能力，即通过记录跟踪每一个Sock（I/O流）的状态来同时管理多个I/O流.

<img src="http://store.secretcamp.cn/uPic/image-20210421214443760202104212144431619012683jATdpljATdpl.png" alt="image-20210421214443760" style="zoom:67%;" />

CPU单核在同一时刻只能做一件事情，一种解决办法是对CPU进行时分复用，多个事件流将CPU切割成多个时间片，不同事件流的时间片交替进行。I0多路复用 可以在单线程/进程中处理多个事件流。

select/poll/epoll 都是 IO 多路复用的具体实现，select 出现的最早，之后是 poll，再是 epoll。



## 内核接收数据流程

首先编写一段基础的网络编程代码

```c++
//创建socket
int s = socket(AF_INET, SOCK_STREAM, 0);   
//绑定
bind(s, ...)
//监听
listen(s, ...)
//接受客户端连接
int c = accept(s, ...)
//接收客户端数据
recv(c, ...);
//将数据打印出来
printf(...)
```

计算机中运行着 A、B、C 三个进程，其中进程A执行着上述基础网络程序

<img src="http://store.secretcamp.cn/uPic/image-202104212241334802021042122413316190160937PqxNR7PqxNR.png" alt="image-20210421224133480" style="zoom:50%;" />

当进程 A 执行到创建 socket 的语句时，操作系统会创建一个由文件系统管理的 socket 对象。这个 socket 对象包含了发送缓冲区、接收缓冲区、等待队列三种结构。

其中，等待队列指向所有需要等待该 socket 事件的进程。

<img src="http://store.secretcamp.cn/uPic/image-20210421224734252202104212247341619016454JyNfjoJyNfjo.png" alt="image-20210421224734252" style="zoom: 50%;" />

当程序执行到 `recv` 时，操作系统会将进程 A 从工作队列移动到该 socket 的等待队列中。工作队列此时只剩下了进程 B、C，依据进程调度，CPU 会轮流执行这两个进程的程序，不会执行进程 A 的程序。所以进程 A 被阻塞，不会往下执行代码，也不会占用 CPU 资源。

<img src="http://store.secretcamp.cn/uPic/image-20210421225119698202104212251191619016679IO5UCrIO5UCr.png" alt="image-20210421225119698" style="zoom:50%;" />



步骤①：CPU 轮流执行进程 B 和进程 C

步骤②：进程在 `recv()` 阻塞期间，计算机收到了某个 socket 连接的客户端发送的数据。

步骤③：数据经由网卡通过硬件 DMA 的方式被写入到内存，然后网卡通过中断信号通知 CPU 有数据到达，

步骤④：CPU 让出当前正在执行的程序，执行中断程序

步骤⑤：先将网络数据写入到对应 socket 的接收缓冲区里面。

步骤⑥：再唤醒进程 A，重新将进程 A 放入工作队列中。

由于一个 socket 对应一个端口号，而网络数据包中包含了 IP 和端口的信息，内核可以通过端口号找到对应的 socket 。

<img src="http://store.secretcamp.cn/uPic/image-202104212254518882021042122545216190168922gaOHV2gaOHV.png" alt="image-20210421225451888" style="zoom:50%;" />

唤醒进程的过程如下:

<img src="http://store.secretcamp.cn/uPic/image-20210421230506882202104212305071619017507bSHXx2bSHXx2.png" alt="image-20210421230506882" style="zoom:60%;" />



在以上的流程中，`recv()` 只能监视单个 socket，但是服务端需要管理多个客户端连接，于是人们开始寻找监视多个 socket 的方法。

select 、 poll 、 epoll 都是 IO 多路复用的具体实现。





## select

### 设计思路

```c
int select(int n, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
```



先准备一个数组 fds，让该数组存放着所有需要监视的 socket 的文件描述符，然后进程调用 `select()` ，首先会遍历一遍 fds ，如果 fds 中的所有 socket 都没有数据，`select()` 会使进程阻塞，直到有一个 socket 接收到数据，`select()` 才会唤醒进程并返回。

> unix 系统中一切皆文件，通过文件描述符即可找到对应的 socket

```c
int s = socket(AF_INET, SOCK_STREAM, 0);  
bind(s, ...)
listen(s, ...)

int fds[] =  存放需要监听的socket的文件描述符

while(1){
    // 返回值是整形，代表有几个 socket 就绪了
    int n = select(..., fds, ...)
    for (int i = 0; i < fds.count; i++){
        if (FD_ISSET(fds[i], ...)) {
            //fds[i]的数据处理
        }
    }
}
```



假如程序同时监视如下图的 sock1 、sock2 和 sock3 一共三个 socket，那么在调用 `select()` 之后，首先会遍历 fds ，如果某个 socket 有数据，则会立即返回，如果没有，那么操作系统会将 A 从工作队列移除，并分别加入这三个 socket 的等待队列中。进程 A 从工作队列中移除，不会再分配到 CPU 时间片，即阻塞。

遍历 fds，通过 FD_ISSET 判断具体哪个 socket 收到数据，然后做出处理。

<img src="http://store.secretcamp.cn/uPic/image-20210421232811534202104212328111619018891hm6V9ghm6V9g.png" alt="image-20210421232811534" style="zoom: 50%;" />



当与某个 socket 连接的客户端发送完数据后，数据通过硬件 DMA 的方式被写入到内存，之后相应的硬件会向 CPU 发出中断信号，CPU 会让正在执行的进程让出，去执行网络数据就绪的中断程序，将内存中的网络数据写入对应 socket 的读缓冲区中。

当任何一个 socket 收到数据后，将触发中断程序，唤醒进程，即将进程从所有 socket 的等待队列中移除，加入到工作队列里面，然后 `select()` 函数返回。

`select()` 函数的返回值是整形，代表有几个 socket 处于就绪状态。

<img src="http://store.secretcamp.cn/uPic/image-20210421233239373202104212332391619019159q5JmYzq5JmYz.png" alt="image-20210421233239373" style="zoom:47%;" />

<img src="http://store.secretcamp.cn/uPic/image-20210421233536174202104212335361619019336uZpCIquZpCIq.png" alt="image-20210421233536174" style="zoom:50%;" />



当进程A 被唤醒后，它知道至少有一个 socket 接收了数据。程序只需遍历一遍 fds，检查每个 socket 文件描述符的状态，就可以得到就绪的 socket。





### 分析

*select* 是最简单的 IO 多路复用实现方法，但存在明显缺点：

- 每次调用 `select` 都需要将进程加入到所有监视 socket 的等待队列，每次唤醒都需要从每个队列中移除。这里涉及了两次遍历，而且每次都要将整个 fds 列表传递给内核，有一定的开销。正是因为遍历操作开销大，出于效率的考量，才会规定 select 的最大监视数量，默认只能监视 1024 个 socket 。

- 进程被唤醒后，程序并不知道哪些 socket 收到数据，还需要遍历一次。

一次 select 总共需要三次遍历

- 将进程加入所有 socket 的等待队列
- 将进程从所有 socket 的等待队列中移除
- 遍历所有 socket ，看哪个 socket 的缓冲区内有数据



## poll

```c
int poll(struct pollfd *fds, unsigned int nfds, int timeout);
```

poll 本质上和 select 没有区别，它将用户传入的数组 fds 拷贝到内核空间，然后查询每个 fd 对应的状态，但是它没有最大连接数的限制。

poll 中传入的数字 fds 是 pollfd 类型的数组，pollfd 的定义如下：

```c
struct pollfd {
     int   fd;         /* file descriptor */
     short events;     /* requested events */
     short revents;    /* returned events */
};
```



## epoll

### 设计思路

select 低效的原因之一是将 "维护等待队列" 和 "阻塞唤醒进程" 两个步骤合二为一

epoll 将这两个操作分开，先用 `epoll_ctl` 维护等待队列，再调用 `epoll_wait` 阻塞唤醒进程。



select 低效的另一个原因在于程序不知道哪些 socket 收到数据，只能一个个遍历。如果内核维护一个 "就绪列表"，引用收到数据的 socket ，就能避免遍历，这一点是通过维护 eventpoll 对象中的就序列表实现的。

<img src="http://store.secretcamp.cn/uPic/image-20210422212928938202104222129291619098169shXCThshXCTh.png" alt="image-20210422212928938" style="zoom:50%;" />



先用 `epoll_create()` 创建一个代表 epoll 的 eventpoll 对象，再通过 `epoll_ctl()` 将需要监视的 socket 添加到 eventpoll 中，最后调用 `epoll_wait` 等待数据。

在 epoll 中，需要一次遍历，而 select 需要三次遍历，效率明显提高。

```c
int s = socket(AF_INET, SOCK_STREAM, 0);   
bind(s, ...)
listen(s, ...)

int epfd = epoll_create(...);  // 创建一个 eventpoll 对象，系统会返回一个 eventpoll 对象的 id
epoll_ctl(epfd, ...); // 将所有需要监听的 socket 添加到 epfd 中

while (1) {
    int n = epoll_wait(...)
    for (接收到数据的socket) {
        //处理
    }
}
```



### eventpoll数据结构

`epoll()` 函数在内核空间内，有一个对应的数据结构存储相关信息，这个数据结构实际上就是 eventpoll 对象。

eventpoll 对象可以通过系统函数 `epoll_create()` 去创建，并返回一个 eventpoll 对象的文件描述符 epfd 。

```c
int epoll_create(int size);
```







eventpoll 主要有三块重要的区域，一块是监听队列 rbr（红黑树），用于存放需要监听的 socket_fd ，另一块是就序队列 rdlist（双向链表），存放处于就绪状态的 socket 信息，最后一块是等待队列 wq，保存了调用 `epoll_wait()` 函数的进程。

另外，epoll 还提供了两个函数 `epoll_ctl()` 和 `epoll_wait()` 

- `epoll_ctl()` 可以去增删改内核空间中 eventpoll 对象的监听列表

- `epoll_wait()` 主要参数也是 eventpoll 对象，用于获取此次系统调用需要监听的 socket_df，默认情况下会阻塞调用进程，直到 eventpoll 中关联的某个 socket 就绪之后，`epoll_wait()` 才会返回。

<img src="../../Library/Application%20Support/typora-user-images/image-20210427173426819.png" alt="image-20210427173426819" style="zoom:50%;" />

#### epoll_ctl

epfd： `epoll_create()` 函数的返回值，即 eventpoll 对象

op：表示动作，即增删改

fd：需要监听的 socket 的文件描述符

*event：需要监听的事件

```c
int epoll_ctl (int epfd, int op, int fd, struct epoll_event *event);
```



#### epoll_wait

epfd： `epoll_create()` 函数的返回值，即 eventpoll 对象

```c
int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);
```



### 创建epoll对象

当某个进程调用 `epoll_create()` 方法时，内核会创建一个代表该 epoll 的 eventpoll ，并返回一个 eventpoll 对象的 文件描述符 epfd ，相当于在内核中开辟了一小块内存空间，这个内存空间的位置是已知的。

eventpoll 对象也是文件系统中的一员，和 socket 一样，它也会维护一个等待队列。eventpoll 对象相当于是 socket 和进程之间的中介，socket 的数据接收并不直接影响进程，而是通过改变 eventpoll 的就绪列表 rdlist 来改变进程状态。



### 维护监听队列

创建 epoll 对象后，可以用 `epoll_ctl()` 添加或删除所要监听的 socket ，内核程序会将 eventpoll 添加到这三个 socket 的等待队列中。

> 这里避免了 select 中将、将进程从 sock 等待队列中移除的操作，取而代之的是 eventpoll 会被加入所有 sock 的等待队列，但没必要移除它，这就减少了一次循环操作。

<img src="http://store.secretcamp.cn/uPic/image-20210422225912683202104222259121619103552DC34R9DC34R9.png" alt="image-20210422225912683" style="zoom:50%;" />





### 阻塞进程

当程序执行到 `epoll_wait` 时，如果就绪队列 rdlist 中没有任何引用，那么内核会将进程 A 放入 eventpoll 的等待队列中，阻塞进程。

<img src="http://store.secretcamp.cn/uPic/image-202104222146245832021042221462416190991843VLa4x3VLa4x.png" alt="image-20210422214624583" style="zoom:50%;" />



### 接收数据

当 socket 接收到数据，中断程序会操作 eventpoll 对象，一方面修改 eventpoll 的就绪队列 rdlist，另一方面唤醒 eventpoll 等待队列中的进程

epoll 中接收数据的过程与 select 相似，当与某个 socket 连接的客户端发送完数据后，数据通过硬件 DMA 的方式被写入到内存，之后相应的硬件会向 CPU 发出中断信号。CPU 会让正在执行的进程让出，去执行网络数据就绪的中断程序，将内存中的网络数据写入对应 socket 的读缓冲区中。

<img src="http://store.secretcamp.cn/uPic/image-20210422214200712202104222142001619098920rcH9rKrcH9rK.png" alt="image-20210422214200712" style="zoom:50%;" />



### 唤醒进程

socket 接收到数据之后，中断程序会去检查 socket 的等待队列，发现 socket 等待队列中引用的不是一个进程，而是一个 eventpoll 对象，于是就将当前 socket 的引用追加到 eventpoll 对象的就绪列表的末尾。然后继续检查 eventpoll 对象的等待队列，如果有进程，就将进程加入到内核的工作队列中，唤醒进程。

因为就绪列表 rdlist 的存在，进程 A 可以知道哪些 socket 接收到了数据。

`epoll_wait()` 函数在调用的时候，会传入一个 epoll_event 事件数组指针，`epoll_wait()` 在返回之前，会将就绪的 socket 事件信息拷贝到这个指针表示的数组中，这样就可以通过这个数组拿到就绪的 socket 信息了。

<img src="http://store.secretcamp.cn/uPic/image-20210422214719189202104222147191619099239C20c1nC20c1n.png" alt="image-20210422214719189" style="zoom:50%;" />





### 相关问题

#### 如何知晓socket数据到达

依靠中断程序获知

如果 `select()` 在第一次调用没有发现就绪的 socket，那么进程会进入阻塞状态。

网络中数据完成传输之后，会触发中断程序，CPU 会立刻执行中断程序，根据内存中的数据包中的端口号，确定是哪个 socket 的实例，将数据读入到 socket 的读缓冲区中，然后会去检查 socket 的等待队列是否有正在等待的进程，如果有，则将该进程移入 CPU 的工作队列中，然后当前进程再次执行 `select()` 函数去检查是否存在就绪的 socket。



#### select和poll的区别

`select()` 使用的是 fd_set，内核中规定了 `#define __FD_SETSIZE    1024` ，即该数组最大长度为 1024 ，要修改只能重新编译内核。`poll()` 使用的是自定义的 pollfd 数组结构，没有长度为 1024 的限制。



#### epoll_wait可以设置成非阻塞吗

可以，`epoll_wait()` 有一个 int 类型的 timeout 参数，表示阻塞时间的长度，如果设置为 0 ，那么就是非阻塞调用的，每次调用都会去检查 eventpoll 的就序列表。



#### eventpoll的监听列表的数据结构

eventpoll 的监听列表（并不是一个列表）用于存放需要监听的 socket 的集合信息，采用的是红黑树结构，因为这个 socket 集合信息经常需要进行增删改查，。





