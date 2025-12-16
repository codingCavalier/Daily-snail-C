#### Stream
##### 简介
- 可以以流的方式持续不断产生数据的异步代码
- 

##### Stream的方法1（简单用法）
- 发射一个数据：Stream.value(1);
- 发射多个数据：Stream.fromIterable([1, 2, 3, 4, 5, 6, 7, 8, 9, 0]);
- 周期发送数据：Stream.periodic(Duration(milliseconds: 1000), (count) { return "第${count}次发送"; }); **count是发送次数，从0开始，可以自定义返回内容，但是返回内容必须是同一种数据类型**
- 从其他类型的Stream转换：Stream.castFrom<int, num>(Stream.fromIterable([0, 1])); **这里要求输入类型（这里是int）必须是输出类型（这里是num）的子类**
- 创建一个空的流：Stream.empty(); **什么数据也不发出，只是发出一个完成的事件回调**
- 创建一个错误：Stream.error(1, StackTrace.current);

- listen 方法：监听并处理流内每个数据，**返回StreamSubscription，可用于取消监听**
- forEach 方法：对流内每个数据，

```dart
void main() async {
  Stream.periodic(Duration(milliseconds: 1000), (count) { // 周期发送
    print('count: $count');
    return A(count);
  });

  StreamSubscription subscription = stream.listen(
    (item) { // 处理每个数据
      print('item0: $item');
    },
    onDone: () { // 流结束的回调
      print('done');
    },
    onError: (error, stackTrace) { // 发生错误的回调
      print('error: $error');
      print('$stackTrace');
    },
    cancelOnError: false, // 发生错误时是否取消监听
  );

  await Future.delayed(Duration(milliseconds: 2000)); // 2秒后取消监听
  subscription.cancel();
}

class A {
  int? _i;

  A(int i) {
    _i = i;
  }

  get i => _i;
}
```

##### Stream的方法2（从Future转换）
- 从Future转换：Stream.fromFuture(传入Future对象); **实际上是调用Future对象的then方法**
- 从Future转换：Future对象.asStream(); **实际上走的还是 Stream.fromFuture(传入Future对象); 方法**
- 从多个Future转换：Stream.fromFutures(传入Future列表); **实际上是遍历调用每个Future对象的then方法，处理正常结果和异常结果**

##### Stream的方法3（复杂用法）
- 复杂用法：允许多个监听：Stream.multi();
- 复杂用法：map转换：Stream.eventTransformed(Stream.fromIterable([0, 1, 2, 3, 4, 5, 6]), (output,) { return TransformSink(output); }); **TransformSink详见下方代码**
