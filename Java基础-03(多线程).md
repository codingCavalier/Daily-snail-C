#### 1、进程和线程？
进程是资源分配的最小单位，线程是 CPU 调度的最小单位。<br>
* 进程（Process）是指正在运行的一个程序的实例。每个进程都拥有的资源：堆、栈、虚存空间（页表）、文件描述符等信息。在 Java 中，每个进程都由一个主线程（main thread）启动。当进程运行时，操作系统会为其分配一个进程号，并将其作为一个独立的实体来进行管理。
* 线程（Thread）是指进程中的一个执行单元，是 CPU 调度和分派的基本单位，它是比进程更小的能独立运行的基本单位。在 Java 中，每个线程都拥有自己的栈空间和程序计数器，并且可以访问共享的堆内存。

* 进程是系统分配资源的基本单位，线程是程序执行的基本单位；
* 进程拥有独立的内存空间和资源，而线程则共享进程的内存和资源；
* 进程之间的通信比较复杂，而线程之间可以直接共享数据；
* 进程的切换代价比较大，需要保存上下文和状态，而线程的切换代价比较小，因为它们共享进程的资源。

#### 2、虚拟线程（逻辑线程），协程？
Java 中的虚拟线程，也叫做协程或“轻量级线程”，它诞生于 JDK 19（预览 API），正式发布于 JDK 21，它是一种在 Java 虚拟机（JVM）层面实现的逻辑线程，不直接和操作系统的物理线程一一对应，因此它可以减少上下文切换所带来的性能开销。

#### 3、线程等待和唤醒有几种实现？
* Object 类下的 wait()、notify() 和 notifyAll() 方法；
* Condition 类下的 await()、signal() 和 signalAll() 方法；
* LockSupport 类下的 park() 和 unpark() 方法。

LockSupport.park()：休眠当前线程。LockSupport.unpark(线程对象)：唤醒某一个指定的线程。
LockSupport 存在的必要性：前两种方法 notify 方法以及 signal 方法都是随机唤醒，如果存在多个等待线程的话，可能会唤醒不应该唤醒的线程，因此有 LockSupport 类下的 park 和 unpark 方法指定唤醒线程是非常有必要的。<br>
Condition 存在的必要性：Condition 相比于 Object 类的 wait 和 notify/notifyAll 方法，前者可以创建多个等待集，例如，我们可以创建一个生产者等待唤醒对象，和一个消费者等待唤醒对象，这样我们就能实现生产者只能唤醒消费者，而消费者只能唤醒生产者的业务逻辑了。

#### 4、Java 中的锁机制？
https://www.javacn.site/interview/thread/java-lock.html

#### 5、什么是线程间通讯？
* 共享内存：多个线程可以访问同一个共享内存区域，通过读取和写入内存中的数据来进行通讯和同步。
* 消息传递：多个线程之间通过消息队列、管道、信号量等机制来传递信息和同步状态。

#### 6、Thread 的启动？
通过调用 native 方法 private native void start0() 启动的线程，并非 Java 代码启动的线程。

#### 7、线程的状态？
* New 新创建的线程，尚未执行
* Runnable 执行中的线程
* Blocked 被阻塞而挂起的线程
* Waiting 等待中的线程
* Timed Waiting 处于计时等待中的线程
* Terminated 执行结束的线程

线程终止的可能：run执行结束、抛出异常、调用stop方法（不建议）

现有主线程main，子线程t，在主线程main中调用t.join()，会使main等待t执行结束，再继续执行main里的代码，如果t已经执行结束，那么join方法会立即返回。
```Java
// 多线程
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            System.out.println("hello");
        });
        System.out.println("start");
        t.start(); // 启动t线程
        t.join(); // 此处main线程会等待t结束
        System.out.println("end");
    }
}
```

#### 8、中断线程
A 线程调用 B 线程的 interrupt() 方法可以请求中断 B 线程（只是一个标记，并不会强行中断 B 线程），B 线程通过检测 isInterrupted() 标志获取自身是否需要中断。<br>
如果 B 线程处于等待状态而被要求中断，则能够让 B 线程进入到等待状态的那行代码（例如cThread.join()）就会抛出 InterruptedException 异常，当这个异常抛出后，B 线程的 isInterrupted 标记会被重置为 false，并且解除等待状态，能够继续往下执行。<br>

理解：当一个线程处于 wait、sleep、join 状态时，说明它正在执行某项非正常任务，此时要求它 中断 ，对它而言是不合理的，因为它可能正在与其他线程进行交互等等。<br>
把 isInterrupted 标记重置，且抛出 InterruptedException 异常的目的，就是为了让代码编写者精准处理这个情况，将原本的处理 正常中断 的情况，演化为处理 错误状态下的中断 的情况，以此来让线程自己判断，此时是真的要中断（走正常中断逻辑），还是继续执行一些其他逻辑。

#### 9、volatile 关键字
Java 内存模型<br>
volatile 关键字的目的是告诉虚拟机：每次访问变量时，总是获取主内存的最新值；每次修改变量后，立刻回写到主内存。<br>
volatile 关键字解决的是可见性问题：当一个线程修改了某个共享变量的值，其他线程能够立刻看到修改后的值。

#### 10、守护线程
守护线程是指为其他线程服务的线程。在 JVM 中，所有非守护线程都执行完毕后，无论有没有守护线程，虚拟机都会自动退出。因此，JVM 退出时，不必关心守护线程是否已结束。<br>
守护线程如何结束？当所有非守护线程都结束后，进程结束，守护线程也便结束了。

#### 11、线程同步（多线程问题）
多线程模型下，要保证逻辑正确，对共享变量进行读写时，必须保证一组指令以原子方式执行：即某一个线程执行时，其他线程必须等待。<br>
通过加锁和解锁的操作，就能保证3条指令总是在一个线程执行期间，不会有其他线程会进入此指令区间。即使在执行期线程被操作系统中断执行，其他线程也会因为无法获得锁导致无法进入此指令区间。<br>
只有执行线程将锁释放后，其他线程才有机会获得锁并执行。这种加锁和解锁之间的代码块我们称之为临界区（Critical Section），任何时候临界区最多只有一个线程能执行。<br>

一个线程可以在持有某个资源的锁的情况下，去干别的事，比如去拿另一个资源的锁。一个对象的头信息里会标记它目前的锁状态，即是否被某个线程持有锁。<br>
synchronized 代码块抛出异常，也会正常释放锁，不用担心。<br>

不需要 synchronized 的操作（JVM 规范定义了几种基本的原子操作）：<br>
* 基本数据类型赋值（long 和 double 除外）：int m = 9;
* 引用类型赋值：List<String> = new ArrayList<>();

这说的是单行语句，默认是原子操作，但是多行语句，则需要同步代码块来保证这几句语句的原子操作

#### 12、死锁
Java 的线程锁，是可重入的锁。
```Java
public class Counter {
    private int count = 0;

    public synchronized void add(int n) {
        if (n < 0) {
            dec(-n); // 已经拿到了锁，可以直接进入
        } else {
            count += n;
        }
    }

    public synchronized void dec(int n) {
        count += n;
    }
}
```
对同一个线程，能否在获取到锁以后继续获取同一个锁？答案是肯定的。JVM允许同一个线程重复获取同一个锁，这种能被同一个线程反复获取的锁，就叫做可重入锁。<br>
由于 Java 的线程锁是可重入锁，所以，获取锁的时候，不但要判断是否是第一次获取，还要记录这是第几次获取。每获取一次锁，记录+1，每退出 synchronized 块，记录-1，减到0的时候，才会真正释放锁。<br>

一个线程可以获取一个锁后，再继续获取另一个锁。例如：
```Java
public void add(int m) {
    synchronized(lockA) { // 获得lockA的锁
        this.value += m;
        synchronized(lockB) { // 获得lockB的锁
            this.another += m;
        } // 释放lockB的锁
    } // 释放lockA的锁
}

public void dec(int m) {
    synchronized(lockB) { // 获得lockB的锁
        this.another -= m;
        synchronized(lockA) { // 获得lockA的锁
            this.value -= m;
        } // 释放lockA的锁
    } // 释放lockB的锁
}
```
在获取多个锁的时候，不同线程获取多个不同对象的锁可能导致死锁。<br>
那么我们应该如何避免死锁呢？答案是：线程获取锁的顺序要一致。即严格按照先获取lockA，再获取lockB的顺序，改写dec()方法如下：
```Java
public void dec(int m) {
    synchronized(lockA) { // 获得lockA的锁
        this.value -= m;
        synchronized(lockB) { // 获得lockB的锁
            this.another -= m;
        } // 释放lockB的锁
    } // 释放lockA的锁
}
```
死锁和可重入锁的性质没有关系，死锁是因为多个线程多个锁导致的。<br>
死锁发生的四个必要条件：锁只能一个线程持有，持有以后不能被其他线程抢走，持有以后还能去抢别的锁，抢不到会等待。

#### 13、wait 和 notify
多线程协调运行的原则就是：当条件不满足时，线程进入等待状态；当条件满足时，线程被唤醒，继续执行任务。<br>
wait() 方法需要在锁对象上调用，即锁是谁，就调用谁的 wait() 方法，例如锁是当前类的实例对象，就调用 this.wait()，wait 是 native 方法，里面很复杂。wait 方法必须在 synchronized 代码块内调用，调用时会释放锁，当再次回来时会尝试获得锁。<br>
notify() 方法同理，它的调用会唤醒等待在这个锁上的某一个线程。notifyAll() 唤醒等待在这个锁上的所有线程。

#### 14、队列的方法
poll 和 remove 都是取出并移除头元素，但 remove 更显强制性，没有元素就抛出异常。poll 的英文翻译是“民意调查/选举投票”更柔和。
peek 和 element 都是取出且不移除头元素，但 element 更强制，表达了就是想要元素的目的，没元素会抛出异常。peek 的英文翻译是“偷看窥视”，只看看不移除，没有那么强硬的意思。
offer 和 add 都是添加的意思，add更强硬，到达队列限制后，会抛出异常，添加失败。

#### 15、ReentrantLock
ReentrantLock 可以替代 synchronized 进行同步；ReentrantLock 获取锁更安全；必须先获取到锁，再进入 try {...} 代码块，最后使用 finally 保证释放锁；可以使用 tryLock() 尝试获取锁。<br>
Condition（必须从Lock对象获取 newCondition()） 提供 await、signal、signalAll 功能和 wait、notify、notifyAll 一样，它也可以和 tryLock() 类似，设置等待时间，等待超时就自己醒来。<br>
缺点：读写之间互相等待，如果读的多，写的少，可以用 ReadWriteLock 优化

#### 16、ReadWriteLock（悲观读锁）
允许多个线程同时读，但是如果有一个线程在写，那么所有的读也就不允许了，其他线程的写也同样不允许。适合读多写少的场景。<br>
缺点：有线程在读的时候，不允许写的线程获取锁，需要等读的线程释放了锁，写的线程才有可能拿到锁，而且是看操作系统分配的。可以用 Java 8 引入的新的读写锁 StampedLock 解决。

#### 17、StampedLock（乐观读锁）
它的不同之处是，读的时候，允许写操作，但是读到的数据可能就不是最新的，那么就需要判断是否正在进行写操作。<br>
乐观锁的意思就是乐观地估计读的过程中大概率不会有写入，因此被称为乐观锁。反过来，悲观锁则是读的过程中坚决不能有写入，也就是写入必须等待。显然乐观锁的并发效率更高，但一旦有小概率的写入导致读取的数据不一致，需要能检测出来，再读一遍就行。<br>
StampedLock 是不可重入锁，即不能在一个线程中反复获取同一个锁。

#### 18、Semaphore
限制最多访问的线程数量。如果要对某一受限资源进行限流访问，可以使用Semaphore，保证同一时间最多N个线程访问受限资源。
```Java
public class AccessLimitControl {
    // 任意时刻仅允许最多3个线程获取许可:
    final Semaphore semaphore = new Semaphore(3);

    public String access() throws Exception {
        // 如果超过了许可数量,其他线程将在此等待:
        semaphore.acquire();
        try {
            // TODO:
            return UUID.randomUUID().toString();
        } finally {
            semaphore.release();
        }
    }
}
调用acquire()可能会进入等待，直到满足条件为止。也可以使用tryAcquire()指定等待时间：
if (semaphore.tryAcquire(3, TimeUnit.SECONDS)) {
    // 指定等待时间3秒内获取到许可:
    try {
        // TODO:
    } finally {
        semaphore.release();
    }
}
Semaphore本质上就是一个信号计数器，用于限制同一时间的最大访问数量。
```

#### 19、concurrent 包
java.util.concurrent 包下的 ArrayBlockingQueue、LinkedBlockingQueue、CopyOnWriteArrayList、ConcurrentHashMap、CopyOnWriteArraySet、LinkedBlockingDeque（双向队列）

#### 20、atomic 包
java.util.concurrent.atomic 包下的原子操作封装类 AtomicInteger、AtomicBoolean、AtomicLong、AtomicReference
AtomicInteger 举例：<br>
* 增加值并返回新值：int addAndGet(int delta)
* 加1后返回新值：int incrementAndGet()
* 获取当前值：int get()
* 用CAS方式设置：int compareAndSet(int expect, int update)
* 
Atomic类是通过无锁（lock-free）的方式实现的线程安全（thread-safe）访问。它的主要原理是利用了CAS：Compare and Set。<br>

#### 21、线程池 ExecutorService

因为ExecutorService只是接口，Java标准库提供的几个常用实现类有：

* FixedThreadPool：线程数固定的线程池；
* CachedThreadPool：线程数根据任务动态调整的线程池；
* SingleThreadExecutor：仅单线程执行的线程池；
* ScheduledThreadPool：多线程调度用的线程池；
* SingleThreadScheduledExecutor：单线程调度用的线程池；

创建这些线程池的方法都被封装到 Executors 这个类中，实际创建的都是这个类 ThreadPoolExecutor。

#### 22、线程池的队列
线程池的两个重要组件，就是线程 和 任务队列。任务的排队策略有三种：
* 直接交付：任务来了直接交给线程，而不是缓存起来，例如 CachedThreadPool 就是这样的，它的最大线程数是Int的最大值，它采用的是 SynchronousQueue 即同步队列，当有人取的时候，才能放入，也就是任务无需排队等待，没有空闲线程时直接创建新线程执行新来的任务，这就可能导致线程无限增长下去。
* 无界队列：任务来了，如果没有空闲线程，同时也不能继续创建新的线程了，那就先缓存起来，例如 FixedThreadPool 采用的是LinkedBlockingQueue，它有固定的线程数，当没有空闲线程时，且任务来的太多，就会把任务缓存起来，它又是无界队列，没有容量限制，这就可能导致队列无限增大下去。
* 有界队列：ArrayBlockingQueue，简单来说，就是可能导致任务丢失。这种队列需要和线程池大小配合来用，大队列配小池子，虽然可以降低CPU使用率，但是吞吐量也被人为降低了，线程少，处理的慢。小队列配大池子，会增加CPU使用率，但是调度困难，也可能导致吞吐量下降。

注意：如果采用直接交付的方式执行任务，那么最大线程数必须是无限大，否则在任务数量超出最大线程数时，会抛出异常 RejectedExecutionException 拒绝执行异常，因为直接交付的策略需要创建新线程去执行任务。<br>
如果采用有界队列，那么当任务添加时，超出队列容量，即添加失败时，将抛出异常 RejectedExecutionException 拒绝执行异常，例如2个线程正在执行2个任务，队列容量为3且已经满了，此时添加第6个任务就会抛出异常。

#### 23、ScheduledThreadPool
它的里面用到了定制的专用队列 DelayedWorkQueue 这是一个用于按照任务的执行时间对任务进行排序的队列，DelayedWorkQueue 是用数组来储存队列中的元素，核心数据结构是二叉最小堆的优先队列，队列满时会自动扩容。<br>
详情看看这个：https://zhuanlan.zhihu.com/p/310621485 <br>

为什么要使用 DelayedWorkQueue 呢？<br>
定时任务执行时需要取出最近要执行的任务，所以任务在队列中每次出队时一定要是当前队列中执行时间最靠前的，所以自然要使用优先级队列。<br>
DelayedWorkQueue 是一个优先级队列，它可以保证每次出队的任务都是当前队列中执行时间最靠前的，由于它是基于堆结构的队列，堆结构在执行插入和删除操作时的最坏时间复杂度是O(logN)。

#### 24、多线程的网络模型（延迟工作队列原理基础）
对于多线程的网络模型来说：所有线程会有三种身份中的一种：leader和follower，以及一个干活中的状态：proccesser。<br>
它的基本原则就是，永远最多只有一个leader。而所有follower都在等待成为leader。<br>
线程池启动时会自动产生一个Leader负责等待网络IO事件，当有一个事件产生时，Leader线程首先通知一个Follower线程将其提拔为新的Leader，然后自己就去干活了，去处理这个网络事件，处理完毕后加入Follower线程等待队列，等待下次成为Leader。<br>
这种方法可以增强CPU高速缓存相似性，及消除动态内存分配和线程间的数据交换。<br>

#### 25、ScheduledThreadPool 是如何调度多个线程获取任务的？
再来说一下leader的作用，这里的leader是为了减少不必要的定时等待。leader线程的设计，是Leader-Follower模式的变种，旨在于为了减少不必要的时间等待。<br>
当一个take线程变成leader线程时，只需要等待下一次的延迟时间，而其他take线程则需要等leader线程出队列了才被唤醒。<br>

举例来说，如果没有leader，那么在执行take时，都要执行available.awaitNanos(delay)，假设当前线程执行了该段代码，这时还没有signal，它就阻塞了，第二个线程也执行了该段代码，也要被阻塞。多个线程执行该段代码是没有作用的，因为只能有一个线程会从take中返回queue[0]（因为有lock），<br>
其他线程这时再返回for循环执行时取的queue[0]，已经不是之前的queue[0]了，然后又要继续阻塞（因为任务是延迟任务，执行时间还没有到）。<br>

所以，为了不让多个线程频繁的做无用的定时等待，这里增加了leader，如果leader不为空，则说明队列中第一个节点已经在等待出队，这时其它的线程会一直阻塞，减少了无用的阻塞（注意，在finally中调用了signal()来唤醒一个线程，而不是signalAll()）。


