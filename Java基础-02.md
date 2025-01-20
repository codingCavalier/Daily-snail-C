看看这个哥们总结的：https://www.javacn.site/interview/basic/

#### 1、finally中的代码一定会执行吗？
不一定。<br>
当try中有System.exit()方法时，程序会立即终止，finally中的代码将没有机会执行。<br>
当try中有Runtime.getRuntime().halt()方法时，程序也会立即终止，并且不会执行Hook方法和终结器。<br>
当try中有死循环或者死锁导致程序无法跳出try代码块时。<br>
非程序性的，例如 JVM崩溃了，或者停电了。<br>

#### 2、什么是Hook方法？
在编程中，钩子方法（Hook Method）是一种由父类提供的空或默认实现的方法，子类可以选择性地重写或扩展该方法，以实现特定的行为或定制化逻辑。<br>
钩子方法可以在父类中被调用，以提供一种可插拔的方式来影响父类的行为。钩子方法通常用于框架或模板方法设计模式中。框架提供一个骨架或模板，其中包含一些已经实现的方法及预留的钩子方法。<br>
具体的子类可以通过重写钩子方法来插入定制逻辑，从而影响父类方法的实现方式。

#### 3、ArrayList和LinkedList？
都是List接口的实现类。<br>
底层实现：ArrayList是基于动态数组的数据结构；LinkedList是基于链表的数据结构。<br>
随机访问的性能不同：ArrayList 优于 LinkedList <br>
插入删除性能不同：LinkedList 优于 ArrayList <br>

#### 4、ArrayList和Vector？
都是List接口的实现类，底层都是动态数组的实现。<br>
Vector是线程安全的，因此性能差于ArrayList。<br>
容量增长方式不同，ArrayList是增加一半（会频繁扩容）；而Vector是翻倍，所以后者适合大量数据的存储。

#### 5、为什么ConcurrentHashMap不能插入null？
HashMap 允许key和value都是null的。<br>
ConcurrentHashMap 都不允许。直接原因是源码中不支持插入null，会抛出空指针异常。深层原因是它的使用场景是多线程环境，也就是并发环境，如果允许null存在，就会出现二义性问题。<br>

二义性问题：<br>
所谓的二义性问题指的是代码或表达式存在多种理解或解释，导致程序的含义不明确或模糊。<br>
即null可以理解成“null”值，也可以理解成“没有”。HashMap是运行在单线程环境下，它不会存在“有”或者“没有”的不确定情况，因为是单线程操作。<br>
而ConcurrentHashMap却不能解释这个“有”或者“没有”是由于存储了null还是由于其他线程的操作导致的。<br>

