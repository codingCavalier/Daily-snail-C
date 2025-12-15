### 简介
- 面向对象
- 程序入口是main函数
- 末尾写分号;
- 一切皆 Object，原文：Because every variable in Dart refers to an object—an instance of a class—you can usually use constructors to initialize variables

### 依赖管理
- 例如在 pubspec.yaml 文件中增加对 synchronized 库的依赖
```dart
dependencies:
  flutter:
    sdk: flutter

# 一些其他依赖
# ...

  synchronized: ^3.4.0
```

### 变量

#### var
- 定义一个变量，如果开始指定了类型，则后期不允许改变类型
```dart
var a = 1;
a = ""; // 报错
var b; // 默认值是null
b = ""; // 通过
b = 1; // 通过
```

#### Object
- 定义一个变量，和 var 相似，区别是：1、Object 任何时候都允许改变类型；2、Object 定义的变量没默认值，使用前必须初始化，而 var 定义的变量有默认值null
```dart
Object c;
print('c=$c') // 报错，c没初始化
c = ""; // 通过
c = 1; // 通过
```

#### dynamic
- 定义一个变量，和 Object 相似，区别是：1、dynamic编译期不检查类型，程序员写什么就是什么，相当于告诉系统，你信我就对了
```dart
Object d = "";
d.abc() // 报错，因为根本没有abc这个方法
dynamic e = "";
e.abc() // 通过，但是运行时肯定报错，毕竟确实没有abc这个方法
```

#### 使用具体类型定义
- 直接使用具体类型定义，比如`Timer? _timer`，`?`表示可空，`_`下划线表示私有（同一个dart文件内的多个类之间可以访问到，不同dart文件就访问不到了）
```dart
class A {
  Timer? _timer;
}
```
