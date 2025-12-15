### 概念
#### 线程
- Dart 中的线程概念，叫做`isolate`（和java、kotlin里的`thread`类似），翻译成中文叫隔离区。
- Dart 中的并发编程既包括异步 API（例如 Future 和 Stream），也包括允许你将进程转移到独立核心上的`isolate`。  
- 所有 Dart 代码都在`isolate`中运行，最初在默认的主`isolate`中运行，并可根据需要显式创建其他`isolate`。
- 当你启动一个新的`isolate`时，它拥有自己独立的内存空间和独立的事件循环。事件循环正是实现 Dart 中异步和并发编程的关键所在。

#### 事件循环
- Dart 的运行时模型基于事件循环。事件循环负责执行程序代码、收集和处理事件等任务。当应用程序运行时，所有事件都会被添加到一个称为事件队列的队列中。
- 事件可以是重新绘制用户界面的请求、用户的点击和按键操作，或是来自磁盘的输入/输出操作。由于应用程序无法预测事件发生的顺序，事件循环会按照事件在队列中的顺序逐个处理。

<img width="1531" height="184" alt="image" src="https://github.com/user-attachments/assets/ab2dc752-5092-475e-9b0f-66026497ade5" />

### 异步编程
#### Future
##### 简介
- Future 表示一个异步操作的结果，该操作最终会以一个值或一个错误完成。
- 打印线程名：Isolate.current.debugName
- 同步情况下休眠当前线程：sleep(Duration(milliseconds: 1000));

```dart
Future<String> _future() { // 定义一个返回Future对象的方法

  print('${Isolate.current.debugName}'); // main（表示主线程）

  return Isolate.run(() { // 相当于java、kotlin里的新建一个Thread，然后运行run方法

    sleep(Duration(milliseconds: 1000)); // 子线程sleep 1秒钟，模拟耗时操作

    print('${Isolate.current.debugName}'); // _RemoteRunner._remoteExecute（表示子线程）

    // throw HttpException("http 请求错误"); // 故意中途抛出异常
    // throw 401; // 可以抛出任何值
    throw false; // 可以抛出任何值

    return "Hello"; // 正常返回结果
  });
}
```

##### Future的方法
- then 方法：用于处理正常返回结果
- onError 方法：用于处理异常，可以根据方法体上的**泛型**自动筛选要处理的异常类型，比如`bool`、`int`；**test是函数参数**，用于对传入的异常做判断，返回true表示此 `onError` 方法要处理这个异常，返回false表示不处理。
- catchError 方法：用于处理异常，比 `onError` 方法更宽泛，如果所有的 `onError` 都没有处理该异常，则最终会由 `catchError` 尝试处理，之所以说尝试处理，是因为它也有test函数参数用于判断是否要处理该异常。
  - test函数参数：**不传此参数，默认表示该 `onError` 或 `catchError` 方法要处理此异常。**
- ignore 方法：忽略正确结果和异常结果。
- unawaited(Future对象) 方法：忽略正确结果，但是异常会正常抛出，注意处理。

##### Future的异常处理顺序
- 基本规则：先尝试走 `onError` ，再尝试走 `catchError` 。
- 有多个 `onError` 时：先匹配方法体上的泛型，再看test是否返回true，如果多个 `onError` 同时满足前面两个条件，那么走最先写的那个 `onError` 方法。
- 有多个 `catchError` 时：先看test是否返回true，如果多个 `catchError` 同时满足条件，那么走最先写的那个 `catchError` 方法。
- 如果所有的 `onError` 和 `catchError` 都没有处理这个异常，那么程序报错退出。

```dart
void main() {
  _future() // 调用一个可以返回Future对象的方法
      .then((result) { // 用于处理正常返回结果
        print('收到结果: $result');
      })
      .onError<bool>( // 只处理bool类型的异常
        (error, stackTrace) {
          print('报错了0: ${error.toString()}');
          print('stackTrace: ${stackTrace.toString()}');
        },
        test: (error) {
          return error == false;
        }
      )
      .onError<int>( // 只处理int类型的异常
        (error, stackTrace) {
          print('报错了1: ${error.toString()}');
          print('stackTrace: ${stackTrace.toString()}');
        },
        test: (error) {
          return error == 401;
        }
      )
      .catchError((error) {
        print('catchError0: $error');
      })
      .catchError( // 任何类型的异常都可以处理
        (error) {
          print('catchError1: $error');
        },
        test: (error) {
          return error == 401;
        }
      );
}
```
