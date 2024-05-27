
CAS 是 compare and swap 的简称，是一种无锁原子算法，映射到操作系统，就是一条CPU的原子操作指令，其作用是让CPU先进行比较两个值是否相等，然后原子地更新某个位置的值，是靠硬件实现，在硬件层面提升效率。

### 执行过程
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/0a59b369-80ea-4f7b-bbcb-790a29163a82)

### 示例
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/c972a011-e6af-4081-a239-3569e2d2eece)

### 缺点
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/41d7c18a-6aad-4bd3-9c49-53b04a53fb94)

#### ABA问题，可以靠 AtomicStampedReference 解决，这也是 Doug Lea 大哥写的，原理是里面定义了一个Pair类，将真实对象引用和一个stamp装箱到一起，stamp类似于一个版本号，每次 compareAndSet 需要比对两个值

原文链接：https://blog.csdn.net/qq_42370146/article/details/105559575
