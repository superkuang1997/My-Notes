# Java线程类🌈

## 多线程创建流程

程序启动运行 `main` 方法的时候，java虚拟机启动一个进程，主线程 main 在 `main` 方法调用时候被创建。随着调用线程对象的 `start` 方法，另外一个新的线程也启动了，这样，整个程序就在多线程下运行。

每当 `main` 方法中执行新线程的 `start` 方法时，就会新开辟一块栈空间，在新的内存空间中执行线程对象的 `run` 方法。多线程执行时，在栈内存中，每一个执行线程都有一片自己私有的栈内存空间，进行方法的压栈和出栈。

当执行线程的任务结束了，线程自动在栈内存中释放了。但是当所有的执行线程都结束了，那么进程就结束了。



## 使用线程

Java使用 `java.lang.Thread` 类代表线程，所有的线程对象都必须是Thread类或其子类的实例。每个线程的作用是完成一定的任务，实际上就是执行一段程序流，即一段顺序执行的代码。java使用线程执行体来代表这段程序流。

Java中通过继承 `Thread` 类来创建并启动多线程的步骤如下：

1. 定义 `Thread` 类的子类，并重写该类的 `run` 方法，该 `run` 方法的方法体就代表了线程需要完成的任务，因此把 `run` 方法称为线程执行体。
2. 创建 `Thread` 子类的实例，即创建线程对象
3. 调用线程对象的 `start` 方法来启动该线程



有三种使用线程的方法：

- 继承 `Thread` 类

- 实现 `Runnable` 接口
- 实现 `Callable` 接口

实现 `Runnable` 和 `Callable` 接口的类只能当做一个可以在线程中运行的任务，不是真正意义上的线程，因此最后还需要通过 `Thread` 来调用，可以理解为任务是通过线程驱动从而执行的。



## 继承Thread类

构造方法：

- `public Thread()` :分配一个新的线程对象。
- `public Thread(String name)` :分配一个指定名字的新的线程对象。
- `public Thread(Runnable target)` :分配一个带有指定目标新的线程对象。
- `public Thread(Runnable target,String name)` :分配一个带有指定目标新的线程对象并指定名字。

常用方法：

- `public String getName()` :获取当前线程名称。
- `public void start()` ：此线程开始执行，Java虚拟机调用此线程的 `run` 方法
- `public void run()` ：此线程要执行的任务在此处定义代码
- `public static void sleep(long millis)`：使当前正在执行的线程以指定的毫秒数暂停（暂时停止执行）
- `public static Thread currentThread()`： 返回对当前正在执行的线程对象的引用。



测试类：

```java
public class demo {
    public static void main(String[] args) {
        MyThread mt1 = new MyThread();
        mt1.setName("第一个新线程");
        mt1.start();
        MyThread mt = new MyThread("第二个新线程");
        mt.start();
    }
}
```

线程类：

```java
// 自定义线程类
class MyThread extends Thread {
    // 无参的构造方法
    public MyThread() {}
    
	//调用父类的String参数的构造方法，指定线程的名称
    public MyThread(String name) {
        super(name);
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
}
```



## 实现Runnable接口

采用 ` java.lang.Runnable` 也是非常常见的一种，我们只需要重写 `run` 方法即可。

实现步骤：

1. 创建一个 `Runnable` 接口的实现类
2. 在实现类中重写 `Runnable` 接口的 `run` 方法，设置线程任务
3. 创建一个 `Runnable` 接口的实现类对象
4. 创建 `Thread` 类对象，构造方法中传递 `Runnable` 接口的实现类对象
5. 调用 `Thread` 类中的 `start` 方法，开启新的线程执行 `run` 方法



```java
public class demo_runnable {
    public static void main(String[] args) {
        TestRunnable runnable = new TestRunnable();
        Thread t = new Thread(runnable);
        t.start();
        for (int i = 0; i <100 ; i++) {
            System.out.println(Thread.currentThread().getName() + " ---> " + i);
        }
    }
}    
    
class TestRunnable implements Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println(Thread.currentThread().getName() + " ---> " + i);
        }
    }
}
```



匿名内部类创建线程

使用线程的匿名内部类方式，可以方便的实现每个线程执行不同的线程任务操作。

```java
public class TestLambda {
    public static void main(String[] args) {
        // 线程的父类是Thread，无需继承Thread类
        new Thread() {
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    System.out.println(Thread.currentThread().getName() + " -> " + i);
                }
            }
        }.start();
        
        // 创建接口方式，无需实现Runnable接口
        Runnable r = new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    System.out.println(Thread.currentThread().getName() + " ->" + i);
                }
            }
        };
        new Thread(r).start();
    }
}
```







## 实现Callable接口

与 `Runnable` 相比，`Callable` 可以有返回值，有返回值且通过 `FutureTask` 进行封装。

```java
public class createThread_Callable {
    public static void main(String[] args) throws Exception {
        CallableImpl myThread = new CallableImpl();
        FutureTask futureTask = new FutureTask(myThread);
        new Thread(futureTask, "A").start();
        System.out.println("main ... over");
        System.out.println(futureTask.get());
    }
}

class CallableImpl implements Callable<Integer> {

    @Override
    public Integer call() throws Exception {
        System.out.println("开始计算.......");
        TimeUnit.SECONDS.sleep(3);
        return 1024;
    }
}
```





##   三种方式对比

如果一个类继承 `Thread` ，则不适合资源共享。但是如果实现了 `Runable` 接口的话，则很容易的实现资源共享。

- 实现 `Runnable` 避免了单继承的局限性
  - 一个类只能继承一个类，类继承了 `Thread` 类就不能继承其他的类
  - 实现了 `Runnable` 接口，还可以继承其他的类，实现其他的接口

- 实现 `Runnable` 增强了程序的扩展性，降低了程序的耦合性
  - 实现 `Runnable` 接口的方式，把设置线程任务和开启新线程进行了分离
  - 实现类中，重写了 `run` 方法，用来设置线程任务
  - 创建 `Thread` 类对象，调用 `start` 方法，用来开启新的线程

- 线程池只能放入实现 `Runable` 或 `Callable` 类线程，不能直接放入继承 `Thread` 的类







# 线程安全问题🌈

## 共享数据访问

多线程访问了共享的数据，会产生线程安全问题

```java
public class MT_ThreadSafe {
    public static void main(String[] args) {
        Window window = new Window();
        Thread t0 = new Thread(window);
        Thread t1 = new Thread(window);
        Thread t2 = new Thread(window);

        t0.start();
        t1.start();
        t2.start();
    }
}

class Window implements Runnable {
    private int ticket = 500;

    @Override
    public void run() {
        while (true) {
            if (ticket > 0) {
                System.out.println(Thread.currentThread().getName() + " -> " + ticket);
                ticket--;
            } else {
                break;
            }
        }
    }
}
```



## 线程安全相关类

- 使用线程安全的集合类
  - List： `Vector` 、 `CopyOnWriteArrayList`
  - Set： `CopyOnWriteArraySet`
  - Map： `HashTable` 、`ConcurrentHashMap`



- 将线程不安全的集合转为线程安全的集合类

  将 `ArrayList` 转为线程安全的集合类：

  ```java
  Collections.synchronizedList(new ArrayList<>());
  ```



- 使用线程安全的方法
  - Java虚拟机实现的 `synchronized` 
  - 基于 AQS 实现的 `java.util.concurrent` 中的相关类





# 基础线程机制🌈

## Daemon

守护线程（Daemon Thread）是程序运行时在后台提供服务的线程，不属于程序中不可或缺的部分。

当所有非守护线程结束时，程序也就终止，同时会杀死所有守护线程，因为没有了被守护者，守护线程也就没有工作可做了。

例如：*main* 是非守护线程，*JVM* 的 GC 线程是守护线程

在线程启动之前使用 `setDaemon()` 方法可以将一个线程设置为守护线程。

```java
public static void main(String[] args) {
    Thread thread = new Thread(new MyRunnable());
    thread.setDaemon(true);
}
```



## sleep

`Thread.sleep(millisec)` 方法会休眠当前正在执行的线程，millisec 单位为毫秒。

`sleep()` 可能会抛出 `InterruptedException`，因为异常不能跨线程传播回 `main()` 中，因此必须在本地进行处理。线程中抛出的其它异常也同样需要在本地进行处理。

```java
Thread.sleep(1000);  // 让线程睡眠1000ms
```



## yield

对静态方法 `Thread.yield()` 的调用声明了当前线程已经完成了生命周期中最重要的部分，可以切换给其它线程来执行。该方法只是对线程调度器的一个建议，而且也只是建议具有相同优先级的其它线程可以运行。

```java
public void run() {
    Thread.yield();
}
```



## 过时的suspend、resume、stop

`suspend()` 方法在调用后，线程不会释放已经占有的资源（比如锁），而是占有着资源进入睡眠状态，这样容易引发死锁问题。

`stop()` 方法在终结 一个线程时不会保证线程的资源正常释放，通常是没有给予线程完成资源释放工作的机会， 因此会导致程序可能工作在不确定状态下。



## 中断机制

中断可以理解为线程的一个标识位属性，它表示一个运行中的线程是否被其他线程进行了中断操作。

Java 中断机制是一种协作机制，也就是说通过中断并不能直接终止另一个线程，而需要被中断的线程自己处理中断。



`java.lang.Thread` 提供了几个方法来操作中断状态

| 方法                                | 方法描述                                                     |
| ----------------------------------- | ------------------------------------------------------------ |
| `public static boolean interrupted` | 测试当前线程是否已经中断，并清除中断标志。                   |
| `public boolean isInterrupted()`    | 测试线程是否已经中断，线程的中断状态不受该方法的影响，不会清楚中断标志 |
| `public void interrupt()`           | 中断线程。其他线程调用阻塞线程的 `interrupt` 方法，可以打断阻塞状态 |







# 并发相关类🌈

## synchronized

当我们使用多个线程访问同一资源的时候，且多个线程中对资源有写的操作，就容易出现线程安全问题。
要解决上述多线程并发访问一个资源的安全性问题，也就是解决重复票与不存在票问题，Java中提供了同步机制来解决。

Java主要提供了两种锁机制来控制多个线程对共享资源的互斥访问，

- java虚拟机原生实现的 `synchronized`
- jdk源码实现的 `ReentrantLock`



### 同步代码块

- 同步代码块： `synchronized` 关键字可以用于方法中的某个区块中，表示只对这个区块的资源实行互斥访问。

```java
synchronized(同步锁) {
	需要同步操作的代码
}
```

同步技术的原理：

使用了一个锁对象，这个锁对象叫「同步锁」也叫「对象锁」，两个线程一起抢夺cpu的执行权，谁抢到了谁执行run方法。

- t0 抢到了 CPU 的执行权执行 `run` 方法遇到 `synchronized` 代码块，这时 t0 会检查 `synchronized` 代码块是否有锁对象，如果有，就会获取到锁对象。进入到同步中执行

- t1 抢到了 CPU 的执行权执行 `run` 方法遇到 `synchronized` 代码块，这时 t1 会检查 `synchronized` 代码块是否有锁对象，发现没有，t1 就会进入到阻塞状态，一直等待 t0 线程归还锁对象
- 直到 t0 线程执行完同步中的代码，会把锁对象归还给同步代码块，t1 才能获取到锁对象进入到同步中执行

总结：同步中的线程没有执行完毕不会释放锁，同步外的线程没有锁进不去同步

```java
class Window implements Runnable {
    private int ticket = 10000;
  	// 创建锁对象
    Object obj = new Object();

    @Override
    public void run() {
        while (true) {
            synchronized (obj) {
                if (ticket > 0) {
                    System.out.println(Thread.currentThread().getName() + " -> " + ticket);
                    ticket--;
                } else {
                    break;
                }
            }
        }
    }
}
```

同步保证了只能有一个线程在同步中执行共享数据保证了安全，但程序频繁的判断锁、获取锁、释放锁，导致程序的效率会降低。



### 同步方法

使用 `synchronized` 修饰的方法，就叫做同步方法，保证A线程执行该方法的时候，其他线程只能在方法外等待。

```java
class Window implements Runnable {
    private int ticket = 10000;

    public synchronized void payTicket() {
        while (true) {
            if (ticket > 0) {
                System.out.println(Thread.currentThread().getName() + " -> " + ticket);
                ticket--;
            } else {
                break;
            }
        }
    }

    @Override
    public void run() {
        payTicket();
    }
}
```

同步方法中的同步锁是谁？

- 对于非 `static` 方法，同步锁就是 this
- 对于 `static` 方法，同步锁不能是 this ，因为 this 是创建对象之后产生的，静态方法优先于对象产生。静态方法的锁对象就是本类的字节码对象（类名.class）



## ReentrantLock

`java.util.concurrent.locks.Lock` 机制提供了比 `synchronized` 代码块和 `synchronized` 方法更广泛的锁定操作，同步代码块/同步方法具有的功能 `Lock` 都有，除此之外更强大，更体现面向对象的特性。

`ReentrantLock` 也称为同步锁，加锁与释放锁都方法化了：

- `public void lock()` ：加同步锁
- `public void unlock()` ：释放同步锁

使用步骤：

1. 在成员位置创建一个 `ReentrantLock` 对象 （可重入锁）

   > "重入" ，就是指可以重新进入。可重入锁，就是说一个线程在获取某个锁后，还可以继续获取该锁，即允许一个线程多次获取同一个锁。

2. 在可能会出现安全问题的代码前调用 `Lock` 接口中的方法 `lock` 获取锁

3. 在可能会出现安全问题的代码后调用 `Lock` 接口中的方法 `unLock` 释放锁

```java
class Window4 implements Runnable {
    private int ticket = 10000;
    Lock lock = new ReentrantLock();

    @Override
    public void run() {
        while (true) {
            // 加锁
            lock.lock();
            if (ticket > 0) {
                System.out.println(Thread.currentThread().getName() + " -> " + ticket);
                ticket--;
            } else {
                break;
            }
            // 解锁
            lock.unlock();
        }
    }
}
```





## ThreadLocal

`ThreadLocal` 叫做线程本地变量，意思是 `ThreadLocal` 中填充的变量属于当前线程，该变量对其他线程而言是隔离的。

`ThreadLocal` 为变量在每个线程中都创建了一个副本，那么每个线程可以访问自己内部的副本变量。

`ThreadLocal` 的主要应用场景：

- 在进行对象跨层传递的时候，使用 `ThreadLocal` 可以避免多次传递，打破层次间的约束。

- 线程间数据隔离

- 进行事务操作，用于存储线程事务信息

- 数据库连接、Session会话管理

  

```java
public class ThreadLocal_demo {
    public static void main(String[] args) {
        ThreadLocal<String> local = new ThreadLocal<>();
        Random random = new Random();
        IntStream.range(0, 5).forEach(x -> new Thread(() -> {
            local.set(x + "  " + random.nextInt(100));
            System.out.println("----->  " + local.get());
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start());
    }
}

----->  0  30
----->  3  64
----->  2  67
----->  1  91
----->  4  84
```



### ThreadLocalMap

`ThreadLocalMap` 是 `ThreadLocal` 中的一个静态内部类，里面定义了一个 `Entry` 来保存数据，而且继承了弱引用。

在 `Entry` 内部使用 `ThreadLocal` 作为 key，使用自定义设置的值作为 value 。

```java
static class Entry extends WeakReference<ThreadLocal<?>> {
    /** The value associated with this ThreadLocal. */
    Object value;

    Entry(ThreadLocal<?> k, Object v) {
        super(k);
        value = v;
    }
}
```



### 获取线程本地变量

`getMap` 方法获取 `threadLocals` 变量，这个变量事实上就是 `ThreadLocalMap`

```java
ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}
```

在 `setInitialValue` 方法中，如果 map 为空，则会调用 `createMap` 创建 `ThreadLocalMap`

```java
void createMap(Thread t, T firstValue) {
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}
```



```java
public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    return setInitialValue();
}
```



`ThreadLocal` 本身并不存储值，它只是作为一个 key 来让线程从 `ThreadLocalMap` 获取 value 。



### 设置线程本地变量

```java
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}
```



### 移除线程本地变量

```java
public void remove() {
   ThreadLocalMap m = getMap(Thread.currentThread());
   if (m != null)
       m.remove(this);
}
```



### 内存泄露

`ThreadLocal` 是一个弱引用，当为 null 时，会被当成垃圾回收，但是此时 `ThreadLocalMap` 的生命周期和 `Thread` 一样，它不会回收，这时候就出现了一个现象，那就是 `ThreadLocalMap` 的 key 被回收，但是 value 还在，这就造成了内存泄漏。

解决办法：使用完 `ThreadLocal` 后，执行 `remove` 操作，避免出现内存溢出情况。





## CopyOnWriteArrayList

`CopyOnWrite` 容器的构造思想是 “读写分离”，如果有多个调用者同时访问相同的资源，他们会共同获取相同的指针指向相同的资源，直到某个调用者修改资源内容时，系统才会真正复制一份专用副本（private copy）给该调用者，对副本进行操作，最后把原数据替换成当前的副本。此时，而其他调用者所见到的最初的资源仍然保持不变，读操作不会被阻塞。

优点：可以对 `CopyOnWrite` 容器进行并发的读取而不需要加锁，如果调用者没有修改资源，就不会有副本被创建。

主要有以下两类：

- `CopyOnWriteArrayList`
- `CopyOnWriteArraySet`

```java
// CopyOnWriteArrayList 的 add() 方法
// 在访问的时候加锁，拷贝出来一个副本，先操作这个副本，再把现有的数据替换为这个副本
public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            newElements[len] = e;
            setArray(newElements);
            return true;
        } finally {
            lock.unlock();
        }
    }

// CopyOnWriteArrayList 的 get(int index) 方法就是普通的无锁访问

  public E get(int index) {
      return get(getArray(), index);
  }

  @SuppressWarnings("unchecked")
  private E get(Object[] a, int index) {
      return (E) a[index];
  }    
```







# 线程间通信🌈

概念：多个线程在处理同一个资源，但是线程的任务却不相同。

为什么要处理线程间通信：

多个线程并发执行时, 在默认情况下 CPU 是随机切换线程的，当我们需要多个线程来共同完成一件任务，并且我们希望他们有规律的执行, 那么多线程之间需要一些协调通信，以此来帮我们达到多线程共同操作一份数据。

如何保证线程间通信有效利用资源：

多个线程在处理同一个资源，并且任务不同的时候，需要线程通信来帮助解决线程之间对同一个变量的使用或操作。为了避免对同一共享变量的争夺，需要通过一定的手段使各个线程能有效的利用资源，这种手段即「等待唤醒机制」。



## 等待唤醒机制

等待唤醒是多个线程间的一种协作机制。

线程间不仅有竞争（争夺锁），也会有协作，就是在一个线程进行了规定操作后，就进入等待 `wait` ，等待其他线程执行完他们的指定代码过后 再将其唤醒 `notify` 。

如果有多个线程进行等待时，可以使用 `notifyAll` 来唤醒所有的等待线程。

`wait/notify` 就是线程间的一种协作机制。





## wait/notify方法

等待唤醒机制就是用于解决线程间通信的问题的，使用到的3个方法的含义如下：

- `wait()`：线程不再活动，不再参与调度，进入 *wait set* 中，因此不会浪费 CPU 资源，也不会去竞争锁，这时的线程状态即是 Watting 。它需要等待别的线程执行 `notify` 方法，在这个对象上等待的线程从 wait set 中释放出来，重新进入到调度队列（ready queue）中

- `notify()`：则选取所通知对象的 wait set 中的一个线程释放，等待越久的线程越先被释放。`notify`() 不会立即释放锁，只有等到同步块代码执行完毕后才会释放。

- `notifyAll()`：释放所通知对象的 wait set 上的全部线程。

注意：

哪怕只通知了一个等待的线程，被通知线程也不能立即恢复执行，因为它当初中断的地方是在同步块内，而此刻它已经不持有锁，所以它需要再次尝试去获取锁（可能面临其它线程的竞争），成功后才能在当初调用 wait 方法之后的地方恢复执行。

- 如果能获取锁，线程就从 WAITING 状态变成 RUNNABLE 状态；
- 否则，从 wait set 出来，又进入 entry set，线程就从 WAITING 状态又变成 BLOCKED 状态

```java
public class WaitNotifyExample {
    public static void main(String[] args) {
        ExecutorService es = Executors.newCachedThreadPool();
        WaitNotifyExample example = new WaitNotifyExample();
        es.execute(() -> example.after());
        es.execute(() -> example.before());
    }

    public synchronized void before() {
        System.out.println("before");
        notifyAll();
        System.out.println("唤醒");
    }

    public synchronized void after() {
        try {
            wait();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("after");
    }
}
```



注意：

1. `wait` 方法与 `notify` 方法必须要由 同一个锁对象 调用

   因为：对应的锁对象可以通过 `notify` 唤醒使用同一个锁对象调用的 `wait` 方法后的线程

2. `wait` 方法与 `notify` 方法是属于 `Object` 类的方法

   因为：锁对象可以是任意对象，而任意对象的所属类都是继承了 `Object` 类

3. `wait` 方法与 `notify` 方法必须要在同步代码块或者是同步函数中使用

   因为：必须要通过锁对象调用这2个方法





## await/signal

`java.util.concurrent` 类库中提供了 `Condition` 类来实现线程之间的协调，可以在 `Condition` 上调用 `await` 方法使线程等待，其它线程调用 `signal` 或 `signalAll` 方法唤醒等待的线程。

相比于 `wait` 这种等待方式，`await` 可以指定等待的条件，因此更加灵活。

```java
public class AwaitSignalExample {
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();

    public static void main(String[] args) {
        ExecutorService executorService = Executors.newCachedThreadPool();
        AwaitSignalExample example = new AwaitSignalExample();
        executorService.execute(() -> example.after());
        executorService.execute(() -> example.before());
    }

    public void before() {
        lock.lock();
        try {
            System.out.println("before");
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }

    public void after() {
        lock.lock();
        try {
            condition.await();
            System.out.println("after");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```



## Thread.join方法

在线程中调用另一个线程的 `join` 方法，会将当前线程挂起，而不是忙等待，直到目标线程结束。

对于以下代码，虽然 b 线程先启动，但是因为在 b 线程中调用了 a 线程的 `join` 方法，b 线程会等待 a 线程结束才继续执行，因此最后能够保证 a 线程的输出先于 b 线程的输出。

```java
public class JoinExample {

    public static void main(String[] args) {
        JoinExample example = new JoinExample();
        example.test();
    }

    public void test() {
        A a = new A();
        B b = new B(a);
        b.start();
        a.start();
    }

    private class A extends Thread {
        @Override
        public void run() {
            System.out.println("A");
        }
    }

    private class B extends Thread {

        private A a;

        B(A a) {
            this.a = a;
        }

        @Override
        public void run() {
            try {
                a.join();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("B");
        }
    }
}
```



# 并发工具类🌈

`java.util.concurrent` 是在并发编程中使用的工具类，大大提高了并发性能，AQS 被认为是 `java.util.concurrent` 的核心。

- `java.util.concurrent`
- `java.util.concurrent.automic`
- `java.util.concurrent.locks`



## LockSupport

`LockSupport` 定义了一组的公共静态方法，这些方法提供了最基本的线程阻塞和唤醒功能，而 `LockSupport` 也成为构建同步组件的基础工具。



## CountDownLatch

`CountDownLatch` 使一个线程等待其他线程各自执行完毕后再执行。

维护了一个计数器 cnt，每次调用 `countDown` 方法会让计数器的值减1，减到0的时候，调用 `await` 方法而在等待的线程就会被唤醒。

等待完成后，`CountDownLatch` 的动作实施者是执行任务的主线程（main），而不是其他线程，因此具有不可重复性。

```java
public class CountdownLatchExample {

    public static void main(String[] args) throws InterruptedException {
        final int totalThread = 6;
        CountDownLatch countDownLatch = new CountDownLatch(totalThread);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalThread; i++) {
            executorService.execute(() -> {
                System.out.println(Thread.currentThread().getName() + "\trun..");
                countDownLatch.countDown();
            });
        }
        countDownLatch.await();
        System.out.println(Thread.currentThread().getName() + "\tend");
        executorService.shutdown();
    }
}
```





## CyclicBarrier

`CyclicBarrier` 用来控制多个线程互相等待，只有当多个线程都到达时，这些线程才会继续执行。

和 `CountdownLatch` 相似，也通过维护计数器实现的，线程执行 `await` 方法之后计数器会减1，并进行等待，直到计数器为 0，所有调用 `await` 方法而在等待的线程才能继续执行。

等待完成后，`CyclicBarrier` 的动作实施者是执行任务的其他线程，而不是主线程（main），因此具有往复多次实施动作的特点。

`CyclicBarrier` 的计数器通过调用 `reset` 方法可以循环使用，所以也叫做循环屏障。

```java
public class CyclicBarrierExample {
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7, () -> System.out.println("召唤神龙"));
        for (int i = 1; i <= 7; i++) {
            final int temp = i;
            new Thread(() -> {
                System.out.println("已收集第" + temp + "颗龙珠");
                try {
                    cyclicBarrier.await();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }, String.valueOf(i)).start();
        }
    }
}
```



## Semaphore

`Semaphore` 类似于操作系统中的信号量，可以控制对互斥资源的访问线程数。

如果线程要访问一个资源就必须先获得信号量。如果信号量内部计数器大于 0 ，信号量减 1 ，然后允许共享这个资源；否则，如果信号量的计数器等于 0 ，信号量将会把线程置入休眠直至计数器大于 0 ，当信号量使用完时，必须释放。

- `void acquire()` ：从信号量获取一个许可，如果无可用许可前将一直阻塞等待
- `boolean tryAcquire()`：从信号量尝试获取一个许可，如果无可用许可，直接返回false，不会阻塞
- `void release()`： 释放一个许可，尽量在 `finally` 中使用

以下代码模拟了对停车场的并发请求，每次只能有3辆车进入停车场。

```java
public class SemaphoreExample {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(3);
        for (int i = 0; i < 5; i++) {
            new Thread(() -> {
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + "号车抢到了车位");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName() + "号车离开了车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();
                }
            }, String.valueOf(i)).start();
        }
    }
}
```





## ReadWriteLock

`ReadWriteLock` 是一个接口，有 `readLock` 和 `writeLock` 两个方法

写操作需要保持数据一致性，但读操作不需要。

```java
public class ReadWriteLockExample {
    public static void main(String[] args) {
        MyCache cache = new MyCache();
        // 写操作
        for (int i = 0; i < 5; i++) {
            final int temp = i;
            new Thread(() -> {
                cache.put(String.valueOf(temp), temp);
            }, String.valueOf(i)).start();
        }
        // 读操作
        for (int i = 0; i < 5; i++) {
            final int temp = i;
            new Thread(() -> {
                cache.get(String.valueOf(temp));
            }, String.valueOf(i)).start();
        }
    }
}

class MyCache {
    private volatile Map<String, Object> map = new HashMap<>();
    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    public void put(String key, Object value) {
        readWriteLock.writeLock().lock();
        System.out.println(Thread.currentThread().getName() + "\t开始写入数据");
        System.out.println(Thread.currentThread().getName() + "\t写入数据完成");
        readWriteLock.writeLock().unlock();
    }

    public void get(String key) {
        readWriteLock.readLock().lock();
        System.out.println(Thread.currentThread().getName() + "\t开始读取数据");
        System.out.println(Thread.currentThread().getName() + "\t读取数据完成");
        readWriteLock.writeLock().lock();
    }
}
```





## BlockingQueue

`java.util.concurrent.BlockingQueue` 接口有以下阻塞队列的实现：

-  `ArrayBlockingQueue`：由数组结构组成的有界阻塞队列
-  `LinkedBlockingQueue` ：由链表结构组成的有界阻塞队列
-  `PriorityBlockingQueue`：支持优先级排序的无界阻塞队列
-  `SynchronousQueue`：不存储元素的阻塞队列，也即单个元素的队列

提供了阻塞的 `take` 和 `put` 方法

- 如果队列为空，那么从队列中获取元素的 `take` 方法将被阻塞，直到队列中有内容。

- 如果队列为满，那么从队列中添加火速的 `put` 方法将阻塞，直到队列有空闲位置。



| 方法类型 | 抛出异常  | 特殊值   | 阻塞   | 超时                 |
| -------- | --------- | -------- | ------ | -------------------- |
| 插入     | add(e)    | offer(e) | put(e) | offer(e, time, unit) |
| 移除     | remove()  | poll()   | take() | poll(time, unit)     |
| 检查     | element() | peek()   | /      | /                    |









# 线程池🌈

我们使用线程的时候就去创建一个线程，这样实现起来非常简便，但是就会有一个问题：

如果并发的线程数量很多，并且每个线程都是执行一个时间很短的任务就结束了，这样频繁创建线程就会大大降低系统的效率，因为频繁创建线程和销毁线程需要时间。

对于使用 `new thread` 来创建线程，有以下几点问题：

- 每次 `new Thread` 新建对象性能差。
- 线程缺乏统一管理，可能无限制新建线程，相互之间竞争，及可能占用过多系统资源

- 缺乏更多功能，如定时执行、定期执行、线程中断

在java中可以通过「线程池」使得线程可以复用，即执行完一个任务，并不被销毁，继续执行其他的任务。



## 线程池特点

线程池：其实就是一个容纳多个线程的容器，其中的线程可以反复使用，省去了频繁创建线程对象的操作，无需反复创建线程而消耗过多资源。

线程池的主要特点为：线程复用、控制最大并发数、管理线程

- 降低资源消耗：通过重复利用己创建的线程降低线程创建和销毁造成的销耗。

- 提高响应速度：当任务到达时，任务可以不需要等待线程创建就能立即执行。

- 提高线程的可管理性：线程是稀缺资源，如果无限制的创建，不仅会销耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。



## 线程池的使用

Java里面线程池的顶级接口是 `java.util.concurrent.Executor` ，但是严格意义上讲 `Executor` 并不是一个线程池，而只是一个执行线程的工具。

真正的线程池接口是 `java.util.concurrent.ExecutorService`。

`java.util.concurrent.Executors` 线程工厂类里面提供了一些静态方法，可以生成一些常用的线程池。

Executors类中有个创建线程池的方法如下：

* `public static ExecutorService newFixedThreadPool(int nThreads)`：返回线程池对象。

  > 创建的是有界线程池，也就是池中的线程个数可以指定最大数量

获取到了一个线程池 `ExecutorService` 对象，然后线程池对象的使用方法如下：

* `public Future<?> submit(Runnable task)`：获取线程池中的某一个线程对象，并执行

  > Future接口：用来记录线程任务执行完毕后产生的结果。线程池创建与使用。



```java
public class MT_POOL {
    public static void main(String[] args) {
        // 线程池会一直开启使用完了线程，会自动把线程归还给线程池，线程可以继续使用
        ExecutorService es = Executors.newFixedThreadPool(3);
        es.submit(new RunnableImplement());
        es.submit(new RunnableImplement());
        es.submit(new RunnableImplement());
        es.submit(new RunnableImplement());
        // 关闭线程池（一般不关闭）
        es.shutdown();
    }
}

class RunnableImplement implements Runnable {
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
}
```



## Executor

`Executor` 管理多个异步任务的执行，而无需程序员显式地管理线程的生命周期。这里的异步是指多个任务的执行互不干扰，不需要进行同步操作。

`Executor` 接口中定义了 `execute` 方法，用来接收一个 `Runnable` 接口的对象

<img src="http://store.secretcamp.cn/uPic/image-20210315104940009202103151049401615776580f0MI7nf0MI7n.png" alt="image-20210315104940009" style="zoom: 43%;" />



### ExecutorService

`ExecutorService` 接口继承了 `Executor` 接口，是 `Executor` 的子接口

接口中定义的 `submit` 方法可以接收 `Runnable`和 `Callable` 接口对象，返回 `Future<?>` 对象



### Executors

`Executors` 类是线程池的工具类，提供工厂方法用来创建不同类型的线程池。

- `Executors.newSingleThreadExecutor()`： 创建一个只有一个线程的线程池，

- `Executors.newFixedThreadPool(int numOfThreads)`：来创建固定线程数的线程池，

- `Executors.newCachedThreadPool()`：创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。

- `Executors.newScheduledThreadPool(int corePoolSize)`：创建一个线程池，它可安排在给定延迟后运行命令或者定期地执行。



### ThreadPoolExecutor

`Executors`中主要可以创建三种 `Executor`：

- `FixedThreadPool`：所有任务只能使用固定大小的线程
- `SingleThreadExecutor`：相当于大小为1的 `FixedThreadPool`
- `CachedThreadPool`：一个任务创建一个线程，即有多少任务就创建多少线程

它们的类型都是 `ThreadPoolExecutor` ，是通过 `Executors` 中的工厂方法创建的。 



线程池最好不要使用 `Executors` 去创建，而是通过 `ThreadPoolExecutor` 创建，这样的处理方式可以更加明确线程池的运行规则，规避资源耗尽的风险。

1) `FixedThreadPool` 和 `SingleThreadExecutor`

允许的请求队列长度为 `Integer.MAX_VALUE`，可能会堆积大量的请求，从而导致内存溢出。 

2) `CachedThreadPool` 和 `ScheduledThreadPool`

允许的创建线程数量为 `Integer.MAX_VALUE`，可能会创建大量的线程，从而导致内存溢出。





## 线程池的参数

`ThreadPoolExecutor` 的构造方法有以下参数：

- `int corePoolSize`： 线程池中的常驻核心线程数，即线程池的基本大小，当提交一个任务到线程池时，线程池会创建一个线程来执行任务，即使其他空闲的基本线程能够执行新任务也会创建线程，等到需要执行的任务数大于线程池基本大小时就不再创建。

- `int maximumPoolSize`：线程池中的最大核心线程数。如果任务队列满了，并 且已创建的线程数小于最大线程数，则线程池会再创建新的线程执行任务。如果使用了无界的任务队列这个参数就没什么效果。

- `long keepAliveTime`：多余的空闲线程的存活时间。当池中线程数量超过 `corePoolSize` 时，当空闲时间达到预定值时，多余线程会被销毁直到只剩下 `corePoolSize` 个线程为止

- `TimeUnit unit`： `keepAliveTime` 的单位

- `BlockingQueue<Runnable> workQueue`：任务队列，被提交但尚未被执行的任务

  - `ArrayBlockingQueue`：是一个基于数组结构的有界阻塞队列，此队列按 FIFO 原则对元素进行排序
  - `LinkedBlockingQueue`：一个基于链表结构的阻塞队列，此队列按FIFO排序元素，吞吐量通 常要高于`ArrayBlockingQueue`
  - `SynchronousQueue`：一个不存储元素的阻塞队列。每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于 `LinkedBlockingQueue`
  - `PriorityBlockingQueue`：一个具有优先级的无限阻塞队列

- `ThreadFactory threadFactory`：表示生成线程池中工作线程的线程工厂，用于创建线程，一般默认的即可

  - 开源框架 guava 提供的 `ThreadFactoryBuilder` 可以快速给线程池里的线程设置有意义的名字

    ```java
    new ThreadFactoryBuilder().setNameFormat("XX-task-%d").build();
    ```

- `RejectedExecutionHandler handler`：拒绝策略，表示当队列满了，并目工作线程大于等于线程的最大线程数 maximumPoolSize 时如何来拒绝请求执行的 Runnable 的策略



### hanlder类型

- `AbortPolicy`（默认）：直接抛出 `RejectedExecution` 异常

- `CallerRunsPolicy`： 该策略既不会抛弃任务，也不会抛出异常，而是将某些任务回退到调用者，从而降低新任务的流量。 

- `DiscardOldestPolicy`：抛奔队列中等待最久的任务，然后把当前任务加人队列中尝试再次提交当前任务。

- `DiscardPolicy`：该策略默默地丟弃无法处理的任务，不予任何处理也不抛出异常。如果允许任务丟失，这是最好的一种策略。



## 线程池的运行机制

1、在创建了线程池后，开始等待请求

2、当调用 `execute` 方法添加一个请求任务时，线程池会做出如下判断：

- 如果正在运行的线程数量小于 corePoolSize，那么马上创建线程运行这个任务
- 如果正在运行的线程数量大于或等于 corePoolSize，那么将这个任务放入队列
- 如果此时队列满了且正在运行的线程数量小于 maximumPoolSize，那么会创建非核心线程立刻运行这个任务 
- 如果队列满了且正在运行的线程数量大于或等于 maximumPoolSize，那么线程池会启动拒绝策略

3、当一个线程完成任务时，它会从队列中取下一个任务来执行。

4、当一个线程无事可做超过一定的时间 keepAliveTime 时，如果当前运行的线程数大于 corePoolSize，那么这个线程就被停止，所以线程池的所有任务完成后，它最终会收缩到 corepoolsize 的大小。

