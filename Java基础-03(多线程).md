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
