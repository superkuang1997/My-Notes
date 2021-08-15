# 概述🌈

## 并发与并行

* 并发（Concurrent）：指两个或多个事件在同一个时间段内发生。
* 并行（Parallel）：指两个或多个事件在同一时刻发生（同时发生）。

<img src="http://store.secretcamp.cn/uPic/v2-674f0d37fca4fac1bd2df28a2b78e633_r202101271744141611740654RLIxNtRLIxNt.jpg" style="zoom: 100%;" />

在操作系统中，安装了多个程序，并发指的是在一段时间内宏观上有多个程序同时运行，但在微观上这些程序是分时的交替运行，只不过是给人的感觉是同时运行，那是因为分时交替运行的时间是非常短的。

在多核 CPU 中，则这些可以并发执行的程序便可以分配到多个核心上，实现多任务并行执行，即利用每个处理器来处理一个可以并发执行的程序，这样多个程序便可以同时执行。

>  注意：单核处理器的计算机肯定是不能并行的处理多个任务的，只能是多个任务在单个cpu上并发运行。同理，线程也是一样的，从宏观角度上理解线程是并行运行的，但是从微观角度上分析却是串行运行的，即一个线程一个线程的去运行，当系统只有一个cpu时，线程会以某种顺序执行多个线程，我们把这种情况称之为线程调度。



## 线程与进程

- 进程： 进程是一个内存中运行的应用程序，每个进程都有一个独立的内存空间，一个应用程序可以同时运行多个进程；进程也是程序的一次执行过程，是系统运行程序的基本单位；系统运行一个程序即是一个进程从创建、运行到消亡的过程。

- 线程： 线程是进程中的一个执行单元，负责当前进程中程序的执行，一个进程中至少有一个线程。一个进程中是可以有多个线程的，这个应用程序也可以称之为多线程程序。

  简而言之：一个程序运行后至少有一个进程，一个进程中可以包含多个线程 

线程调度:

- 分时调度

  所有线程轮流使用 CPU 的使用权，平均分配每个线程占用 CPU 的时间。

- 抢占式调度

  优先让优先级高的线程使用 CPU，如果线程的优先级相同，那么会随机选择一个(线程随机性)，Java使用的为抢占式调度。




## 多线程与多进程

|                | 多进程                                                       | 多线程                                                       | 总结     |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------- |
| 数据共享、同步 | 数据共享复杂，需要用IPC；数据是分开的，同步简单              | 因为共享进程数据，数据共享简单，但也是因为这个原因导致同步复杂 | 各有优势 |
| 内存、CPU      | 占用内存多，切换复杂，CPU利用率低                            | 占用内存少，切换简单，CPU利用率高                            | 线程占优 |
| 创建销毁、切换 | 创建销毁、切换复杂，速度慢                                   | 创建销毁、切换简单，速度很快                                 | 线程占优 |
| 编程、调试     | 编程简单，调试简单                                           | 编程复杂，调试复杂                                           | 进程占优 |
| 可靠性         | 进程间不会互相影响                                           | 一个线程阻塞可能会影响整个进程                               | 进程占优 |
| 分布式         | 适应于多核、多机分布式；如果一台机器不够，扩展到多台机器比较简单 | 适应于多核分布式                                             | 进程占优 |







# 内存模型与线程🌈

## JMM概念

java内存模型（Java Memory Model，JMM）是java虚拟机规范定义的，用来屏蔽java程序在各种不同的硬件和操作系统对内存的访问的差异，可以实现java程序在各种不同的平台上都能达到内存访问的一致性。



## 缓存一致性问题

绝大多数运算任务并不仅仅只有 "计算" ，处理器还要与内存交互，读取运算数据、储存运算结果。

计算机的存储设备和处理器的运算速度有几个数量级的差距，cpu 的时钟频率非常的快，速度远远超过了内存、硬盘，所以必须引入一层读写速度接近处理器运算速度的「高速缓存」作为处理器和内存之间的缓冲。

将运算需要使用到的数据复制到缓存中，让运算能够快速进行，当运算结束后再从缓存同步回内存之中，处理器无需等待缓慢的内存读写。

「高速缓存」的引入也带来了「缓存一致性问题」

在多核处理器中，每个处理器都有自己的高速缓存，它们又共享同一主内存，多个处理器的运算结果都涉及同一块主内存区域时，可能会导致缓存数据不一致。



## 指令重排序

### 数据依赖性

如果两个操作访问同一个变量，且这两个操作中有一个为写操作，此时这两个操作之间就存在数据依赖性。

```
写后读  a = 1 -> b = a; 
写后写  a = 1 -> a = 2;
读后写  a = b -> b = 1; 
```

编译器和处理器可能会对操作进行重排序，编译器和处理器在重排序时，会遵守数据依赖性，编译器和处理器不会改变存在数据依赖关系的两个操作的执行顺序。

> 数据依赖性仅针对单个处理器中执行的指令序列和单个线程中执行的操作，不同处理器之间和不同线程之间的数据依赖性不被编译器和处理器考虑。



### 线程内串行

线程内表现为串行的语义（Within-Thread As-If-Serial Semantics）指：不管怎么重排序，单线程程序的执行结果不能被改变，即编译器和处理器不会对存在数据依赖关系的操作做重排序，因为这种重排序会改变执行结果。



### 编译器的指令重排序

```java
public class Rearrange {
    private static int a = 0;
    private static boolean flag = false;

    public static void main(String[] args) {
        new Thread(()-> {
            a = 1;  				  // 操作1
            flag = true;      // 操作2
        }, "A").start();

        new Thread(()-> {
            if (flag) {          // 操作3
                int i = a * a;   // 操作4 
            }
        }, "B").start();
    }
}
```

操作1与操作2之间没有数据依赖性，操作3与操作4之间也没有数据依赖性，那么编译器就可能进行指令重排序

可能有以下情况：  `操作2 -> 操作3-> 操作4 -> 操作1`

显然多线程的语义被破坏了，变量a还没有被赋值，就已经被使用了，但是此时运行仍能得到正确结果，因为编译器和处理器会采用猜测执行（Speculation）来克服控制相关性对并行度的影响。

具体操作是：执行线程B的处理器可以提前读取并计算 `a * a` ，然后把计算结果临时保存到一个名为「重排序缓存」的硬件缓存中，当接下来操作3的条件判断为真时，就把该计算结果写入变量i中。



### 处理器的指令重排序

指令集并行的重排序是对CPU的性能优化，从指令的执行角度来说一条指令可以分为多个步骤：

- 取指 IF
- 译码和取寄存器操作数 ID
- 执行或者有效地址计算 EX
- 存储器访问 MEM
- 写回 WB

cpu在工作时，需要将上述指令分为多个步骤依次执行，为了提高硬件利用率，cpu会自动进行指令重排序。





## 工作内存与主内存

Java 内存模型的主要目标是定义程序中各个变量的访问规则，如虚拟机「将变量存储到内存」和「从内存中取出变量」。

这里的变量包括了实例字段、静态字段和构成数组对象的元素，但不包括局部变量和方法参数，因为后者是线程私有的。

JMM 规定所有的变量存储在主内存中。每条线程有自己的工作内存，其中保存了被线程使用到的变量的主内存副本拷贝，线程对变量的所有操作都在工作内存中进行，不能直接读写主内存中的变量，不同的线程之间的变量也无法相互访问。

主内存 ---> 物理硬件的内存

工作内存 ---> 寄存器和高速缓存 

<img src="http://store.secretcamp.cn/uPic/image-20210715221158572202107152211581626358318DojrNrDojrNr.png" alt="image-20210715221158572" style="zoom:40%;" />



## 内存间交互

变量在主内存与工作内存之间的交互，主要有以下 8 种操作：

- read（读取）：把一个变量的值从主内存传输到工作内存中
- load（载入）：在 read 之后执行，把 read 得到的值放入工作内存的变量副本中
- use（使用）：把工作内存中一个变量的值传递给执行引擎
- assign（赋值）：把一个从执行引擎接收到的值赋给工作内存的变量
- store（赋值）：把工作内存的一个变量的值传送到主内存中
- write（写入）：在 store 之后执行，把 store 得到的值放入主内存的变量中
- lock（锁定）：作用于主内存的变量，把一个变量标识为一条线程独占的状态
- unlock（解锁）：作用于主内存的变量，把一个处于 lock 状态的变量释放，释放后的变量可以被其他线程 lock

![](http://store.secretcamp.cn/uPic/java%E5%86%85%E5%AD%98%E9%97%B4%E4%BA%A4%E4%BA%9220210715223132162635949285RnOn85RnOn.png)





## volatile

*volatile* 是 Java 虚拟机提供的最轻量級的同步机制。

当一个变量定义为 *volatile* 之后，将会具备以下特性：

- 保证此变量对其他所有线程的可见性
- 禁止指令重排序优化



### 保证可见性

“可见性” 是指当一条线程修改了这个变量的值，新值对于其他线程来说是可以立刻得知的。

- 对 *volatile* 变量进行写操作后，会立刻将值回写到主内存，保证其他线程可以看到自己对变量所做的修改。

- 每次读取 *volatile* 变量，工作内存必须从主内存中刷新最新值，保证能看见其他线程对变量所做的修改。

> 对于普通变量而言，值也是必须从工作内存回写到主内存，才能被线程获取，但修改普通变量后并不会立刻回写到主内存



注意！ 这不意味着基于 *volatile* 的变量在并发下是安全的。

例如对一个 *volatile* 类变量进行自增操作， javap 反编译后可以得到字节码，一个自增操作实际上对应4条字节码语句

```
getstatic  // getstatic 把值取到操作栈顶，volatile 保证此时的值是最新的
iconst_1   // 但执行下面的三条字节码指令时，在多线程下，值可能已经被改变了
iadd
putstatic  // putstatic 可能会把过期的数据同步回主内存
```

*volatile* 只能保证 `getstatic` 时获取的是正确的值，但如果此时此刻有多个线程对该变量进行操作，那么 `getstatic` 获取的值可能就会变成了过期的数据，那么增加后回写的数据就是错误的。

例子：

1. 假如某个时刻变量 inc 的值为 10，线程 A 对变量进行自增操作，先读取了变量 inc 的原始值，然后线程 A 被阻塞；

2. 然后线程 B 对变量进行自增操作，也去读取变量 inc 的原始值，由于线程 A 只是对变量 inc 进行读取操作，而没有对变量进行修改操作，所以不会导致线程 B 的工作内存中缓存变量 inc 的缓存行无效，所以线程 B 会直接去主存读取 inc 的值，发现 inc 的值是 10，然后进行加 1 操作，并把 11 写入工作内存，最后写入主存。

3. 线程 A 唤醒后接着进行加 1 操作，由于已经读取了 inc 的值，此时在线程 A 的工作内存中 inc 的值仍然为 10，所以线程 A 对 inc 进行加1 操作后 inc 的值为11，然后将 11 写入工作内存，最后写入主存。



由于 *volatile* 变量只能保证可见性，在不符合以下两条规则的运算场景中，仍然要通过加锁来保证原子性：

- 运算结果并不依赖变量的当前值，或者能够确保只有单一的线程修改变量的值。

- 变量不需要与其他的状态变量共同参与不变约束。

对于 "一写多读"，*volatile* 可以解决变量同步问题，但是如果是 "多写"，则无法保证线程安全。



### 禁止指令重排序

*volatile* 修饰的变量不会被指令重排序优化，从而保证代码的执行顺序与程序的顺序 相同

原理是在汇编的层面上会增加一个「内存屏障」，重排序不能把后面的汇编指令重排序到内存屏障之前的位置。

例如 ，`lock addl$0x0，(%esp)` 指令就是一个内存屏障，`addl$0x0，(%esp)` 指把 ESP 寄存器的值加 0 ，相当于一个空操作，重要的是 `lock` ，它的作用是将本处理器的缓存写入了内存，该写入动作也会引起别的处理器作废其缓存。在 JMM 中，相当于对缓存中的变量做了一次 store 和 write 操作。

<img src="http://store.secretcamp.cn/uPic/image-20210715224643911202107152246441626360404RkTkZsRkTkZs.png" alt="image-20210715224643911" style="zoom:80%;" />

仅有一个 CPU 时，无需内存屏障，但如果有多个 CPU 访问同一块内存，其中有一个在观察另一个，就需要内存屏障来保证一致性。



### 与synchronized的对比

*volatile* 变量的读操作和普通变量相同，写操作比普通变量稍慢，因为需要在代码中插入内存屏障相关的指令。

大多数情况下，*volatile* 的性能开销比 *synchronized* 和 锁操作要低



### volatile与MESI

*volatile* 和 MESI 差着好几层抽象，中间会经历 Java 编译器，Java 虚拟机和 JIT，操作系统，CPU 核心。

*volatile* 是一个高层的表达意图的 “抽象”，而 MESI 是为了实现这个抽象，在某种特定情况下需要使用的一个实现细节，*volatile* 的底层实现，满足了 MESI 的触发条件。







## 内存模型的三大特性

### 原子性

Java 内存模型保证了 read、load、use、assign、store、write、lock 和 unlock 这样的内存间交互操作具有原子性。

但是 Java 内存模型允许虚拟机将没有被 volatile 修饰的64位数据（long，double）的读写操作划分为两次 32 位的操作来进行，即 load、store、read 和 write 操作可以不具备原子性，但是商用虚拟机都选择将64位数据实现为原子操作。



### 可见性

可见性指当一个线程修改了共享变量的值，其它线程能够立即得知这个修改。Java内存模型是通过在变量修改后将新值同步回主内存，在变量读取前从主内存刷新变量值来实现可见性的。

主要有三种实现可见性的方式：

- *volatile*：上文提及

- *synchronized*： 对一个变量执行 unlock 操作之前，必须把变量值同步回主内存（store、write）。

- *final*： 被 *final* 关键字修饰的字段在构造器中一旦初始化完成，并且没有发生 this 逃逸，那么其它线程就能看见 *final* 字段的值。

  > this 逃逸是指其它线程通过 this 引用访问到初始化了一半的对象



### 有序性

有序性是指：在本线程内观察，所有操作都是有序的；但在一个线程观察另一个线程，所有操作都是无序的。

前半句是指： “线程内表现为串行的语义”

后半句是指： “指令重排序” 和 “工作内存与主内存同步延迟”

在 Java 内存模型中，允许编译器和处理器对指令进行重排序，重排序过程不会影响到单线程程序的执行，却会影响到多线程并发执行的正确性。

java提供了 *volatile* 和 *synchronized* 两个关键字来保证线程之间操作的有序性

- *volatile* 关键字通过添加内存屏障的方式来禁止指令重排，即重排序时不能把后面的指令放到内存屏障之前。

- *synchronized* 保证每个时刻只有一个线程执行同步代码，相当于是让线程顺序执行同步代码。

但如果有序性仅靠以上两种方法，就会变得非常繁琐，「先行并发原则」也是保证有序性的重要规则。





## 先行并发原则

除了可以用 `volatile` 和 `synchronized` 来保证有序性，jvm还规定了先行发生原则，让一个操作无需控制就能先于另一个操作完成。

一个操作 “时间上的先发生” 不代表这个操作会是 “先行发生” 的，两者之间没有必然联系。



### 单一线程原则

在一个线程内，在程序前面的操作先行发生于后面的操作。



### 管程锁定规则

一个 `unlock` 操作先行发生于后面对同一个锁的 `lock` 操作。



### volatile变量规则

对一个 *volatile* 变量的写操作先行发生于后面对这个变量的读操作。

因为对 *volatile* 变量的写操作之后会立刻刷新回主存，其他线程的读操作自然在写操作之后了。



### 线程启动规则

Thread 对象的 `start` 方法调用先行发生于此线程的每一个动作。



### 线程加入规则

Thread 对象的结束先行发生于 `join` 方法返回。



### 线程中断规则

对线程 `interrupt` 方法的调用先行发生于被中断线程的代码检测到中断事件的发生，可以通过 `interrupted` 方法检测到是否有中断发生。





# Java线程🌈

## 线程的实现

### 使用内核线程实现

内核线程（Kernel-Level Thread，KLT）就是直接由操作系统内核支持的线程，这种线程由内核来完成线程切换，内核通过调度器对线程进行调度，并负责将线程的任务映射到各个处理器上。

程序一般不会直接使用内核线程，而是使用内核线程的一种高级接口——轻量级进程（Light Weight Process，LWP），即通常意义上的线程。

<img src="http://store.secretcamp.cn/uPic/image-20210127203549418202101272035491611750949Ejo5QsEjo5Qs.png" alt="image-20210127203549418" style="zoom:50%;" />

优点：

- 内核直接支持，负责维护进程及线程的上下文信息以及线程切换。
- 每个 LWP 都是一个独立的调度单元，即使阻塞也不会影响整个进程继续工作

缺点：

- 由于 LWP 是基于内核线程实现，各种线程操作都需要进行系统调用，系统调用的代价比较高，需要在用户态和内核态来回切换。
- 每个 LWP 都需要有一个内核线程支持，需要消耗一定的内核资源，因此一个系统支持的 LWP 数量是有限的



### 使用用户线程实现

用户线程完全建立在用户空间的线程库上，系统内核不能感知线程的存在，用户线程的建立、同步、销毁和调度完全在用户态中完成，不需要内核的帮助。

<img src="http://store.secretcamp.cn/uPic/image-2021012720365791820210127203658161175101823Zu7v23Zu7v.png" alt="image-20210127203657918" style="zoom:50%;" />

优点：

- 用户线程不需要切换到内核态，因此操作是非常快速且低消耗的，可以支持规模更大的线程数量。

缺点：

- 操作系统只将 CPU 资源分配到进程，因此线程相关的所有操作都需要用户自己处理。
- 多核处理器系统中很难将线程映射到其他处理器上。
- 一个线程在使用内核服务时被阻塞，那么整个进程都会被阻塞，因为操作系统意识不到用户线程的存在，只会阻塞整个进程。



### 使用用户线程加轻量级进程混合实现

在这种方式下，既存在用户线程，也存在轻量级进程。

操作系统提供 LWP 作为用户线程和内核线程之间的桥梁，这样可以使用内核提供的线程调度功能及 CPU 映射，并且用户线程的系统调用通过 LWP 来完成，降低了整个进程被完全阻塞的风险。

<img src="http://store.secretcamp.cn/uPic/image-20210127203721787202101272037211611751041whQQQgwhQQQg.png" alt="image-20210127203721787" style="zoom:50%;" />



### Java线程的实现

对于 HotSpot，它的每一个 Java 线程都是直接映射到一个操作系统原生线程来实现的，中间没有额外的间接结构，所以 HotSpot 自己是不会去干涉线程调度，全权交给操作系统去处理，何时冻结或唤醒线程、该给线程分配多少处理 器执行时间、该把线程安排给哪个处理器核心去执行等，都是由操作系统完成的



## Java线程调度

线程调度是指系统为线程分配处理器使用权的过程，主要分为两种：

- 协同式线程调度
- 抢占式线程调度

Java使用的线程调度方式是抢占式调度

### 协同式调度

协同式调度的多线程系统，线程的执行时间由线程本身来控制，线程把自己的工作执行完之后，主动通知系统切换到另一个线程。

优点：

- 实现简单
- 切换线程是可知的，不存在线程同步的问题

缺点：

- 线程执行时间不可控制，如果一个线程出现问题，一直不告知系统切换线程，那么就会一直阻塞



### 抢占式调度

抢占式调度的多线程系统，每个线程将由系统来分配执行时间，切换线程不由线程本身决定。

优点：

- 不会因为一个线程阻塞导致整个进程阻塞





## 线程状态

### 线程状态概述

当线程被创建并启动以后，它既不是一启动就进入了执行状态，也不是一直处于执行状态。 `java.lang.Thread.State` 这个枚举类中提供了六种线程状态。

| 线程状态                   | 导致状态发生条件                                             |
| -------------------------- | ------------------------------------------------------------ |
| New（新建）                | 线程刚被创建，但是并未启动。还没调用start方法。              |
| Runnable（可    运行）     | 线程可以在java虚拟机中运行的状态可能正在运行自己代码也可能没有，这取决于操作系统处理器。 |
| Blocked（锁阻 塞           | 当一个线程试图获取一个对象锁，而该对象锁被其他的线程持有则该线程进入Blocked状态；当该线程持有锁时，该线程将变成 Runnable 状态。 |
| Waiting（无限 等待）       | 一个线程在等待另一个线程执行一个（唤醒）动作时，该线程进入Waiting状态。进入这个状态后是不能自动唤醒的，必须等待另一个线程调用`notify`或者`notifyAll`方法才醒够 |
| Timed Waiting（计时 等待） | 同 Waiting 状态，有几个方法有超时参数，调用他们将进入 TimedWaiting 状态。这一状态 将一直保持到超时期满或者接收到唤醒通知带有超时参数的常用方法有 `Thread.sleep` ， `Object.wait` |
| Teminated（被 终止）       | 因为 `run` 方法正常退出而死亡，或者因为没有捕获的异常终止了 `run` 方法而死亡。 |



### New（新建）

创建后尚未启动。



### Runnable（可运行）

处于此状态的线程可能正在运行，也可能在等待 CPU 为它分配执行时间



### Blocked（阻塞）

*Blocked* 描述为：「一个正在阻塞并等待一个锁对象的线程」处于这一状态。

一个处于 *Runnable* 状态的线程，如果没有争取到锁对象，就会进入 *Blocked* 状态





### TimedWaiting（限期等待）

*TimedWaiting* 描述为：「一个正在限时等待另一个线程执行一个（唤醒）动作的线程」处于这一状态。

处于无限期等待的线程不会被 CPU 分配执行时间，但无需等待其他线程显式地唤醒，在一定时间后会被系统自动唤醒。

调用了 `sleep` 方法之后，当前执行的线程就进入到 “休眠状态”，其实就是所谓的 TimedWaiting，线程睡眠到期自动苏醒，并返回到 Run*n*able（可运行）

进入到 *TimeWaiting* 有两种方式

-  `Thread::sleep()` 方法，在毫秒值结束之后，线程睡醒进入到 *Runnable*/*Blocked* 状态

- 使用 `Object.wait(long m)` 方法，如果在毫秒值结束之后，还没有被 `notify()` 唤醒，就会自动醒来，线程睡醒进入到Runnable/Blocked 状态



### Waiting（无限期等待）

*Waiting* 描述为：「一个正在无限期等待另一个线程执行一个特别的唤醒动作的线程」处于这一状态。

处于无限期等待的线程不会被cpu分配执行时间，它们要等待其他线程显示地唤醒。

进入到 *Waiting* 的方法：

-  `Object::wait()` 方法

-  `Thread::join()` 方法



### Teminated（结束）

线程已经结束执行



### 状态转换

<img src="http://store.secretcamp.cn/uPic/image-20210716085333648202107160853331626396813moRkxNmoRkxN.png" alt="image-20210716085333648" style="zoom:45%;" />



# Java协程🌈

## 内核线程的局限

Java 线程的实现方式是 1：1 的映射到内核线程上，是如今 Java 虚拟机线程实现的主流选择，但是这种映射到操作系统上的线程天然的缺陷是切换、调度成本高昂，系统能容纳的线程数量也很有限。

内核线程调度的切换成本更高，主要来自于用户态与核心态之间的状态转换，这部分成本主要来自于响应中断、保护和恢复执行现场。

当中断发生，从线程 A 切换到线程 B 去执行之前，操作系统首先要把线程 A 的上下文数据妥善保管好，然后把寄存器、内存分页等恢复到线程 B 挂起时候的状态，这样线程 B 被重新激活后才能仿佛从来没有被挂起过。这种保护和恢复现场的工 作，涉及一系列数据在各种寄存器、缓存中的来回拷贝，自然不是一种轻量级的操作。

在处理一个允许花费很长时间请求中，具有这种线程切换的成本也是无伤大雅的，但在微服务架构下，每个服务的复杂度降低，但服务数量增加，这就缩短了留给每个服务的响应时间，因为这要求组合多个服务的总耗时不能太长，在这样的场景下，由于每个请求本身的执行时间变得很短、数量变得很多， 用户线程切换的开销甚至可能会接近用于计算本身的开销，这就会造成严重的浪费。传统 Java Web 服务器的线程池的容量通常在几十个到两百之间，如果有数以百万计的请求，那么其中切换损耗也是相当可观的。





## 协程





## 纤程

对于有栈协程，有一种特例实现名为纤程（Fiber），即为了解决 Java 线程模型的局限性而设计的官方解决方案。

OpenJDK在 2018 年创建了 Loom 项目，意图是重新提供对用户线程的支持，但与过去的绿色线程不同，这些新功能不是 为了取代当前基于操作系统的线程实现，而是会有两个并发编程模型在 Java 虚拟机中并存，可以在程 序中同时使用。

在新并发模型下，一段使用纤程并发的代码会被分为两部分：

- 执行过程（Continuation）：执行过程主要用于维护执行现场，保护、恢复上下文状态

- 调度器（Scheduler）：调度器则负责编排所有要执行的代码的顺序。

将执行过程与调度程序分离的好处是，用户可以选择自行控制其中的一个或者多个，而且 Java 中现有的调度器也可以被直接重用。







# 线程安全🌈

## 线程安全的概念

一个严谨的定义：当多个线程访问一个对象时，如果不用考虑这些线程在运行时环境下的调度和交替执行，也不需要进行额外的同步，或者在调用方进行任何其他的协调操作，调用这个对象的行为都可以得到正确的结果，那这个对象就是线程安全的。

线程安全的代码具备一个特征：代码本身封装了必要的保证手段，令调用者无需关心并发一致性问题。



## 线程安全的等级

线程安全并不是非真既假的二元排他选项，可以按照安全程度分成五档。

### 不可变

不可变的对象一定是线程安全的，不需要再采取任何的线程安全保障措施。只要一个不可变的对象被正确地构建出来，永远也不会看到它在多个线程之中处于不一致的状态，在多线程环境下，应当尽量使对象成为不可变，来满足线程安全。

不可变的类型：

- `final` 关键字修饰的基本数据类型
- `String`
- 枚举类型
- `Number` 部分子类，如 `Long` 和 `Double` 等数值包装类型，`BigInteger` 和 `BigDecimal` 等大数据类型。



### 绝对线程安全

绝对线程安全的类即满足线程安全定义的类，是一个比较严格的范畴。

在 java 中标注是线程安全的类，大多数都不是绝对线程安全，而是相对线程安全。



### 相对线程安全

相对线程安全即时通常意义上的线程安全，调用时无需做额外的保障措施，但是对于一些特定顺序的连续调用，就可能需要在调用端使用额外的同步手段保证调用的正确性。

java中大多数线程安全类都是这种类型，例如 `Vector` 、 `HashTable` 、 `Colletions` 的 `synchronizedCollection` 方法包装的集合等。



### 线程兼容

线程兼容是指对象本身不是线程安全的，但是可以通过使用同步方法保证对象在并发环境中可以安全使用。

java中大多数类都是线程兼容的，例如 `ArrayList` 、 `HashMap` 等



### 线程对立

线程对立指无论调用端是否采取了同步措施，都无法在多线程环境下实现线程安全，这通常是有害的。

例如 `Thread` 类的 `suspend()` 和 `resume()` 方法，目前这些方法已经被废弃



## 线程安全的实现方法

### 互斥同步（悲观锁）

同步是指在多个线程并发访问某个共享数据时，保证共享数据在同一个时刻只被一个线程使用，互斥则是实现同步的一种手段，操作系统中临界区（Critical Section）、互斥量 （Mutex）和信号量（Semaphore）都是常见的互斥实现方式。

Java 中最基本的互斥实现手段是 `synchronized` 关键字

互斥同步最主要的问题是进行线程阻塞和唤醒带来的性能问题，因为 java 的线程是映射到操作系统的，阻塞或者唤醒线程需要从频繁在用户态和内核态之间切换，非常消耗性能。

互斥同步，实际上是一种悲观的并发策略，因为它总是认为如果不去做正确的同步措施，就肯定会出现并发一致性问题，所以无论数据是否真的存在竞争，它都要进行加锁、用户态内核态转换、维护锁计数器、检查是否有被阻塞的线程需要被唤醒等操作。

悲观锁的类型：

- 可重入锁（*synchronized*、*ReentrantLock*）
- 不可重入锁（自旋锁、自适应自旋锁）



### 非阻塞同步（乐观锁）

随着硬件指令集的发展，可以实现基于冲突检测的乐观并发策略：先进行操作，如果没有其它线程争用共享数据，那操作就成功了，否则采取补偿措施（不断地重试，直到成功为止）。

这种乐观的并发策略的许多实现都不需要将线程阻塞，因此这种同步操作称为「非阻塞同步」。

乐观锁需要 "操作" 和 "冲突检测" 这两个步骤具备原子性，但这里不能靠悲观锁实现，只能靠硬件的指令集实现，如果不是原子操作，则仍存在并发一致性问题，乐观锁的底层是基于 CAS 操作的。

乐观锁的类型：

- 轻量级锁
- 偏向锁



## 可重入与不可重入锁

### 可重入锁

可重入就是说某个线程已经获得某个锁，可以再次获取锁而不会出现死锁，也就是自己可以再次获取自己的内部锁。

比如一个线程获得了某个对象的锁，此时这个对象锁还没有释放，当其再次想要获取这个对象的锁的时候还是可以获取的，如果不可锁重入的话，就会造成死锁。同一个线程每次获取锁，锁的计数器都自增1，所以要等到锁的计数器下降为0时才能释放锁。

可重入锁有以下实现：

- JVM 实现的 `synchronized` 关键字
- JDK 实现的 `ReentrantLock` 类以及其他相关类

`ReentrantLock` 和 `synchronized` 很相似，只是增加了一些高级功能，例如等待可中断、可实现公平锁、锁可绑定多个条件

- 等待可中断：当持有锁的线程长期不释放锁的时候，正在等待的线程可以选择放弃等待，改为处理其他事情
- 公平锁：多个线程在等待一个线程时，必须按照申请锁的时间顺序来依次获取锁
- 锁绑定多个条件：一个 `ReentrantLock` 对象可以绑定多个 `Condition` 对象



### 不可重入锁

不可重入与可重入相反，不可递归调用，递归调用就会发生死锁。

自旋锁和自适应自旋锁就是一种不可重入锁





# synchronized🌈

*synchronized* 的作用主要有三个：

- 原子性：确保线程互斥的访问同步代码

- 可见性：保证共享变量的修改能够及时可见，依靠内存模型的中规则实现（lock、unlock）
- 有序性：防止指令重排序

这正好对应了内存模型的三大特性



特性：

- 被 *synchronized* 修饰的同步块对同一条线程来说是可重入的。这意味着同一线程反复进入同步块也不会出现自己把自己锁死的情况。
- 被 *synchronized* 修饰的同步块在持有锁的线程执行完毕并释放锁之前，会无条件地阻塞后面其他线程的进入，这意味着无法像处理某些数据库中的锁那样，强制已获取锁的线程释放锁，也无法强制正在等待锁的线程中断等待或超时退出。



## JVM实现

*synchronized* 在 *JVM* 层面上的实现是基于进入和退出 monitor 对象来实现方法同步和代码块同步。

使用 *synchronized* 实现同步的基础： Java 中每一个对象都可以作为锁

- 对于同步方法，锁对象是当前的实例对象。

- 对于静态同步方法，锁对象是当前类的 `Class` 对象

- 对于同步方法块，锁对象是 *synchronized* 括号里配置的对象



### 锁对象的类型

#### 对象锁

当前对象所有的「非静态同步方法」用的锁都是：当前实例对象本身

类的对象实例可以有很多个，不同对象实例的对象锁是互不干扰的。

一个对象里面如果有多个 *synchronized* 方法，某ー个时刻内，只要有一个线程去调用其中一个 *synchronized* 方法，其它的线程都只能等待，换句话说，某一个时刻内，只能有ー个线程去访问 *synchronized* 方法的当前对象 *this*，被锁定后，其它的线程都不能进入到当前对象的其它 *synchronized* 方法

所以， *synchronized* 锁的不是当前方法，而是当前方法所在的资源类。



#### 类锁

所有的「静态同步方法」用的是同一把锁：类对象本身

「非静态同步方法」和「静态同步方法」之间用的不是用一类型的锁，所以不会相互影响。

每个类只有一个类锁，一个「静态同步方法」获取锁后，其他的「静态同步方法」必须等待其释放锁，才能再获取锁。



### 实现流程

*synchronized* 在 *JVM* 里的实现都是基于进入和退出 monitor 对象来实现方法同步和代码块同步，虽然具体实现细节不一样，但是都可以通过成对的 `monitorEnter` 和 `monitorExit` 指令来实现。

- `MonitorEnter`：插入在同步代码块的开始位置，当代码执行到该指令时，将会尝试获取该对象 Monitor 的所有权，即尝试获得该对象的锁；
- `MonitorExit`：插入在方法结束处和异常处，JVM 保证每个 MonitorEnter 必须有对应的 MonitorExit；



JVM 是通过进入、退出管程来实现对方法、同步块的同步的，而管程的本质依赖于底层操作系统的互斥原语（Mutex Lock）实现。



#### 方法级的同步

方法级的同步无需通过字节码指令来控制，它实现在方法调用和返回操作之中。

*JVM* 可以从方法常量池中的方法表结构（method_info Structure）中的 `ACC_SYNCHRONIZED` 访问标志来区分一个方法是否同步方法。

当方法调用时，调用指令将会检查方法的 `ACC_SYNCHRONIZED` 访问标志是否被设置，如果设置了，执行线程将先获取 Monitor， 然后再执行方法，最后在方法完成时释放 Monitor。



#### 代码块的同步

*synchronized* 关键字经过 javac 编译后，会在同步块的前后分别形成 `monitorenter` 和 `monitorexit` 两个字节码指令，至两个指令都需要一个 reference 类型的参数来指明要锁定和解锁的对象。

执行 `monitorenter` 指令时，会尝试获取 Monitor 对象的所有权，即获取对象的锁，把锁的计数器加一；

执行 `monitorexit` 指令时，会将锁的计数器减一，当计数器为零时，锁就被释放了。如果获取 monitor 对象失败，该线程则会进入阻塞状态，直到其他线程释放锁。





## 操作系统实现

`synchronized` 在操作系统层面上的实现依赖于特殊的 CPU 指令。



### Monitor

Java 中的 `Lock + Condition` 和 `synchronized` 都是基于管程模型（Monitor）实现的，Monitor 来自 `ObjectMonitor`，由 C++ 编写。

Monitor 被称为管程，是通过管理共享变量访问过程，保证共享变量线程安全的一种机制，这种机制是通过线程之间访问共享资源的互斥特性来达到线程安全的目的，保持互斥的方式也就是我们常说的 “锁” ，Monitor 实现临界区互斥的方式实际上是通过操作系统的信号量（互斥量）来实现的。

任何一个 Java 对象都有一个 Monitor 与之关联，所以 Monitor 也被称为 “对象监视器”，当使用 `synchronized` 获取锁时，Java 对象的 Mark Word 中的指针`ptr_to_heavyweight_monitor` 指向操作系统提供的 Monitor 对象。



### 数据结构

```c++
ObjectMonitor() {
    _header       = NULL;
    _count        = 0;  // 记录
    _waiters      = 0,
    _recursions   = 0;  // 重入次数
    _object       = NULL;
    _owner        = NULL;  // 指向拥有锁的线程
    _WaitSet      = NULL;  // 等待队列，处于 wait 状态的线程，会被加入到_WaitSet
    _WaitSetLock  = 0 ;
    _Responsible  = NULL ;
    _succ         = NULL ;
    _cxq          = NULL ;
    FreeNext      = NULL ;
    _EntryList    = NULL ; // 阻塞队列，处于等待锁释放，即 block 状态的线程，会被加入到该列表
    _SpinFreq     = 0 ;
    _SpinClock    = 0 ;
    OwnerIsThread = 0 ;
  }
```



`ObjectMonitor` 中有两个队列，*_WaitSet* 和 *_EntryList*，用来保存 `ObjectWaiter` 对象列表，每个等待锁的线程都会被封装成 ObjectWaiter 对象，*_owner* 指向持有 ObjectMonitor 对象的线程，当多个线程同时访问一段同步代码时：

- 首先会进入 *_EntryList* 集合，当线程获取到对象的 Monitor 后，把 Monitor 中的 *_owner* 变量设置为当前线程，同时 Monitor 中的计数器 *_count* 加一。
- 若当前线程调用 `wait()` 方法主动让出 CPU 时间，将释放当前持有的 Monitor，*_owner* 变量恢复为 null，count--，该线程进入 *_WaitSet* 集合中等待被唤醒。
- 若当前线程执行完 CPU 分配的时间片，也会释放 Monitor ，同时 *_count* 减一 ，以便其他线程进入获取 Monitor，该线程进入 *_WaitSet* 集合中等待被唤醒。

<img src="http://store.secretcamp.cn/uPic/image-20210717152701170202107171527011626506821B8MBTfB8MBTf.png" alt="image-20210717152701170" style="zoom:50%;" />



### 实现流程

从上文可知，互斥同步是通过 `monitorenter` 和 `monitorexit` 两个字节码指令来展开的。

当执行 `monitorenter` 指令时，当前线程将试图获取锁对象所对应的 Monitor 的持有权，当 Monitor 的进入计数器为 0，那线程可以成功取得 Monitor，并将计数器值设置为 1，取锁成功。

如果当前线程已经拥有锁对象的 Monitor 的持有权，那它可以重入这个 Monitor，重入时计数器的值也会加 1。如果其他线程已经拥有锁对象的 Monitor 的所有权，那当前线程将被阻塞，直到正在执行线程执行完毕，即 `monitorexit` 指令被执行，执行线程将释放 Monitor 并设置计数器值为 0 ，其他线程将有机会持有 Monitor 。

值得注意的是：编译器将会确保无论方法通过何种方式完成，方法中调用过的每条 `monitorenter` 指令都有执行其对应 `monitorexit` 指令，而无论这个方法是正常结束还是异常结束。为了保证在方法异常完成时 `monitorenter` 和 `monitorexit` 指令依然可以正确配对执行，编译器会自动产生一个异常处理器，这个异常处理器声明可处理所有的异常，它的目的就是用来执行 `monitorexit` 指令。从字节码中也可以看出多了一个 `monitorexit` 指令，它就是异常结束时被执行的释放 Monitor 的指令。

<img src="http://store.secretcamp.cn/uPic/image-20210717153735061202107171537351626507455wOeFBSwOeFBS.png" alt="image-20210717153735061" style="zoom:50%;" />





# CAS🌈

比较并交换（Compare-and-Swap，CAS）是一个原子操作，CAS指令需要有3个操作数，分别是内存地址 V、旧的预期值 A  和新值B 。当执行操作时，只有当内存地址 V 的值等于旧预期值 A，才将 V 的值更新为新值B，否则不更新。

系统底层进行 CAS 操作的时候，会判断当前系统处理器是否为多核 CPU，如果是则给 "总线" 加锁，同一时刻只有一个 CPU 可以对 "总线" 加锁，因此 CAS 的原子性是平台级别的，通过这种方式能够保证多核心下的线程安全。 

在 Java 中，`sun.misc.Unsafe` 类提供了硬件级别的原子操作来实现 CAS ， `java.util.concurrent` 包下的大量类都使用了CAS操作。

CAS 的主要应用：

- 实现乐观锁，例如轻量级锁、偏向锁
- 实现无锁并发操作，例如 `AtomicInteger` 、 `LongAdder` 、`Semaphore` ，这些类底层还是基于 AQS ，AQS 本身也是基于 CAS 操作。





## ABA问题

CAS 需要在操作值的时候检査下值有没有发生变化，如果没有发生变化则更新

但是如果一个值原来是 A，在 CAS 方法执行之前，被其它线程修改为了 B，然后又修改回了 A，那么 CAS 方法执行检査的时候会发现它的值没有发生变化，但是实际却变化了。这就是 CAS 的 ABA 问题。

解决 ABA 最简单的方案就是给值加一个修改版本号，每次值变化，都会修改它的版本号，CAS 操作时都去对比此版本号。J.U.C 包为了解决这个问题，提供了一个带有标记的原子引用类 `AtomicStampedReference`，它可以通过控制变量值的版本来保证 CAS 的正确性。



## Unsafe

*JDK 5* 之后，Java 类库中才开始使用 CAS 操作，该操作由 `sun.misc.Unsafe` 类里面的 `compareAndSwapInt()` 和 `compareAndSwapLong()` 之类的几个方法包装提供。

Java和 C++ 的一个重要区别就是 Java 无法直接操作一块内存区域，不能像 C++ 那样可以自己申请内存和释放内存。而  `Unsafe` 类提供了类似 C++ 手动管理内存的能力。







### getXXX

普通读写

```java
public native int getInt(Object o, long offset);

public native void putInt(Object o, long offset, int x);
```





### getXXXVolatile

volatile读写

```java
public native int getIntVolatile(Object o, long offset);

public native void putIntVolatile(Object o, long offset, int x);
```





### compareAndSwapXXX

compareAndSwapXXX 相关的方法就是借助 C 语言来调用 CPU 底层指令实现的

参数 o: 表示要操作的对象

参数 offset: 表示要操作象中属性地址的偏移量

参数 expected: 表示需要修改数据的期望的值

参数 x: 表示需要修改为的新值

```java
public final native boolean compareAndSwapInt(Object o, long offset,
                                              int expected,
                                              int x);
```



### getAndAdd/SetXXX

该方法同时只有一个线程能够执行，其他线程会进入忙自旋。

同一时刻只有一个线程能够完成 CAS 操作，其他线程执行到 A 的时候，v 已经变成了旧值，CAS 会返回 false，然后 dowhile 循环会一直执行。并发程度越高该方法的性能也就越低。

```java
public final int getAndAddInt(Object o, long offset, int delta) {
    int v;
    do {
        v = getIntVolatile(o, offset);
    } while (!compareAndSwapInt(o, offset, v, v + delta));  // A
    return v;
}

public final int getAndSetInt(Object o, long offset, int newValue) {
    int v;
    do {
        v = getIntVolatile(o, offset);
    } while (!compareAndSwapInt(o, offset, v, newValue));
    return v;
}
```





## AtomicInteger

场景：假设多个线程需要对一个变量不停的累加一，会出现并发一致性问题。在这个场景下，如果使用 `synchronized` ，则相当于让各个线程串行化。

这种场景可以使用 `AtomicInteger` 原子类，底层使用的是无锁化的CAS机制。





## AtomicStampedReference

`AtomicStampedReference` 通过引入版本号机制，可以避免 ABA 问题

```java
public boolean compareAndSet(V   expectedReference,
                             V   newReference,
                             int expectedStamp,
                             int newStamp) {
    Pair<V> current = pair;
    return
        expectedReference == current.reference &&
        expectedStamp == current.stamp &&
        ((newReference == current.reference &&
          newStamp == current.stamp) ||
         casPair(current, Pair.of(newReference, newStamp)));
}
```



## LongAdder

`AtomicInteger` 实际上也存在性能问题，如果大量的线程同时并发修改一个 `AtomicInteger`，可能有很多线程会不停的自旋，进入一个无限重复的循环中。

这些线程不停地获取值，然后发起 CAS 操作，但是发现这个值被别的线程改过了，于是再次进入下一个循环，获取值，发起 CAS 操作又失败了，再次进入下一个循环。在大量线程高并发更新 `AtomicInteger` 的时候，这种问题可能会比较明显，导致大量线程空循环，自旋转，性能和效率都不是特别好。

Java 8 推出了一个新的类 `LongAdder` ，它尝试使用分段 CAS 以及自动分段迁移的方式来大幅度提升多线程高并发执行CAS操作的性能。

<img src="http://store.secretcamp.cn/uPic/image-20210326183543953202103261835441616754944Iae1WsIae1Ws.png" alt="image-20210326183543953" style="zoom:50%;" />

在 `LongAdder` 的底层实现中，首先有一个 base 值，刚开始多线程来不停的累加数值，都是对 base 进行累加的，接着如果发现并发更新的线程数量过多，就会开始施行「分段CAS」的机制，也就是内部会搞一个Cell数组，每个数组是一个数值分段。

让大量的线程分别去对不同 Cell 内部的 value 进行 CAS 累加操作，这样就把 CAS 的计算压力分散到了不同的 Cell  分段中了，就可以大幅度的降低多线程并发更新同一个数值时出现的无限循环的问题。

同时，`LongAdder` 内部实现了自动分段迁移的机制，也就是如果某个 Cell 的 value 执行 CAS 失败了，那么就会自动去找另外一个 Cell 分段内的 value 值进行 CAS 操作。

最后，会把 base 和所有 Cell 分段数值累加起来返回。













# AQS🌈

AQS（AbstractQueuedSynchronizer）指抽象的队列式同步器，是除了 java 虚拟机实现的 `synchronized` 关键字之外的锁机制，这个类在 `java.util.concurrent.locks` 包中。

AQS 面向的是锁的实现者，它简化了锁的实现方式，屏蔽了同步状态管理、线程的排队、等待与唤醒等底层操作。

AQS 对象内部有一个核心的 int 类型变量叫做 `state`，代表了加锁的状态，整 个AQS 的源码既是围绕 `state` 的获取和修改。

`state` 可以通过 `getState()` ，`setState() `以及 `compareAndSetState()` 等 protected 类型方法来操作。`state` 在不同的实现类中有不同的含义。例如 `ReentrantLock` 用它来表示所有者线程重复获取改锁的次数，`Semphore` 用它来表示剩余的许可数量，`FutureTask` 用它来表示任务的状态。

```java
private volatile int state;
```



另外，AQS 内部还有一个关键变量，用来记录当前加锁的是哪个线程，初始化状态下，这个变量是 null。

AQS的核心思想：如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，并将共享资源设置为锁定状态，如果被请求的共享资源被占用，那么就需要一套线程阻塞等待以及被唤醒时锁分配的机制，这个机制 AQS 是用 CLH 队列锁实现的，即将暂时获取不到锁的线程加入到队列中。

> CLH（Craig，sLandin，and Hagersten）队列是一个虚拟的双向队列，虚拟的双向队列即不存在队列实例，仅存在节点之间的关联关系。AQS 是将每一条请求共享资源的线程封装成一个CLH锁队列的一个结点（Node），来实现锁的分配。



## 资源共享方式的实现

AQS 定义了两种资源共享方式：

- 独占锁（Exclusive）：在同一时刻只能有一个线程获取到锁，其他获取锁的线程只能 处于同步队列中等待，如 `ReentrantLock`

- 共享锁（Share）：多在同一时刻允许多个线程获取到锁，如 `Semaphore` 、`CountDownLatch` 、 `ReadWriteLock` 、`CyclicBarrier`

AQS 也支持自定义同步器同时实现独占和共享两种方式，如 `ReentrantReadWriteLock`



对于 `ReentrantLock` ，并不是直接使用了AQS，而是使用了继承自 AQS 的自定义同步器，例如 `Sync`

事实上，AQS 底层使用了模板方法的设计模式，对于不同的锁实现，可以用不同的自定义同步器继承 AQS，并重写指定的方法，随后将同步器组合在自定义同步组件的实现中，并调用同步器提供的模板方法，而这些模板方法将会调用使用者重写的方法。

1. 使用者继承 `AbstractQueuedSynchronizer` 并重写指定的方法。

   主要是对于共享资源 state 的获取和释放，自定义同步器在实现的时候只需要实现共享资源 state 的获取和释放方式即可，至于具体线程等待队列的维护，AQS 已经在顶层实现好了。

2. 将 AQS 组合在自定义同步组件的实现中，并调用其模板方法，而这些模板方法会调用使用者重写的方法。



自定义同步器实现的时候主要重写下面几种 protected 类型的方法：

- `boolean tryAcquire(int)`：独占方式尝试获取资源，成功则返回 true，失败则返回 false

- `boolean tryRelease(int)`：独占方式尝试释放资源，成功则返回 true，失败则返回 false

- `int tryAcquireShared(int)`：共享方式尝试获取资源。负数表示失败；0 表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。

- `boolean tryReleaseShared(int)`：共享方式尝试释放资源，如果释放后允许唤醒后续等待结点返回true，否则返回false。

- `boolean isHeldExclusively()`：该线程是否正在独占资源。



同步器提供的模板方法基本上分为 3 类：

1. 独占式获取与释放同步状态：

   - `void acquire(int arg)`：独占式获取同步状态

   - `void acquireInterruptibly(int arg)`：独占式获取同步状态，响应中断

   - `boolean tryAcquireNanos(int arg, long nanosTimeout) `：独占式获取同步状态，响应中断，增加超时限制
   - `boolean release(int arg)` ：独占式的释放同步状态

2. 共享式获取与释放 同步状态

   - `void acquireShared(int arg)`：共享式获取同步状态
   - `void acquireSharedInterruptibly(int arg) `：共享式获取同步状态，响应中断
   - `boolean tryAcquireSharedNanos(int arg, long nanosTimeout)` ：共享式获取同步状态，响应中断，增加超时限制
   - `boolean releaseShared(int arg)` ：共享式的释放同步状态

3. 查询同步队列中的等待线程情况

   - `Collection<Thread> getQueuedThreads()`：获取等待在同步队列上的线程集合







## AQS的结构

### Node

```java
static final class Node {
    /**
     * 节点在共享状态下等待的标记
     */
    static final Node SHARED = new Node();
    /**
     * 节点在独占状态下等待的标记
     */
    static final Node EXCLUSIVE = null;

    /**
     * 因超时或者中断节点处于取消状态，取消状态的节点线程不再参与锁的竞争
     */
    static final int CANCELLED = 1;
    /**
     * 该节点的后继节点处于等待状态，当该节点的线程释放了锁后，将会唤醒后继节点的线程
     */
    static final int SIGNAL = -1;
    /**
     * 该节点的线程等待在condition队列上，其他线程调用了condition的唤醒方法后，该节点会进入同步队列
     */
    static final int CONDITION = -2;
    /**
     * 同步状态的获取将会无条件的传递下去
     */
    static final int PROPAGATE = -3;
    /**
     * 当前节点的等待状态
     */  
    volatile int waitStatus;
}
```



### 同步队列

AQS 依赖内部的同步队列来完成同步状态的管理，该同步队列是一个 FIFO 的双端队列，通过链表结构实现，链表元素即 Node 。

当前线程获取同步状态失败时，同步器会将当前线程以及等待状态等信息构造成为一个节点（Node）并将其加入同步队列，同时会阻塞当前线程，当同步状态释放时，会把首节点中的线程唤醒，使其再次尝试获取同步状态。

同步队列中的节点（Node）用来保存获取同步状态失败的线程引用、等待状态以及前驱和 后继节点，节点的属性类型

节点是构成同步队列的基础，同步队列拥有首节点（head）和尾节点（tail），没有成功获取同步状态的线程将会成为节点加入该队列的尾部，该双向链表的结构如下所示：

<img src="http://store.secretcamp.cn/uPic/image-202107172355349712021071723553516265373350nlH7M0nlH7M.png" alt="image-20210717235534971" style="zoom:45%;" />

同步器将节点加入到同步队列的过程：

<img src="http://store.secretcamp.cn/uPic/image-20210717235828406202107172358281626537508rT3EfprT3Efp.png" alt="image-20210717235828406" style="zoom:50%;" />

首节点是获取同步状态成功的节点，首节点的线程在释放同步状态时，将会唤醒后继节点，而后继节点将会在获取同步状态成功时将自己设置为首节点

<img src="http://store.secretcamp.cn/uPic/image-20210717235949403202107172359491626537589iNCoiQiNCoiQ.png" alt="image-20210717235949403" style="zoom:45%;" />







## 独占式同步状态获取与释放

### 获取同步状态

通过调用同步器的 `acquire(int arg)` 方法可以获取同步状态，该方法对中断不敏感，也就是由于线程获取同步状态失败后进入同步队列中，后续对线程进行中断操作时，线程不会从同步队列中移出。

1. 首先调用自定义同步器实现的 `tryAcquire(int arg)` 方法，该方法保证线程安全的获取同步状态
2. 如果同步状态获取失败，则构造同步节点  `Node.EXCLUSIVE` 并通过  `addWaiter(Node node)` 方法将该节点加入到同步队列的尾部
3. 最后调用 `acquireQueued(Node node,int arg)` 方法，使得该节点以 "死循环" 的方式获取同步状态。如果获取不到则阻塞节点中的线程，而被阻塞线程的唤醒主要依靠 "前驱节点的出队" 或 "阻塞线程被中断" 来实现。

```java
/**
 * 主要完成了同步状态获取、节点构造、加入同步队列以及在同步队列中自旋等待的相关工作
 */
public final void acquire(int arg) {
    if (!tryAcquire(arg) && acquireQueued(addWaiter(Node.EXCLUSIVE), arg)) {
    		selfInterrupt();  
    }
}
```



#### tryAcquire

 AQS 本身没有实现它，由各个子类实现，主要作用是返回获取同步器状态的结果，如果获取成功则返回 true，获取失败则返回 false 

以 `ReentrantLock` 为例，非公平锁调用 `NoFairSync.tryAcquire()`  ，公平锁调用 `FairSync.tryAcquire()`



#### addWaiter

`addWaiter() `  通过 CAS 线程安全的将节点添加到队列尾部，如果失败，则调用 `enq()` 方法，此方法采用死循环的方式确保成功将当前线程的节点添加至队列尾部。

最后返回添加完成的节点。

```java
private Node addWaiter(Node mode) {
    // 创建一个新节点
    Node node = new Node(Thread.currentThread(), mode);
    Node pred = tail;
    if (pred != null) {
        node.prev = pred;
        // 确保节点被线程安全的添加到尾部
        if (compareAndSetTail(pred, node)) {
            pred.next = node;
            return node;
        }
    }
    enq(node);
    return node;
}

private Node enq(final Node node) {
    for (; ; ) {
        Node t = tail;
        // 初始化，当 tail 节点不存在时，head 自然也不存在
        if (t == null) { 
            if (compareAndSetHead(new Node()))
                tail = head;
        }
        // 通过CAS添加到尾部
        else {
            node.prev = t;
            if (compareAndSetTail(t, node)) {
                t.next = node;
                return t;
            }
        }
    }
}
```



#### acquireQueued

通过死循环的方式获取同步器状态，节点进入同步队列之后，就进入了一个自旋的过程，只有条件满足，获取到了同步状态，才可以从这个自旋过程中退出。

该方法不断尝试获取同步器状态，并且在循环的过程中修改等待队列中的节点状态。

```java
final boolean acquireQueued(final Node node, int arg) {
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (; ; ) {
            // 前驱节点 p
            final Node p = node.predecessor();
            // 如果前驱节点 p 是头节点，则再次尝试获取锁，如果成功，则将当前节点设置为头节点
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return interrupted;
            }
            // 如果前驱节点不是头节点或者获取锁失败，判断是否要挂起线程
            if (shouldParkAfterFailedAcquire(p, node) && parkAndCheckInterrupt()) {
                interrupted = true;
            }    
        }
    }
    finally {
        if (failed)
            cancelAcquire(node);
    }
}
```





*shouldParkAfterFailedAcquire* -> 获取同步失败后是否阻塞线程

1. 查看前驱节点的状态，如果前驱节点是 *SIGNAL* 则返回 true，由 `parkAndCheckInterrupt()` 方法挂起 Node 的线程。

2. 删除 *CANCLLED* 状态的节点，

3. 通过 CAS 的方式修改前驱节点的状态为 *SIGNAL*

```java
private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
    // 获取前驱节点的等待状态
    int ws = pred.waitStatus;
    // 如果前驱节点的状态为SIGNAL，则表示前驱节点释放锁后会通知后继节点，则当前节点的线程可以安心挂起等待通知了
    if (ws == Node.SIGNAL)
        return true;
    if (ws > 0) {
				// 把CANCLLED状态的节点移出队列
        do {
            node.prev = pred = pred.prev;
        }
        while (pred.waitStatus > 0);
        pred.next = node;
    }
    // 其他情况下则通过CAS的方式修改前驱节点的状态为SIGNAL
    else {
        compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
    }
    return false;
}
```



`LockSupport.park(this)` 执行后，当前线程会被阻塞，直到被 head 节点唤醒，之后返回中断标识，继续进入 for 循环中尝试获取同步器状态。

```java
private final boolean parkAndCheckInterrupt() {
    LockSupport.park(this);
    return Thread.interrupted();
}
```



### 释放同步状态

`release()` 方法用于释放独占式同步状态

```java
public final boolean release(int arg) {
    if (tryRelease(arg)) {
        Node h = head;
        if (h != null && h.waitStatus != 0)
            unparkSuccessor(h);
        return true;
    }
    return false;
}
```



#### tryRelease

 AQS 本身没有实现它，由各个子类实现，主要作用是释放同步状态，如果释放成功则返回 true，获取失败则返回 false



#### unparkSuccessor

```java
private void unparkSuccessor(Node node) {
		// head节点的状态
    int ws = node.waitStatus;
    if (ws < 0) compareAndSetWaitStatus(node, ws, 0);
		
    // 后继节点
    Node s = node.next;
    // 如果后继节点为空或者状态是CANCLLED
    if (s == null || s.waitStatus > 0) {
        s = null;
        // 从尾部向前遍历，直到找到最后一个状态不为CANCLLED的节点
        for (Node t = tail; t != null && t != node; t = t.prev)
            if (t.waitStatus <= 0)
                s = t;
    }
  	// 唤醒节点中的线程
    if (s != null)
        LockSupport.unpark(s.thread);
}
```





### 总结

- head 节点释放同步状态后会唤醒后继节点
- head 的后继节点被阻塞，等待 head 节点唤醒，被唤醒后尝试获取同步，成功后将自己设置为 head 节点
- 其他节点在自旋中不断尝试用 CAS 将前驱节点的状态为 *SIGNAL* ，这样就产生了连锁效应，在下一次 for 循环的时候回将当前节点阻塞
- 从整个同步队列的角度看，阻塞行为从队首传播到队尾，唤醒行为也是从队首传播到队尾
- 节点被唤醒之后会再进入 for 循环执行 `tryAcquire()` 方法争抢锁，因为新线程会执行 `acquire()` 方法，也会 `tryAcquire()` 争抢锁，所以被唤醒的节点不一定就能抢到锁，如果是公平锁，则能保证被唤醒的线程一定能抢到锁。
- 一个线程只要不在同步队列中，那就可以理解为新线程，比如已经执行过同步的线程被移出了同步队列，那它之后会再次尝试争抢锁，抢不到就入队，这样的过程一直反复。

![](http://store.secretcamp.cn/uPic/AQS%20%E7%8B%AC%E5%8D%A0%E5%BC%8F202107181445011626590701Rz72gvRz72gv.png)







## 共享式同步状态获取与释放

### 同步状态获取

`acquireShared()` 方法可以共享式地获取同步状态

```java
public final void acquireShared(int arg) {
    // 尝试获取同步状态，大于等于0时表示获取到了同步状态
    if (tryAcquireShared(arg) < 0)
        doAcquireShared(arg);
}
```



#### doAcquireShared

`tryAcquireShared(int arg)` 方法返回值为 int 类型，当返回值大于等于0 时，表示能够获取到同步状态，即退出自旋的条件。

```java
private void doAcquireShared(int arg) {
    final Node node = addWaiter(Node.SHARED);
    boolean failed = true;
    try {
        boolean interrupted = false;
        /**
         * 自旋
         * 退出条件是tryAcquireShared()方法的返回值大于等于0
         */
        for (; ; ) {
            // 前驱节点
            final Node p = node.predecessor();
            if (p == head) {
                // 退出自旋的条件是返回值大于等于0
                int r = tryAcquireShared(arg);
                if (r >= 0) {
                    // 设置队列的头节点以及传播
                    setHeadAndPropagate(node, r);
                    p.next = null; // help GC
                    if (interrupted) selfInterrupt();
                    failed = false;
                    return;
                }
            }
            if (shouldParkAfterFailedAcquire(p, node) && parkAndCheckInterrupt())
                interrupted = true;
        }
    }
    finally {
        if (failed)
            cancelAcquire(node);
    }
}
```



#### setHeadAndPropagate

`setHeadAndPropagate()` 方法也调用了 `setHead()` 方法将当前线程节点修改为 head 节点，但是其多了一步操作：在满足一定条件的情况下将执行 `doReleaseShared()` 修改头节点的状态唤醒后继节点线程。

共享模式是自己拿到了同步器状态还会尝试会唤醒后继的节点，独占模式只保证自己获取同步器状态，只有在释放的时候才回去唤醒后继节点。

```java
/**
 * node是获取到了同步状态的节点
 */
private void setHeadAndPropagate(Node node, int propagate) {
    Node h = head;
    // 将node设为头节点
    setHead(node);
    /**
     *  propagate>0 表示允许获取同步器状态
     *  h == null：设置node为头节点之前头节点为空的情况
     *  h.waitStatus < 0  状态不为CANCEL
     *  (h = head) == null || h.waitStatus < 0)：新head节点为空或者为CANCEL
     */
    if (propagate > 0 || h == null || h.waitStatus < 0 ||
            (h = head) == null || h.waitStatus < 0) {
        // 获取当前节点的后继节点
        Node s = node.next;
        if (s == null || s.isShared())
            // 共享模式下修改头节点状态并唤醒后继线程
            doReleaseShared();
    }
}
```



### 同步状态释放

`releaseShared()` 方法在释放同步状态之后，将会唤醒后续处于等待状态的节点。

对于能够支持多个线程同时访问的并发组件（比如 `Semaphore`），它和独占式主要区别在于 `tryReleaseShared(int arg)` 方法必须确保同步状态（或者资源数）线程安全释放，一般是通过循环和 CAS 来保证的，因为释放同步状态的操作会同时来自多个线程。

```java
public final boolean releaseShared(int arg) {
    if (tryReleaseShared(arg)) {
        doReleaseShared();
        return true;
    }
    return false;
}
```



#### tryReleaseShared

AQS 本身没有实现它，由各个子类实现，主要作用是释放共享资源，如果获取成功则返回 true，获取失败则返回 false 。





#### doReleaseShared

```java
private void doReleaseShared() {
    for (; ; ) {
        Node h = head;
        if (h != null && h != tail) {
            int ws = h.waitStatus;
            if (ws == Node.SIGNAL) {
                // 将当前节点状态从SIGNAL修改为0，CAS失败则continue，等下一个循环继续开搞
                if (!compareAndSetWaitStatus(h, Node.SIGNAL, 0))
                    continue;            // loop to recheck cases
                // 唤醒后继节点
                unparkSuccessor(h);
            }
            // 将当前节点状态从0修改为PROPAGATE，CAS失败则continue
            else if (ws == 0 && !compareAndSetWaitStatus(h, 0, Node.PROPAGATE))
                continue;                // loop on failed CAS
        }
        // head节点不变，break；head节点改变（被唤醒的节点拿到了锁），继续循环唤醒过程，一直向队尾传播
        if (h == head)              
            break;
    }
}
```





## 读写锁







# Lock接口🌈

`Lock` 是一个接口，它定义了锁获取和释放的基本操作。

```JAVA
public interface Lock {

    /**
     * Acquires the lock.
     *
     */
    void lock();

    /**
     * Acquires the lock unless the current thread is
     * {@linkplain Thread#interrupt interrupted}.
     */
    void lockInterruptibly() throws InterruptedException;

    /**
     * Acquires the lock only if it is free at the time of invocation.
     */
    boolean tryLock();

    /**
     * Acquires the lock if it is free within the given waiting time and the
     * current thread has not been {@linkplain Thread#interrupt interrupted}.
     */
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;

    /**
     * Releases the lock.
     *
     */
    void unlock();

    /**
     * Returns a new {@link Condition} instance that is bound to this
     */
    Condition newCondition();
}

```





## ReentrantLock

`ReentrantLock` 是 `Lock` 接口最常见的一种实现，它与 `synchronized` 一样是可重入的，基本用法上也很相似。



### 可重入

可重入是指任意线程在获取到锁之后能够再次获取该锁而不会被该锁阻塞。

重入需要解决两个问题：

1. 线程再次获取锁：锁需要去识别获取锁的线程是否为当前占据锁的线程，如果是，则再次成功获取。

   ```java
   final boolean nonfairTryAcquire(int acquires) 
       // 获取当前线程
       final Thread current = Thread.currentThread();
       int c = getState();
   		// state=0 ，通过CAS自增，并设置锁的owner
       if (c == 0) {
           if (compareAndSetState(0, acquires)) {
               setExclusiveOwnerThread(current);
               return true;
           }
       }
       // 当前线程是锁的owner
       else if (current == getExclusiveOwnerThread()) {
           // state增加
           int nextc = c + acquires;
           if (nextc < 0) // overflow
               throw new Error("Maximum lock count exceeded");
           setState(nextc);
           return true;
       }
       return false;
   }
   ```

   

2. 锁的最终释放：线程重复 n 次获取了锁，随后在第 n 次释放该锁后，其他线程能够获取到该锁。

   ```java
   protected final boolean tryRelease(int releases) {
       // 还需要释放锁的次数
       int c = getState() - releases;
       // 判断当前线程是否是拥有锁的线程
       if (Thread.currentThread() != getExclusiveOwnerThread())
           throw new IllegalMonitorStateException();
       boolean free = false;
       // 只有释放一次锁后state为0，才是完全释放了锁，返回true
       if (c == 0) {
           free = true;
           setExclusiveOwnerThread(null);
       }
       setState(c);
       return free;
   }
   ```



### 公平锁

公平锁：是指多个线程在等待同一个锁时，必须按照申请锁的时间顺序来依次获得锁；而非公平锁则不保证这一点，在锁被释放时，任何一个等待锁的线程都有机会获得锁。`synchronized` 中的锁是非公平的，`ReentrantLock` 在默认情况下也是非公平的，但可以通过构造函数要求使用公平锁。

不过一旦使用了公平锁，将会导致 `ReentrantLock` 的性能急剧下降，会明显影响吞吐量。

公平性与否是针对获取锁而言的，如果一个锁是公平的，那么锁的获取顺序就应该符合请求的绝对时间顺序，也就是 FIFO。

相对非公平锁，多了一个判断条件 `!hasQueuedPredecessors()` ，该函数查看同步队列中当前是否有前驱节点，没有前驱结点的是否判断条件才为 true ，这保证了只有同步队列中的 head 节点才能去获取锁。

```java
protected final boolean tryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {
        /**
         * 相对非公平锁，多了一个判断条件 !hasQueuedPredecessors()
         */
        if (!hasQueuedPredecessors() &&
            compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    else if (current == getExclusiveOwnerThread()) {
        int nextc = c + acquires;
        if (nextc < 0)
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}
```



公平锁和同步队列 FIFO 的关系：

重点在于`acquire()` 方法

- 非公平锁：直接执行 `tryAcquire(arg)` ，新线程直接可以参与锁的争抢
- 公平锁：也可以直接执行 `tryAcquire(arg)` ，但由于公平锁的新增的判断条件，该方法返回值一定是 false，所以一定会进入后面的方法中，从而和同步队列一样拥有 FIFO 的特性。

所有，公平与否，针对的其实是被唤醒的线程与还未加入同步队列的线程，对于已经在同步队列中阻塞的线程而言，它们内部自身其实是公平的，因为它们是按顺序被唤醒的。

```java
public final void acquire(int arg) {
    if (!tryAcquire(arg) && acquireQueued(addWaiter(Node.EXCLUSIVE), arg)) {
    		selfInterrupt();  
    }
}
```



### 等待可中断

等待可中断：是指当持有锁的线程长期不释放锁的时候，正在等待的线程可以选择放弃等待，改为处理其他事情。





### 锁绑定多个条件

锁绑定多个条件：是指一个 `ReentrantLock` 对象可以同时绑定多个 `Condition` 对象。

在 `synchronized`  中，锁对象的 `wait()` 跟它的 `notify()` 或者 `notifyAll()` 方法配合可以实现一个隐含的条件，如果要和多于一个的条件关联的时候，就不得不额外添加一个锁；而 `ReentrantLock` 则无须这样做，多次调用 `newCondition()` 方法即可。







### 与synchronized比较

`ReentrantLock` 在功能上是 `synchronized` 的超集，性能上也持平，但 `synchronized` 仍有一些优点：

- `synchronized` 是在 Java 语法层面的同步，足够清晰，也足够简单。
- Lock 应该确保在 finally 块中释放锁，否则一旦受同步保护的代码块中抛出异常，则有可能永远不会释放持有的锁。这一点必须由程序员自己来保证，而使用 `synchronized` 可以由 Java 虚拟机来确保，即使出现异常，锁也能被自动释放。







# Condition接口🌈

任意一个 Java 对象，都拥有一组监视器方法，定义在 `java.lang.Object` 上，这些方法与 *synchronized* 关键字配合，可以实现 等待/通知 模式。`Condition` 接口也提供了类似 `Object` 的监视器方法，与 Lock 配合可以实现 等待/通知 模式，

```java
public interface Condition {

    /**
     * Causes the current thread to wait until it is signalled or
     * {@linkplain Thread#interrupt interrupted}.
     */
    void await() throws InterruptedException;

    /**
     * Causes the current thread to wait until it is signalled.
     */
    void awaitUninterruptibly();

    /**
     * Causes the current thread to wait until it is signalled or interrupted,
     * or the specified waiting time elapses.
     */
    long awaitNanos(long nanosTimeout) throws InterruptedException;

    /**
     *
     */
    boolean await(long time, TimeUnit unit) throws InterruptedException;

    /**
     * Causes the current thread to wait until it is signalled or interrupted,
     * or the specified deadline elapses.
     */
    boolean awaitUntil(Date deadline) throws InterruptedException;

    /**
     * Wakes up one waiting thread.
     */
    void signal();

    /**
     * Wakes up all waiting threads.
     *
     */
    void signalAll();
}

```





`Condition` 定义了 等待/通知 两种类型的方法，当前线程调用这些方法时，需要提前获取到 `Condition` 对象关联的锁。`Condition` 对象是由 `Lock` 对象创建出来的，换句话说，`Condition` 是依赖 `Lock` 对象的。





## 等待队列

`ConditionObject` 是 AQS 的内部类，其实现了 `Condition` 接口，每个 `Condition` 实例都能够访问同步器提供的方法，相当于每个 `Condition` 都拥有所属同步器的引用，同时每个 `Condition` 对象都包含着一个等待队列。

类似于 AQS 的同步队列，等待队列的节点也是 AQS 中定义的静态内部类 `Node` 



<img src="http://store.secretcamp.cn/uPic/image-202107181652322742021071816523216265983525TSAN95TSAN9.png" alt="image-20210718165232274" style="zoom:50%;" />







## 等待

`await()` 方法会使当前线程进入等待队列并释放锁，同时线程状态变为等待状态，相当于同步队列的 head 节点移动到 `Condition` 的等待队列中，然后唤醒同步队列中的后继节点。

<img src="http://store.secretcamp.cn/uPic/image-20210718170448743202107181704481626599088VcdOvZVcdOvZ.png" alt="image-20210718170448743" style="zoom:50%;" />



## 通知

调用 `Condition` 的 `signal()` 方法，将会唤醒在等待队列中等待时间最长的节点（head 节点），在唤醒节点之前，会将节点移到同步队列中。

`signalAll()` 方法，相当于对等待队列中的每个节点均执行一次 `signal()` 方法，效 果就是将等待队列中所有节点全部移动到同步队列中，并唤醒每个节点的线程。

<img src="http://store.secretcamp.cn/uPic/image-20210718170608474202107181706081626599168iayT3SiayT3S.png" alt="image-20210718170608474" style="zoom:50%;" />





# 锁优化🌈

锁优化技术是为了在线程之间更高效地共享数据，以及解决竞争问题，从而提高程序的执行效率



## 自旋与自适应自旋

在互斥同步中，对性能影响最大的是线程的阻塞与唤醒，但有时候共享数据的锁定只是持续很短的一段时间，为了这段时间去挂起和恢复线程是非常不值得的。如果有两个以上的线程并行执行，可以让后面请求锁的那个线程 “稍等一会”，但不放弃处理器的执行时间，看看持有锁的线程是否很 快就会释放锁。

自旋的思想是让请求锁的线程进行一段忙循环（自旋），看看持有锁的线程是否很快就会释放锁，从而避免线程切换。

尽管自旋等待本身避免了线程切换的开销，但是它仍然要占用 CPU 时间，因此，如果锁被占用的时间很短，那么自旋等待的效果就很好，反之，如果锁占用的时间很长，那自旋等待只会白白消耗 CPU 资源，造成浪费。

因此，自旋的次数必须是有一定限度的，默认的自旋次数是 10 次。



「自适应自旋」是指自旋的次数不再是默认的 10 次，而是由前一次在同一个锁上的自旋时间以及锁拥有者的状态动态决定。

简单的说，就是对于某个锁，如果上一次自旋等待成功了，那么虚拟机就会认为下一次自旋也很有可能成功，就会允许自旋等待更长的时间。如果自旋很少成功获得过锁，那么以后想要获得锁的时候就会直接省略自旋过程。



## 锁消除

锁消除是指虚拟机即时编译器在运行时，对一些代码要求同步，但是对被检测到不可能存在共享数据竞争的锁进行消除。

锁消除主要是通过 "逃逸分析" 来支持，如果堆上的共享数据不可能逃逸出去被其它线程访问到，那么就可以把它们当成私有数据对待，也就可以将它们的锁进行消除。

对于一些看起来没有加锁的代码，其实隐式的加了很多锁。例如下面的字符串拼接代码就隐式加了锁：

```java
public static String concatString(String s1, String s2, String s3) {
    return s1 + s2 + s3;
}
```

`String` 是一个不可变的类，编译器会对 `String` 的拼接自动优化。在 *JDK 1.5* 之前，会转化为 `StringBuffer` 对象的连续 `append` 操作：

```java
public static String concatString(String s1, String s2, String s3) {
    StringBuffer sb = new StringBuffer();
    sb.append(s1);
    sb.append(s2);
    sb.append(s3);
    return sb.toString();
}
```

每个 `append` 方法中都有一个同步块。虚拟机观察变量 sb，很快就会发现它的动态作用域被限制在 `concatString` 方法内部。也就是说，sb 的所有引用永远不会逃逸到 `concatString` 方法之外，其他线程无法访问到它，因此可以进行消除。



## 锁粗化

如果一系列的连续操作都对同一个对象反复加锁和解锁，频繁的加锁操作就会导致性能损耗。

上一节的示例代码中连续的 `append` 方法就属于这类情况。如果虚拟机探测到由这样的一串零碎的操作都对同一个对象加锁，将会把加锁的范围扩展（粗化）到整个操作序列的外部。

对于上一节的示例代码就是扩展到第一个 `append` 操作之前直至最后一个 `append` 操作之后，这样只需要加锁一次就可以了。



## 轻量级锁

轻量级锁是相对于传统锁机制而言的，传统锁机制被称为 “重量级锁”，轻量级锁的目的是为了在没有多线程竞争的前提下，减少重量级锁使用操作系统互斥量产生的性能消耗。

轻量级锁的思想是：当一个线程执行某个方法时，实际上这个方法很少有其他线程也在同时执行，所以执行方法时不需要再加锁，只需要做一个标记，用一个变量记录当前方法正在被执行；退出方法时，再将状态修改为当前方法没有被执行。相比起重量锁，轻量级锁的性能开销很小。

轻量级锁实现原理：

在代码进入同步块时，如果此对象没有被锁定 -> `0 01`  （指 Mark Word 中的后三位），虚拟机首先将在当前线程的栈帧中建立一个名为锁记录（Lock Record）的空间，用于存储锁对象目前的 Mark Word 拷贝，这个拷贝被称为 Displaced Mark Word ，然后虚拟机将使用 CAS 操作尝试将对象的 Mark Word 更新为指向 Lock Record 的指针。

CAS操作：

- 内存地址 V：对象的地址

- 旧的预期值 A：  Mark Word

- 新值B：栈帧中锁记录的地址

加锁：

- 如果这个更新动作成功，那么这个线程就拥有了该对象的轻量级锁，该对象 Mark Word 的锁标志位转为 -> `0 00` ；

- 如果这个更新动作失败，那就意味着至少存在一条线程与当前线程竞争获取该对象的锁。

  虚拟机首先会检查对象的 Mark Word 是否指向当前线程的栈帧，如果是，说明当前线程已经拥有了这个对象的锁，那直接进入同步块继续执行就可以了，否则就说明这个锁对象已经被其他线程抢占了，即并发下有其他线程先执行了 CAS 操作， Mark Word 的值已经变成了 *ptr_to_lock_record*（指向锁记录的指针） 。

  如果有两条以上的线程争用同一个锁，那轻量级锁就不再有效，需要膨胀成重量级锁，锁的标志位转为 -> `0 10` ，此时 Mark Word 中存储的是指向重量级锁 monitor 的指针。

<img src="http://store.secretcamp.cn/uPic/image-20210127194618064202101271946181611747978vGoaEYvGoaEY.png" alt="image-20210127194618064" style="zoom: 50%;" />

<img src="http://store.secretcamp.cn/uPic/image-20210127194647682202101271946471611748007kNHQAHkNHQAH.png" alt="image-20210127194647682" style="zoom:50%;" />

CAS 操作：

- 内存地址 V：对象的地址

- 旧的预期值 A： 栈帧中锁记录的地址

- 新值B：Displaced Mark Word

解锁：

如果对象的 Mark Word 仍然指向线程的 Lock Record ，那就用 CAS 操作把对象当前的 Mark Word 和线程中复制 Displaced Mark Word 替换回来。

- 如果替换成功，则实现了轻量级锁的解锁过程
- 如果替换失败，说明有其他线程尝试过获取该锁，该锁已经膨胀成了重量级锁，那就要在释放锁的同时，唤醒被挂起的线程



轻量级锁能提升程序性能的依据是 “对于绝大多数锁，在整个同步周期内都是不存在竞争的” ，如果没有竞争，轻量级锁使用 CAS 操作避免了线程切换的开销，但是如果存在锁竞争，除了线程切换的开销，还额外进行了 CAS 操作，因此在有竞争的情况下，轻量级锁比传统的重量级锁更慢。



## 偏向锁

轻量级锁是在无竞争的情况下使用 CAS 操作去消除同步使用的互斥量，而偏向锁的目的是在无竞争情况下将整个同步都消除掉，连 CAS 操作都不再需要。

偏向锁中的 “偏”，就是偏心的 “偏”，意思是这个锁会偏向于第一个获得它的线程，如果在接下来的执行过程中，该锁一直没有被其他的线程获取，则持有偏向锁的线程将永远不需要再进行同步。

获取锁的过程：

1. 检查 Mark Word 是否为可偏向锁的状态，即是否偏向锁即为 1 即表示支持可偏向锁，否则为 0 表示不支持可偏向锁
2. 如果是可偏向锁，则检查 Mark Word 储存的线程 id 是否为当前线程 id，如果是则执行同步块，否则执行步骤 3
3. 如果检查到 Mark Word 的 id 不是本线程的 id，则通过 CAS 操作去修改线程 id 修改成本线程的 id，如果修改成功则执行同步代码块，否则执行步骤 4 
4. 当拥有该锁的线程到达安全点之后，挂起这个线程，升级为轻量级锁。



锁释放的过程：

1. 偏向锁的释放并不是主动，而是被动的，指持有偏向锁的线程执行完同步代码后不会主动释放偏向锁，而是等待其他线程来竞争才会释放锁。
2. 撤销偏向锁的操作需要等到全局安全点（没有字节码正在执行）才会执行，暂停持有偏向锁的线程，同时检查该线程的状态，如果该线程是否处于同步代码块，若不在同步块中，则利用 CAS 尝试替换 Mark Word 中的 Thread ID，设置为无锁状态，允许其他线程竞争偏向锁，这被称为 “重偏向” 。若该线程处于同步块中，则撤销偏向，升级为轻量级锁。

偏向锁可以提高带有同步但无竞争的程序性能，但是如果程序中大多数的锁总是被多个不同的线程访问，那么偏向模式就是多余的。





## 锁膨胀的过程

1. 无锁  0 01
2. 偏向锁 1 01
3. 轻量级锁 0 00
4. 重量级锁 0 10

<img src="http://store.secretcamp.cn/uPic/image-20210717122144283202107171221441626495704eEJRbveEJRbv.png" alt="image-20210717122144283" style="zoom:50%;" />













