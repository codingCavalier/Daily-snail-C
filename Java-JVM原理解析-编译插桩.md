
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

