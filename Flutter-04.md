### 定时器Timer
- 引用import 'dart:async';
```dart
Timer? _timer; // 使用Timer类型名称定义，并且?表示可为null
_timer = Timer.periodic(const Duration(milliseconds: 100), (timer) {
   // 主线程回调
});

// 不用了以后，记得取消
_timer?.cancel();
```

### 私有成员 变量/方法
- 下划线开头表示这是私有变量，同文件中可以访问，不同文件中不可以访问
```dart
// 文件 a.dart 中
class A {
  Timer? _timer;
}
class B {
  void b() {
    var a = A();
    a._timer; // 可以访问  
  }
}

// 文件 b.dart 中
void main() {
  var a = A();
  a._timer; // 报错，不可访问
}
```
