#### 1、Java如何实现的跨平台？在不同操作系统上实现各自对应的JVM虚拟机（相同的接口，调用平台底层的能力），把Java代码编译成字节码文件，然后在JVM虚拟机上运行，而不是直接在具体的某个操作系统上运行，避免针对不同操作系统开发一套相同功能的代码，降低开发成本和维护成本。

#### 2、JDK和JRE的区别？JDK是java develop kits，是java开发工具包，包含了JRE和开发工具（javac等）。JRE是java runtime environment，是java运行时环境（JVM和必须的类库），有了JRE就可以运行java程序了。

#### 3、为什么需要配置Java环境变量？因为Java程序是一种可以跨平台运行的程序，而它的跨平台实现靠的是独立的Java运行环境，为了让系统在运行Java程序时找到这个运行环境，就需要通过配置path的方式告诉系统，运行环境在哪个位置。

#### 4、Java语言的特性？
1、面向对象：这是一种编程思想，编程理念，指导程序开发者如何去组织程序架构，开发出更简洁更易于维护且更好拓展的程序，利用封装，继承，多态的基本思想，进行程序设计。
2、内存回收：Java提供了自动的内存回收机制（堆内存，新生代，老年代，永久代（Java8移除改为元数据空间使用本地内存存储class等元数据），标记清除算法，内存泄漏，内存溢出）。
3、异常处理：Java在保障程序健壮性方面提供了异常处理机制（try、catch、throw抛出单个异常时使用、finally、throws用在方法上表明这个方法会抛出1个或多个异常）。
4、多线程编程：
5、发射机制：

#### 5、==和equals的区别？
1、==是操作符，用于比较两个变量的值，如果是基本数据类型，比较的是数值大小，如果是引用数据类型，比较的是内存地址。
2、equals是Object类的一个方法，允许子类重写，作用是比较两个对象的内容是否相等，默认情况下比较的是内存地址，我们可以重写这个方法，自定义内容比较的具体逻辑。
3、Java虚拟机内存中会默认存储128个Integer对象，所以在进行==比较时，前面128个对象是返回true的。
4、==两边有一个是数值类型的，那么会自动拆箱，以数值类型进行比较。
5、拆箱是Integer.intValue，装箱是Integer.valueOf
6、对于包装类型，equals方法不会进行类型转换
```Java
public class Main {
    public static void main(String[] args) {
         
        Integer a = 1;
        Integer b = 2;
        Integer c = 3;
        Integer d = 3;
        Integer e = 321;
        Integer f = 321;
        Long g = 3L;
        Long h = 2L;
         
        System.out.println(c==d);//true
        System.out.println(e==f);//false 超出了128个预加载对象
        System.out.println(c==(a+b));//true 拆箱对比
        System.out.println(c.equals(a+b));//true 装箱对比
        System.out.println(g==(a+b));//true 拆箱
        System.out.println(g.equals(a+b));//false 装箱，类型不一样
        System.out.println(g.equals(a+h));//true a+h自动装箱Long类型
    }
}
```

#### 6、Java中有哪些数学函数？
位于java.lang.Math中，有：
基本数学函数：abs绝对值、max、min。
指数函数：exp(a)求e的a次方、log、pow(a,b)求a的b次方、sqrt求平方根、cbrt求立方根。
三角函数：sin、cos、tan、asin、acos、atan、toRadians角度转弧度、toDegrees弧度转角度。
双曲函数：sinh、cosh、tanh、asinh、acosh、atanh。
其他函数：random随机数、round舍入函数。

#### 7、Java中的运算符？
&与运算，两个都是1，才为1。|或运算，两个有一个为1，就为1。~非运算，即位取反，1为0，0为1。^异或运算，两个操作数的位中，相同则结果为0，不同则结果为1。<<有符号左移运算，例如5<<35等同于左移3位，因为int一共32位，5左移3位就是5乘以2的3次方，等于40。>>有符号右移运算，例如16>>3，就是16除以2的3次方，等于2，15>>3，就是14(15-1)除以8，得到的整数商，等于1。>>>无符号右移，同右移，但是结果全变正数。没有<<<这个符号！！！

