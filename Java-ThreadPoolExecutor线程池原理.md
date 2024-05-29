
### 1. 不要自己创建线程，而是使用线程池

1、降低资源消耗，线程的创建和销毁，在操作系统层面，需要由用户态切换到内核态，这是一个费时费力的过程。<br>
2、提高响应速度，线程已经在线程池存在了，省去了创建线程的时间。<br>

### 2. 核心线程数、最大线程数、任务队列（BlockingQueue）

1、核心线程数，线程池的基本大小，至少至少有这么多线程存活<br>
2、最大线程数，当任务数量超过核心线程数后，线程池会创建新的线程，但是最大不会超过这个数量<br>
3、任务队列，当任务数量继续增加，超过了最大线程数，那就需要用队列临时存储新提交的任务，等待有空闲线程后再执行<br>

### 3. BlockingQueue 4种运行方式

1、add 如果队列满了，直接抛异常<br>
2、offer 如果队列满了，不抛异常，而是返回特殊值（null或者false）<br>
3、put 如果队列满了，就等待，一直阻塞当前线程，知道队列有空间可用<br>
4、offer(等待时间) 如果队列满了，阻塞当前线程给定时间后，放弃并返回特殊值，不抛异常<br>

### 4. 生产者 - 消费者 模式，放入任务的是生产者，线程池是消费者

任务执行流程：
1、调用 execute 放入任务，依靠 AtomicInteger 存储当前活跃线程数（也算是处理了多线程问题，毕竟可以多个线程频繁 execute 放入任务）<br>
2、如果工作线程小于核心线程数，尝试创建新线程<br>
3、如果工作线程大于等于核心线程数，尝试放入任务队列（不急于创建线程，而是先放入队列）<br>
4、如果工作线程小于最大线程数，创建线程，否则放入队列<br>

为什么不急于创建线程，因为放入队列后，如果有空闲线程，将会直接进入执行，而不用创建新线程了

### 5. 线程池状态

RUNNING:    Accept new tasks and process queued tasks <br>
SHUTDOWN:   Don't accept new tasks, but process queued tasks **不接受新任务，但是队列里的会继续处理完**<br>
STOP:       Don't accept new tasks, don't process queued tasks, and interrupt in-progress tasks **不接受新任务，队列里的也不处理了**<br>
TIDYING:    All tasks have terminated, workerCount is zero, the thread transitioning to state TIDYING will run the terminated() hook method<br>
TERMINATED: terminated() has completed<br>

### 6. Worker对象，继承了 AbstractQueuedSynchronizer，实现了 Runnable 接口，主要讲 AQS 原理（三个重点：自旋，LockSupport.park() 和 unpark()，CAS）

1、AQS 是 AbstractQueuedSynchronizer 的简称，AQS 父类是 AbstractOwnableSynchronizer，这个父类里拿着一个独占线程 Thread 对象。<br>
2、AQS 两种资源共享模式：**独占式**，每次只能有一个线程持有锁，例如 ReentrantLock 实现的就是独占式的锁资源。**共享式**，允许多个线程同时获取锁，并发访问共享资源，ReentrantWriteLock 和 CountDownLatch 等就是实现的这种模式。而 ReentrantReadWriteLock 同时实现了这两种模式。<br>
3、AQS 它维护了一个 volatile 的 state 变量和一个 FIFO（先进先出）的队列。其中 state 变量代表的是竞争资源标识，而队列代表的是竞争资源失败的线程排队时存放的容器。<br>
4、AQS 是一个抽象类，他采用模板方法的设计模式，规定了独占和共享模式需要实现的方法，并且将一些通用的功能已经进行了实现，所以不同模式的使用方式，只需要自己定义好实现共享资源的获取与释放即可，至于具体线程在等待队列中的维护（获取资源入队列、唤醒出队列等），AQS 已经实现好了。<br>

#### AQS 实现类

1、ReentrantLock 在初始化的时候 state=0，表示资源未被锁定。当A线程执行 lock() 方法时，会调用 tryAcquire() 方法，将 AQS 中队列的模式设置为独占，并将独占线程设置为线程A，以及将 state + 1。这样在线程A没有释放锁前，其他线程来竞争锁，调用 tryAcquire() 方法时都会失败，然后竞争锁失败的线程就会进入到队列中。当线程A调用执行 unlock() 方法将 state=0 后，其他线程才有机会获取锁（注意 ReentrantLock 是可重入的，同一线程多次获取锁时 state 值会进行累加的，在释放锁时也要释放相应的次数才算完全释放了锁）。

2、CountDownLatch 会将任务分成N个子线程去执行，state 的初始值也是N（state 与子线程数量一致）。N个子线程是并行执行的，每个子线程执行完成后 countDown() 一次，state会通过 CAS 方式减1。直到所有子线程执行完成后（state=0），会通过 unpark() 方法唤醒主线程，然后主线程就会从 await() 方法返回，继续后续操作。

#### Worker 工作原理

1、runWorker 方法里面调用 ThreadPoolExecutor 的 getTask 方法，从任务队列拿任务，或者创建这个Worker的时候传入的firstTask，直接执行。<br>
2、如果没有任务，就会一直等待（阻塞队列），或者到达设置的线程存活时间后销毁线程。<br>
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/ad044b87-5e39-44b7-9152-c8becd64f397)
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/3d9ea701-3e75-4381-b06e-a8f3257e7ce3)
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/3cd13671-16fc-434e-b418-fc0d43f2dc1e)
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/931edc8d-b2cf-4289-aa8c-690900603da0)
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/2aa1a1ee-c1b3-4b8f-85db-ead61c1f58a0)

### 7. 任务队列 BlockingQueue

1、ArrayBlockingQueue 数组实现，初始化需要指定大小，所以是有界队列。<br>
2、LinkedBlockingQueue 链表实现，可以无限大Integer.MAX_VALUE，但是一般会指定大小，变成有界队列。<br>
3、SynchronousQueue 是一个不存储元素的阻塞队列。每个插入操作必须得等到另一个线程调用了移除操作后，该线程才会返回，否则将一直阻塞。吞吐量通常要高于LinkedBlockingQueue。<br>
4、PriorityBlockingQueue 是一个将元素按照优先级排序的阻塞队列，元素的优先级越高，将会越先出队列。这是一个无界队列。<br>

### 8. 拒绝策略，创建线程池需要传入 RejectedExecutionHandler 来处理被拒绝的任务（队列满了，并且不能创建更多线程了），

- AbortPolicy：不再接收新任务，直接抛出异常。
- CallerRunsPolicy：提交任务的线程自己处理。
- DiscardPolicy：不处理，直接丢弃。
- DiscardOldestPolicy：丢弃任务队列中排在最前面的任务，并执行当前任务。（排在队列最前面的任务并不一定是在队列中待的时间最长的任务，因为有可能是按照优先级排序的队列）

原文链接：https://juejin.cn/post/6844904000056197127
这个讲的好：https://blog.csdn.net/cy973071263/article/details/131484384
