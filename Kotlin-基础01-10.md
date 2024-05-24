
### 1. 默认参数

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/e9a3fe14-67f6-4131-9f1c-43997b2a468e)

### 2. 三引号文本，trimMargin，trimIndent

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/be4980ac-11f2-4cdf-9cdf-8ea0411b3f38)

### 3. 字符串模板 引用 a 使用 `$a` 或者 `${a}`

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/a9a64d47-8a15-484b-94c6-1c7ddb6bfb72)

### 4. 可空类型 ?.

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/fd23bc6a-790f-42b6-8a4b-9d78ba86bd44)

### 5. Nothing 类型，编译器遇到 Nothing 返回值，会知道这是一个unreachable code，从而终止后面逻辑，而不是编译报错

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/227436d2-0bed-4686-8f2e-afa8336d25a3)

### 6. lambda 表达式，如果一个方法A的入参要的是lambda表达式，那么实际传入的是一个匿名内部类实例，A方法内调用的此实例的唯一方法

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/4a4106a3-f5e1-41f6-ac3b-60692294e5cb)

### 7. Data class ，默认实现了equals、hashCode、toString，及其他方法（copy方法，但是是浅拷贝）

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/16f13200-5b5c-4dfc-aea3-a676567252ae)

### 8. 智能类型转换、when表达式

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/49308ac3-51a0-49f1-94e7-ea61e281317b)

### 9. 密封类、密封接口
1、一个密封类可以有多个子类，子类必须定义在同一个文件中，子类的子类不用遵守这个约定可以放到其他文件中
2、密封类本身不允许有公开构造方法，必须是Private的或者Protected的，因此密封类不能直接实例化
3、**密封类更像是一个不能实例化的抽象类**，它允许有抽象成员变量和抽象成员方法

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/8fdef240-0664-4a2a-877c-774a13afc67c)

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/1a9b00d0-73ec-422f-9a65-9d22d633fdc8)

### 10. 导包，as 别名解决导包冲突

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/37a185d5-92c6-4faf-8d79-4b525200e3da)

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/a7df953b-5a17-45ed-b9cf-df997431460b)


