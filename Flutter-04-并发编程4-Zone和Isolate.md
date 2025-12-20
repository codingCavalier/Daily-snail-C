#### Zone
-​ Zone 是 Dart 中管理异步代码执行上下文的机制，可以理解为 JavaScript 中的 "zone.js"，但更强大。它是 Dart 异步编程的底层基础设施。
- Zone.current 执行代码时所在的 Zone 。
- Zone.fork() 创建继承父 Zone 的新 Zone 。
- runZoned() 在指定 Zone 中运行代码。**内部是调用的fork方法，创建了新的Zone**
```dart
print('${Zone.current}'); // Instance of '_RootZone'

Zone zone = Zone.current.fork();
print('${zone}'); // Instance of '_CustomZone'

runZoned(() {
  print('zone: ${Zone.current}'); // Instance of '_CustomZone'
});
```

##### Zone 的应用
- 错误隔离
```dart
runZoned(() {
  Future.error('Error inside zone');
}, onError: (error, stackTrace) {
  print('Zone caught error: $error'); // 这里处理错误，不会崩溃应用
});

Future.error('Error outside zone'); // 会崩溃应用
```

- 异步操作拦截。**没太懂内涵**
- 内部存储空间，类似Java的ThreadLocal。例如：Zone.current['request_id'] = '12345'; 只能在当前Zone内访问，出了Zone就访问不到了。
- 自定义Zone行为
```dart
ZoneSpecification(
  // 1. 调度相关
  scheduleMicrotask: (self, parent, zone, task) {
    // 拦截微任务调度
    return parent.scheduleMicrotask(zone, task);
  },
  
  // 2. Timer 相关
  createTimer: (self, parent, zone, duration, callback) {
    // 拦截 Timer 创建
    return parent.createTimer(zone, duration, callback);
  },
  createPeriodicTimer: (self, parent, zone, period, callback) {
    // 拦截周期性 Timer
    return parent.createPeriodicTimer(zone, period, callback);
  },
  
  // 3. 错误处理
  handleUncaughtError: (self, parent, zone, error, stackTrace) {
    // 处理未捕获错误
    parent.handleUncaughtError(zone, error, stackTrace);
  },
  
  // 4. Fork 行为
  fork: (self, parent, zone, specification, zoneValues) {
    // 自定义 fork 行为
    return parent.fork(zone, specification, zoneValues);
  },
  
  // 5. 打印
  print: (self, parent, zone, line) {
    // 拦截所有 print
    parent.print(zone, '[ZONE] $line');
  },
  
  // 6. 注册回调
  registerCallback: (self, parent, zone, callback) {
    // 拦截回调注册
    return parent.registerCallback(zone, callback);
  },
  
  // 7. 运行
  run: (self, parent, zone, f) {
    // 拦截 run
    return parent.run(zone, f);
  },
  runUnary: (self, parent, zone, f, arg) {
    // 拦截带一个参数的 run
    return parent.runUnary(zone, f, arg);
  },
  runBinary: (self, parent, zone, f, arg1, arg2) {
    // 拦截带两个参数的 run
    return parent.runBinary(zone, f, arg1, arg2);
  },
)
```
- 实用示例：具有性能监控能力的 Zone
```dart
class PerformanceZone {
  final Map<String, int> _metrics = {};
  final Stopwatch _stopwatch = Stopwatch();
  
  ZoneSpecification get specification => ZoneSpecification(
    scheduleMicrotask: (self, parent, zone, task) {
      _metrics['microtasks'] = (_metrics['microtasks'] ?? 0) + 1;
      return parent.scheduleMicrotask(zone, task);
    },
    
    createTimer: (self, parent, zone, duration, callback) {
      _metrics['timers'] = (_metrics['timers'] ?? 0) + 1;
      return parent.createTimer(zone, duration, callback);
    },
    
    run: (self, parent, zone, f) {
      _stopwatch.start();
      try {
        return parent.run(zone, f);
      } finally {
        _stopwatch.stop();
        _metrics['total_time_ms'] = _stopwatch.elapsedMilliseconds;
        print('Performance metrics: $_metrics'); // 打印每个任务的耗时
      }
    },
  );
  
  void run(void Function() body) {
    runZoned(body, zoneSpecification: specification);
  }
}

// 使用
void main() {
  PerformanceZone().run(() {
    scheduleMicrotask(() {});
    Timer(Duration(milliseconds: 100), () {});
    print('Task completed');
  });
}
```
- Zone 的切换
```dart
void zoneSwitching() {
  final zone1 = Zone.current.fork(zoneValues: {'name': 'Zone1'});
  final zone2 = Zone.current.fork(zoneValues: {'name': 'Zone2'});
  
  zone1.run(() {
    print('In ${Zone.current["name"]}'); // Zone1
    
    // 显式切换到另一个 Zone
    zone2.run(() {
      print('Now in ${Zone.current["name"]}'); // Zone2
    });
    
    print('Back in ${Zone.current["name"]}'); // Zone1
  });
}
```
- Zone 的嵌套
  - **理解一下输出的结果，为什么是这样的**
```dart
runZoned(() {
  runZoned(() {
    print('This will be intercepted');
  }, zoneSpecification: ZoneSpecification(
    print: (self, parent, zone, line) {
      parent.print(zone, '[Inner] $line');
    },
  ));
}, zoneSpecification: ZoneSpecification(
  print: (self, parent, zone, line) {
    parent.print(zone, '[Outer] $line');
  },
));

打印结果：
[Outer] [Inner] This will be intercepted
```

#### Isolate
