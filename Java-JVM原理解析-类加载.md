
### 1. Class 文件 平台无关性
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/7c7debb9-4dd1-46b6-829e-067c2ffd562c)

### 2. Class 类文件结构 8字节为基础单位的二进制流，采用类C结构体的伪结构存储数据，主要由无符号数和表构成，整个Class文件本质上可以视作一张表
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/bcbe8ef2-106a-42c8-b599-485f32f2ac99)

### 3. 常量池中两大类常量：字面量(Literal)和符号引用(Symbolic References)

字面量：你可以理解为java的常量，如String常量，被声明为final的常量值等<br>
符号引用：属于编译原理方面的概念，主要包括以下几种常量：<br>
1.类和接口的权限定名<br>
2.字段的名称和描述符<br>
3.方法的名称描述符<br>

java文件编译后，需要在虚拟机加载Class文件的时候进行动态连接？什么是动态连接，这里涉及到Class文件加载，Class文件不会保存各个方法，字段在内存中的布局信息，当虚拟机在做类加载的时候，从常量池获取对应的符号引用，再在类创建时或者运行时解析，翻译到具体的内存之中。常量池中每一项都是一个表。 每个表互不干扰，数据结构完全独立

CONSTANT_Utf8_info：真正存储字符串的地方<br>
CONSTANT_String_info：本身不包含字符，但是其有个指针指向CONSTANT_Utf8_info中的字符串<br>
还有类中常量项：Class，Fieldref，Methodref，InterfaceMethodref，nameAndType<br>
可以看到常量池中只有CONSTANT_Utf8_info表是存储字符串的地方，其他表都或多或少有个指针指向CONSTANT_Utf8_info中的字符串<br>

前面我们讲解了class类组成：常量池，属性，Field_info，Method_info等等。这些只是一些静态内容，并不能驱使jvm去执行我们的函数代码。<br>
前面在讲解Code属性的时候，了解过，内部 code 数组存储了一个函数源码经过编译后得到的 JVM 字节码<br>

**栈帧**（Stack Frame）是用于支持虚拟机进行方法调用和方法执行的数据结构，它是虚拟 机运行时数据区中的虚拟机栈（Virtual Machine Stack）的栈元素。<br>
栈帧中存储了方法的 局部变量表、操作数栈、动态连接和方法返回地址、帧数据区 等信息。每一个方法从调用开始至执行完成的过程，都对应着一个栈帧在虚拟机栈里面从入栈到出栈的过程。<br>
一个线程中的方法调用链可能会很长，很多方法都同时处于执行状态。对于 JVM 的执行引擎来 说，在活动线程中，只有位于栈顶的栈帧才是有效的，称为当前栈帧（Current Stack Frame），与这个栈帧相关联的方法称为当前方法（Current Method）。执行引擎运行的所有 字节码指令都只针对当前栈帧进行操作。<br>

Java 中当一个方法被调用时会产生一个栈帧（Stack Frame）,而此方法便位于栈帧之内。而Java方法栈帧 主要包括三个部分，如下所示：<br>

1）、局部变量区<br>
2）、操作数栈区<br>
3）、帧数据区（常量池引用）<br>

帧数据区，即常量池引用在前面我们已经深入地了解过了，但是还有两个重要部分我们需要了解，一个是操作数栈，另一个则是局部变量区。通常来说，程序需要将局部变量区的元素加载到操作数栈中，计算完成之后，然后再存储回局部变量区。<br>

使用 jclasslib 这个字节码工具去查看字节码，使用效果如下图所示，代码编译后在菜单栏 ”View” 中选择 ”Show Bytecode With jclasslib”，可以很直观地看到当前字节码文件的类信息、常量池、方法区等信息<br>

原文链接：https://juejin.cn/post/7136540148329611278

### 4. 类加载

Java天生可以动态扩展的语言特性就是依赖运行期动态加载和动态连接这个特性实现的。我们应用程序使用的Class文件不一定是要安装运行的apk里面，也可以使用存放在本地或者远端的二进制Class文件。插件化、热修复。

加载、验证、准备、解析（可能在初始化后执行）、初始化、卸载

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/f1ff2595-b657-46b3-a3ae-5fd00cf288ca)

#### 加载：通过类的全限定名获取类二进制class文件字节流，将字节流静态存储结构转化为方法区运行时数据结构，在内存中生成一个代表这个类的Class对象作为方法区这个类的各种数据的访问入口

1. 针对获取二进制class文件字节流，并没有指定二进制字节流的存储位置，这个字节流可以来自本地或者远端，我们可以重写一个类加载器，重写findClass或者loadClass实现自己的二进制流获取方式。

加载阶段主要任务就是**加载二进制流，产生Class对象**。

#### 连接：包括验证、准备、解析

验证阶段：<br>
1. 文件格式验证
2. 元数据验证
3. 符号引用验证
4. 字节码验证

准备阶段是正式为类中定义的静态变量分配内存，并设置类变量的初始值<br>
1. 这时候的内存分配只包括类的静态变量，不包括实例的变量，实例变量会在对象实例化时一起分配到java堆中<br>
2. 这里的初始值，表示的数据的零值，而不是数据的真实值，因为value的真实赋值操作是在putStatic方法后，而putStatic方法是在类构造器cinit方法中，**所以会在类的初始化阶段给value赋值为真实值**<br>
3. 但是也有特殊情况：如使用final修饰的静态变量，**则在准备阶段就会赋值为真实的final值**

解析阶段就是将class文件常量池中的符号引用转换为虚拟机内存中的直接引用<br>
1. 类或者接口解析
2. 字段解析
3. 方法解析
4. 接口方法解析

通过上面的分析可知：连接阶段主要任务就是**完成将class文件中的类信息，加载到虚拟机的内存中，并给类的静态变量赋上零值**。

#### 初始化：初始化阶段就是执行类构造器<clinit>方法的过程，<clinit>方法是在编译器自动收集类中所有类变量的赋值动作和静态语句块static{}中的语句合并产生的

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/3f315477-c293-48b9-8d74-e312a08972a3)
1. 静态语句块只能访问定义在其之前的变量，对在其后的变量只能赋值不能访问
2. <clinit>方法执行前会优先加载父类的<clinit>，所以最先执行的肯定是Object的<clinit>方法
3. <clinit>方法在多线程情况下会加锁同步，多个线程同时初始化一个类，那么只会有其中一个线程执行这个类的

类初始化时机：
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/af8fd640-acae-4f03-b682-6a7e52de49fd)

### 类加载器：类加载器用于加载Class文件到内存中。
虽然两个类都是同一个路径下的同一个类，但是使用不同的类加载器ClassLoader加载，获取的Class类肯定不是同一个。<br>
类加载器在Android插件化框架中起着关键作用，基本上市面上使用的几种框架都需要自定义DexClassLoader或者PathClassLoader进行处理。

### 双亲委派模型

Java类加载器种类：<br>
1. 启动类加载器，加载<JAVA_HOME>\lib下的类。<br>
2. 扩展类加载器，加载<JAVA_HOME>\lib\ext下的类。<br>
3. 应用程序类加载器，加载用户路径上的类<br>
4. 自定义类加载器，自己定义的类加载器，比如用于插件化<br>

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/12bbfb22-ba91-4204-905b-a745ad1aa3d9)

**为什么这么做？？？**
1. 避免重复加载，当父加载器已经加载了该类的时候，就没有必要子ClassLoader再加载一次。
2. 安全性考虑，防止核心API库被随意篡改。

### Android 类加载器

1. ClassLoader 接口
2. BootClassLoader 加载framework层的类
3. SecureClassLoader 对ClassLoader的扩展，加强了安全性
4. BaseDexClassLoader 实现了大部分的dex加载功能，是PathClassLoader和DexClassLoader、InMemoryDexClassLoader的父类
5. PathClassLoader 加载应用程序的类，会加载/data/app目录下的dex文件以及包含dex的apk文件或者java文件
6. DexClassLoader 可以加载自定义dex文件以及包含dex的apk文件或jar文件，支持从SD卡进行加载。我们使用插件化技术的时候会用到
7. InMemoryDexClassLoader 用于加载内存中的dex文件

DexClassLoader：完全自己加载dex文件，自己指定dex优化后的文件输出路径。
PathClassLoader：不让指定dex优化后的文件输出路径，因为它是用来加载系统类，或者已安装的apk里的dex，然后统一将优化后的文件输出到/data/dalvik-cache/这个目录下。

**Android中插件化框架原理：**

将apk放到应用文件夹，如assert目录下，然后重写DexClassLoader，实现自己loadClass等方法，并使用反射替换系统中的类加载器，在类进行加载的时候，就会自动使用重写后的类加载器进行加载，这个时候就可以加载未安装的apk中的文件。

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/ff73bb84-19ef-4947-86db-e87cc2167325)


原文链接：https://juejin.cn/post/7140064468897595422

