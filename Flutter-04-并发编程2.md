#### Stream
##### 简介
- 可以以流的方式持续不断产生数据的异步代码

##### Stream的方法1（产生Stream）
- 发射一个数据：Stream.value(1);
- 发射多个数据：Stream.fromIterable([1, 2, 3, 4, 5, 6, 7, 8, 9, 0]);
- 周期发送数据：Stream.periodic(Duration(milliseconds: 1000), (count) { return "第${count}次发送"; }); **count是发送次数，从0开始，可以自定义返回内容，但是返回内容必须是同一种数据类型**
- 从其他类型的Stream转换：Stream.castFrom<int, num>(Stream.fromIterable([0, 1])); **这里要求输入类型（这里是int）必须是输出类型（这里是num）的子类**
- 创建一个空的流：Stream.empty(); **什么数据也不发出，只是发出一个完成的事件回调**
- 创建一个错误：Stream.error(1, StackTrace.current);

- 从Future转换：Stream.fromFuture(传入Future对象); **实际上是调用Future对象的then方法**
- 从Future转换：Future对象.asStream(); **实际上走的还是 Stream.fromFuture(传入Future对象); 方法**
- 从多个Future转换：Stream.fromFutures(传入Future列表); **实际上是遍历调用每个Future对象的then方法，处理正常结果和异常结果**

##### Stream的方法2（常用方法）
- 结果处理：
  - listen 方法：监听并处理流内每个数据，**返回StreamSubscription，可用于取消监听**
  - forEach 方法：对流内每个数据，均执行action操作
  - reduce 方法：对数据做合并处理，返回的Future最终结果将返回计算后的最终值
  - fold 方法：同reduce，一模一样，但是fold方法有初始值，流里的第1个数据和初始值做计算，而不是像reduce那样1和2做计算
  - drain 方法：忽略流内所有数据，并最终返回一个固定值，**大概使用场景是不关心流内数据，但是关心最终结果，目的就是等流结束**
- 数量控制：
  - take takeWhile skip skipWhile 等方法：提供了对数量控制的简便方法，比如只取多少个，或者满足条件时才取，跳过多少个，或者满足条件时才跳过
  - distinct 方法：和前面一个数据比较，去重，**只和前面一个数据对比，大概使用场景是为了避免连续的两次重复数据**
  - where 方法：类似于kotlin里的filter方法，做数据筛选用
- 筛选查找：
  - firstWhere 方法：类似于kotlin里的firstOrNull方法，不过它可以提供缺省值，即假如找不到符合条件的数据，则返回缺省值
  - lastWhere 方法：和firstWhere方法类似，从头找到尾，当数据流结束后，给出最后一个符合条件的数据，也可以提供缺省值
  - any every contains elementAt 等方法：都是返回一个Future，当Stream里条件满足时，Future执行

- timeout 方法：判定**每个数据**是否发送超时，如果超时，则**每次超时的时候**都执行缺省方法
- 

- map 方法：类似于kotlin里的map方法，做数据转换用

```dart
void main() async {
  Stream stream = Stream.periodic(Duration(milliseconds: 1000), (count) { // 周期发送
    print('count: $count');
    return A(count);
  });

  // 测试1
  StreamSubscription subscription = stream
    .timeout(
        Duration(milliseconds: 1000),
        onTimeout: (controller) { // 超时发生时的缺省方法
          print('超时了');
          controller.add(A(404));
        },
    )
    .map((input) { // 转换
      return B(200);
    })
    .listen(
      (item) { // 处理每个数据
        print('item0: ${item.i}');
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

  // 测试2
  await stream.contains(A(3)).then((result) {
    print('找到了3: $result');
  });
}

class A {
  int? _i;

  A(int i) {
    _i = i;
  }

  get i => _i;

  @override
  bool operator ==(Object other) { // 重写 == 和 hashCode
    return other.runtimeType == runtimeType && other.hashCode == hashCode;
  }

  @override
  get hashCode => i;
}
```

##### Stream的方法3（数据转换）
- 调用 stream 对象的 map 方法：对每个数据做转换
- 创建转换类：Stream<A> stream = Stream.eventTransformed(Stream.fromIterable([0, 1, 2, 3, 4, 5, 6]), (output,) { return TransformSink(output); }); **TransformSink 详见下方代码**，如下示例，是将int流转换成A类型的流，当event为3时，发射一个A对象

```dart
class TransformSink implements EventSink {
  EventSink<A>? _output;

  TransformSink(EventSink<A> output) {
    _output = output;
  }

  @override
  void add(event) {
    if (event == 3) {
      _output?.add(A(event));
    }
  }

  @override
  void addError(Object error, [StackTrace? stackTrace]) {
    _output?.addError(error, stackTrace);
  }

  @override
  void close() {
    _output?.close();
  }
}
```

##### Stream的方法4（多监听）
- **允许被多个listener监听**：Stream.multi(functionA);
- 每次被监听，都会回调functionA，然后controller就是监听的一方，可以通过add给它发射数据，或者close关闭流，也可以设置一个当监听取消时的回调。
- 一般是将一个普通Stream转换成多监听Stream，会用一个集合拿住所有的controller，当普通Stream有数据来临时，遍历调用每个controller

```dart
Stream stream = Stream.multi((controller) {
  // controller.add(event);
  // controller.close();
  controller.onCancel = () { // 当监听取消时的回调
    
  };
});
```

