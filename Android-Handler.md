
### Handler

1、android.os.Handler，既然放在os包下，说明它很重要<br>
2、Handler类的注释
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/5fc580ce-59d7-4be1-b6ce-0d1df518b705)
说明：<br>
- Handler允许你发送和处理 消息 和 Runnable（Handler内部包装成了Message，然后把Runnable赋值给了Message的callback字段） 到一个消息队列。一个Handler绑定一个线程和一个消息队列，和一个Looper。Handler往这个Looper的消息队列发消息，并且使用这个Looper的线程处理消息。
- Handler的主要用途：a. 在某个特定的时间点执行某个任务；b. 切换线程执行任务
- post用于发送Runnable，send用于发送Message（使用Bundle携带数据），handleMessage方法用于处理Message
- 任务可以即时处理（消息队列准备好的情况下），也可以延迟处理（自定义各种时间计划）
- 当一个应用的进程创建以后，它的主线程将专注于处理消息队列里的关于应用的顶层组件（Activity、BroadcastReceiver等），windows等的消息处理，你可以创建属于自己的线程，然后往主线程发送消息（创建一个绑定主线程Looper的Handler）

3、**Handler处理消息三个优先级**：先是Message自己带的Callback，再是Handler创建时候传入的Callback，最后是Handler自己的成员方法handleMessage
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/8f292750-1083-4779-bad7-e4dd3678d6ad)

4、Handler里都有啥？ Looper、MessageQueue（Looper里的，它拿着用于塞任务和移除任务）、Callback、IMessenger（Binder通信）
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/20f0d5a8-4bbe-4524-a02a-f2d5cd9c8435)
有一个对主线程 Handler 的复用缓存
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/7a3ee65d-dc64-4daf-bade-42059c6fbf60)
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/866e90ed-4bd6-4cbc-bc79-337b42bd240f)

5、主线程的 Handler 什么时候初始化的？ ActivityThread 的 main 方法里：
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/2c177f9e-1cbb-42d5-996b-7a94bdac3419)
所以，很多人问，为何不会卡死，这不是卡死不卡死问题，而是如果这个方法执行完了，整个main就退出了，线程结束，game over，这个线程干的活，就是不停的执行消息，不仅仅是用户的消息，还有绘制渲染的消息。
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/39b70ac2-60ca-4871-9c67-5eb370a2dd0b)

6、创建Handler，必须是处于有消息队列的线程，也就是调用了Looper.prepare()方法，然后创建Handler，最后调用Looper.loop()方法。Looper.loop()方法干了什么？
```java
class LooperThread extends Thread {
       public Handler mHandler;
 
       public void run() {
           Looper.prepare();
 
           mHandler = new Handler(Looper.myLooper()) {
               public void handleMessage(Message msg) {
                   // process incoming messages here
               }
           };
 
           Looper.loop();
       }
}
```

### Looper

1、Looper.prepare()干了什么？ 使用 ThreadLocal，塞了一个 Looper 对象
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/88bb054a-5413-43c5-8180-09b31b4313b8)
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/01e36bca-a958-4392-9aa4-e37d85d390fd)

2、Looper.loop()干了什么？ 从 ThreadLocal 拿出 Looper 对象，执行死循环，每次循环调用 loopOnce 方法
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/ba36cfad-c6c0-4bec-8770-9f1efef4af8a)
loopOnce 方法内，调用了 Handler 的 dispatchMessage 方法
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/90282d88-0e2f-4556-80d4-b2bd771cd96f)

3、loopOnce 方法干了什么？ 从Looper的消息队列拿消息，如果没有消息就阻塞
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/705b1b03-5758-4719-ae35-86873d571425)

### Message

1、实现了 Parcelable 接口，因为涉及到 Binder 通信

2、看看 Message 缓存机制，利用 static 拿住一个 message，如果有 message A 被回收了，就把 static 拿住的替换成 A，同时把 A 的 next 指向刚刚 static 的那个，相当于往链表前面插入值，同时指针指向刚刚加入的这个。默认 MAX_POOL_SIZE = 50
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/63e15074-d1de-4778-aaa9-da1f70cab842)
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/fbde70e0-d546-4455-b3a8-30eaa960e02a)

3、三种类型消息：同步消息、异步消息、屏障消息

### MessageQueue

1、MessageQueue里有啥？ next()方法拿消息时是否阻塞的标记、屏障消息的token、一些native方法（说明消息队列的实现靠的是底层实现）
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/c987f682-30db-43d6-95c9-57f8b4939741)

2、可以监听队列是否空闲（可能真空闲，也可能有消息，但是消息不是当前时间执行）
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/031acff7-8a00-496c-9e37-0738fcfb7219)

3、next()方法，获取消息，死循环直到拿到消息，**target是null表示屏障消息**，然后开始找异步消息
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/1697b2fa-98af-4941-ade2-7770f60a6531)

### HandlerThread

1、看看 HandlerThread 持有一个Looper，一个Handler，在线程启动的时候，准备Looper，准备Handler，启动Looper一直拿消息
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/94c10bd6-5646-4c1f-b21b-5bb72afddcb4)

### View 绘制
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/8e6b79a5-f30f-4aec-a6c5-90f834033513)
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/5724d455-7e9d-4ef2-8b63-3ce8c7e0d3c3)
先插入屏障消息（**没给target赋值**）
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/92ec347d-92dc-4a58-b398-226b0f004fe0)
再插入异步消息（mChoreographer.postCallback）
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/3007c5a7-cb63-47c8-915e-9671ab3efde1)


