#### for循环
```dart
  // 不需要index
  var list = [1, 2, 3, 4];
  for (var i in list) {
    print(i);
  } // 1 2 3 4

  // 需要index
  var message = StringBuffer("Hello dart ");
  for (var i = 0; i < 5; i++) {
    message.write("!");
  }
  print(message); // Hello dart !!!!!

  // 使用模式，其实就是解构
  // 提前定义一个 People 类
  // class People {
  //   final String name;
  //   final int age;
  //
  //   People(this.name, this.age);
  // }
  var peopleList = [People("baby", 3), People("girl", 20)];
  for (final People(:name, :age) in peopleList) {
    print('$name - $age');
  }

  // 直接传入一个方法
  peopleList.forEach(print);

  // 闭包，Dart 中 for 循环内的闭包会捕获索引的值。这避免了 JavaScript 中常见的一个陷阱。例如，考虑以下情况：
  var callbacks = [];
  for (var i = 0; i < 2; i++) {
    callbacks.add(() => print(i));
  }
  for (final c in callbacks) {
    c();
  } // 正常输出 0 1, 但是 JavaScript 会输出 2 2, 除非让 JavaScript 使用 let 定义 i(不用var), 则可以输出 0 1
```

#### while和do..while循环
```dart
  // while循环
  var i = 0;
  while (i < 10) {
    i++;
    print(i);
  }

  // do..while循环
  i = 0;
  do {
    print(i);
    i++;
  } while (i < 10); // 0 - 9
```

#### 退出循环(break, continue, 标签)
```dart
  var i = 0;
  while (i < 10) {
    i++;
    if (i == 4) continue;
    if (i == 6) break;
    print(i);
  } // 1 2 3 5

  // 标签，配合 break 使用，退出循环
  label:
  for (var j = 0; j < 10; j++) {
    for (var k = 0; k < 10; k++) {
      print("$j, $k;");
      if (k == 2) break label;
    }
  } // 0, 0; 0, 1; 0, 2

  // 标签，配合 continue 使用
  outerLoop:
  for (var i = 1; i <= 3; i++) {
    for (var j = 1; j <= 3; j++) {
      if (i == 2 && j == 2) {
        continue outerLoop;
      }
      print('i = $i, j = $j');
    }
  } // 打印结果里不会出现 i = 2, j = 2
```
