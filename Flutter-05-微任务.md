#### 注意事项
- 避免在循环中创建大量微任务，避免微任务爆炸。不应该每个Function都放入一个微任务处理，应该启动一个微任务，在微任务中批量处理Function。
- 紧急任务用微任务，不紧急任务用队列（Timer.run()）

#### 执行microtask的方式
- 直接执行：scheduleMicrotask((){ ... });
- 包装成Future：Future.microtask((){ ... });
- 题外话：Future() 和 Future.microtask() 的区别是：前者在事件队列中执行，后者在微任务队列中执行。
  - **通过以下打印结果，也验证了一个事实，微任务队列先于事件队列被执行。**
  - 判断是微任务队列中运行的，堆栈信息中有`_microtaskLoop`。
  - Future.sync() 是同步执行，但也返回一个Future，目前大概没有特别需要的使用场景，除非外界要求返回值是Future的情况。
```dart
void main() {
  Future(() {
    return 404;
  }).then((event) {
    print('event: $event');
    print(StackTrace.current.toString());
  });

  Future.value(22).then((event) {
    print('event: $event');
    print(StackTrace.current.toString());
  });

  Future.microtask(() {
    return 500;
  }).then((event) {
    print('event: $event');
    print(StackTrace.current.toString());
  });

  getAge().then((event) {
    print('event: $event');
    print(StackTrace.current.toString());
  });

  Future.sync(() {
    return 200;
  }).then((event) {
    print('event: $event');
    print(StackTrace.current.toString());
  });
}

Future<int> getAge() async { // 一个工具方法
  return 30;
}

打印结果：
event: 22
#0      main.<anonymous closure> (package:untitled/test05.dart:13:22)
#1      Future._propagateToListeners.handleValueCallback (dart:async/future_impl.dart:948:45)
#2      Future._propagateToListeners (dart:async/future_impl.dart:977:13)
#3      Future._completeWithValue (dart:async/future_impl.dart:720:5)
#4      Future._asyncCompleteWithValue.<anonymous closure> (dart:async/future_impl.dart:804:7)
#5      _microtaskLoop (dart:async/schedule_microtask.dart:40:35)
#6      _startMicrotaskLoop (dart:async/schedule_microtask.dart:49:5)
#7      _runPendingImmediateCallback (dart:isolate-patch/isolate_patch.dart:127:13)
#8      _RawReceivePort._handleMessage (dart:isolate-patch/isolate_patch.dart:194:5)
event: 500
.. 同上
event: 30
.. 同上
event: 200
.. 同上
event: 404
#0      main.<anonymous closure> (package:untitled/test05.dart:8:22)
#1      Future._propagateToListeners.handleValueCallback (dart:async/future_impl.dart:948:45)
#2      Future._propagateToListeners (dart:async/future_impl.dart:977:13)
#3      Future._complete (dart:async/future_impl.dart:711:7)
#4      new Future.<anonymous closure> (dart:async/future.dart:265:14)
#5      Timer._createTimer.<anonymous closure> (dart:async-patch/timer_patch.dart:18:15)
#6      _Timer._runTimers (dart:isolate-patch/timer_impl.dart:423:19)
#7      _Timer._handleMessage (dart:isolate-patch/timer_impl.dart:454:5)
#8      _RawReceivePort._handleMessage (dart:isolate-patch/isolate_patch.dart:193:12)
```

- await 表达式的微任务。底层原理：await会被编译器转换为 then()，而 then() 的回调是在微任务中执行的。**但是我没验证出来，打印堆栈信息，没有出现`_microtaskLoop`**
```dart
Future<void> testAwait() async {
  print('1. Before await');
  await Future.value(42); // 这里会创建微任务！
  print('3. After await'); // 在微任务中执行
}

void main() {
  print('Start');
  testAwait();
  print('2. End of main');
}
```

- Stream 的监听回调是在微任务里执行的，这里和Future很像。
- Isolate 有自己独立的微任务队列
