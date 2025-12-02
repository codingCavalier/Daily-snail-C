### 数据类型

#### 类型概述
##### 内置类型
- Numbers (int, double)
- Strings (String)
- Booleans (bool)
- Records ((value1, value2))
- Functions (Function)
- Lists (List, also known as arrays)
- Sets (Set)
- Maps (Map)
- Runes (Runes; often replaced by the characters API)
- Symbols (Symbol)
- The value null (Null)

##### 其他类型
- Object: The superclass of all Dart classes except Null.
- Enum: The superclass of all enums.
- Future and Stream: Used in asynchronous programming.
- Iterable: Used in for-in loops and in synchronous generator functions.
- Never: Indicates that an expression can never successfully finish evaluating. Most often used for functions that always throw an exception.
- dynamic: Indicates that you want to disable static checking. Usually you should use Object or Object? instead.
- void: Indicates that a value is never used. Often used as a return type.

#### Numbers
- 分为 int 和 double，int表示不超过64位的数字(native -2^63 to 2^63 - 1，web端 -2^53 to 2^53 - 1)，double表示64位浮点数
- int 和 double 都是 num 的子类，num 类中定义了很多操作符，int 类中定义了很多二进制操作符，如果没有喜欢的，可以去 dart:math 库里找找看
- 没有小数点用int，有小数点用double
- 字符串转数字：int.parse('1'); double.parse('1.1');
- 数字转字符串：1.toString(); 1.1.toStringAsFixed(2);
- 如果字符串判断里涉及到了数字，可能要用表达式去表示，因为不同平台计算完的结果可能不一样，见下方示例
```dart
int e = 1;
double f = 1.1;
int g = 0xABCDEF;
double h = 0xFFBBCC;
var i = 2.2;

num j = 1; // num 支持 int 和 double
j += 2.5;

double k = 1; // int 可以赋值给 double
// int l = 1.1; // 报错
print('e=$e, f=$f, g=$g, h=$h, i=$i, j=$j, k=$k'); // e=1, f=1.1, g=11259375, h=16759756.0, i=2.2, j=3.5, k=1.0

var l = 1 == int.parse('1');
var m = 1.1 == double.parse('1.1');
print('l=$l, m=$m'); // l=true, m=true

var n = '1' == 1.toString();
var o = '1.234' == 1.234.toString();
var p = '1.234e+3' == 1234.toStringAsExponential(3); // 保留3位小数，四舍五入，且输出指数形式
var q = '1.234e+0' == 1.234.toStringAsExponential(3);
var r = '1.2' == 1.234.toStringAsFixed(1); // 保留1位小数，四舍五入
print('n=$n, o=$o, p=$p, q=$q, r=$r'); // n=true, o=true, p=true, q=true, r=true

```
```dart
void main() {
  var count = 10.0 * 2;
  var message = "$count cows";
  if (message != "20.0 cows") throw Exception("Unexpected: $message");
  // 正确写法是：
  // if (message != "${20.0} cows") throw ...
  // 因为Web端计算完了，count 是 20，而不是20.0
}
```

#### Strings
- 单引号或者双引号都行
- 允许“+”号拼接，允许==比较内容是否相同
- 创建多行文本，使用"""..."""或者'''...'''
- 创建raw字符串，使用r开头，raw字符串里不会将反斜杠\视作转义
- 常量字符串和常量数字，组成的值依然是常量。非常量数字和非常量字符串不能组成常量值
```dart
var s = """
1
  2
3
  4
5"""; // 5后面紧跟"""符号，则没有多余换行
  print(s);
// 1
//   2
// 3
//   4
// 5

var t = r'raw string with \n and \\';
print(t); // raw string with \n and \\
print('${t.endsWith(r"\\")}'); // true
print('${t.contains(r"\n")}'); // true
print('${t.codeUnitAt(1)}'); // 97

const u = "abcd";
const v = 123;
const w = "$u $v";
// const x = "$t $v"; // 报错, t 不是常量
```

#### Booleans
- 没什么特殊的，true/false

#### Records
- 类似于Kotlin里的超级版的Pair或者Triple
- 可以直接存入合法的数据类型的值，取的时候按照顺序$1开始取
- 也可以存入key: value形式的值，key不能是基本类型（int, double, String, bool）
- 定义：(String, int) record; 或者直接实例化 var record = ("abc", 1);
- 比较：定义不同，肯定是false；定义相同，则比较参数值；
- 赋值：定义相同，可以互相赋值；使用父类类型定义，可以赋值子类类型；
- 结构：直接写结构，无需定义类；（比Kotlin的Triple强的地方，就是它可以定义参数名，而且参数数量不受限制）
- 可以作为返回值类型或者参数类型
```dart
var record = ("aa", "bb", 3, a: 2, f: 7.2356, false);
print(record); // (aa, bb, 3, false, a: 2, f: 7.2356), 可以发现没有参数名的参数"false"被提到了有参数名的参数前面
print(record.$1); // aa
print(record.$2); // bb
print(record.$3); // 3
print(record.$4); // false
print(record.a); // 2
print(record.f); // 7.2356

var record0 = ("aa", false, "bb", 3, a: 2, f: 7.2356);
print(record0); // (aa, false, bb, 3, a: 2, f: 7.2356)

(String, int) record1; // 定义
// record1 = (1, 1); // 报错
record1 = ("", 12); // 通过
print(record1);

({int a, bool b}) record2; // 定义，并且指定具体的类型和参数名
record2 = (a: 1, b: false);

({int x, bool b}) record3;
record3 = (x: 1, b: false);

print(record2 == record3); // false，因为有参数名不一样，一个是a，一个是x；同理，如果都是a，但是a的值不一样，也是不相等的；

({bool b, int a}) record4; // {}里定义的参数，表示顺序无关
record4 = (a: 3, b: false);
record2 = record4; // 定义相同，就可以赋值；

(num, Object) record5 = (30, "123"); // (num, Object) 类型的 Record
print(record5.$1.runtimeType); // int
record5 = (20.33, false); // 允许赋值
print(record5.$1.runtimeType); // double

var buttons = [ // 数组
  ( // 直接写结构，无需额外定义类
    name: "按钮1",
    onClick: () => print('click1')
  ),
  (
    name: "按钮2",
    onClick: () => print('click2')
  ),
];

// 定义方式1（别名）
typedef ButtonItem = ({String label, int icon, void Function()? onPressed});

// 定义方式2（aaa有点像构造函数，b是入参的参数名）
extension type ButtonItem.aaa(
({String label, int icon, void Function()? onPressed}) b
) {
  String get label => b.label;

  int get icon => b.icon;

  void Function()? get onPressed => b.onPressed;

  ButtonItem({
    required String label,
    required int icon,
    void Function()? onPressed,
  }) : this.aaa((label: label, icon: icon, onPressed: onPressed));

  bool get hasOnPressed => b.onPressed != null;
}
```

#### Collections
##### Lists
- 没啥区别
```dart
var list = [1, 1, 2, 2, 3, 4, 5, 6];
list[0] = 2; // 通过
// list[2] = "22"; // 编译期报错

var list1 = const [1, 2, 3, 4, 5]; // 定义成常量
// list1[1] = 1; // 运行时报错，Unsupported operation: Cannot modify an unmodifiable list

var list2 = <int>[]; // 定义
list2.add(2);
list2.add(2);
list2.add(2);
print(list2); // [2, 2, 2]
// list2[3] = 3; // 报错，RangeError (index): Invalid value: Not in inclusive range 0..2: 3

List<int> list3 = []; // 定义
```

##### Sets
- 有序
```dart
var set = {1, 1, 1, 3, 4};
print(set); // {1, 3, 4}
print(set.elementAt(2)); // 4

// var set1 = const {1, 1, 1}; // 报错，常量set里的值不能出现相同值
var set1 = const {1, 2, 3}; // 定义成常量
// set1.add(2); // 运行时报错，Unsupported operation: Cannot change an unmodifiable set

var set2 = <int>{}; // 定义
Set<int> set3 = {}; // 定义

// var set4 = {}; // 这不是set，这是map
```

##### Maps
- 
```dart
var map = {}; // 定义，泛型是<dynamic, dynamic>
map["123"] = "111";
map[1] = 1;
print(map); // {123: 111, 1: 1}

var map1 = <String, int>{};
// map1[1] = 1; // 报错，key类型不对
map1["123"] = 123;
var map2 = Map<String, String>(); // 和map1的定义方式一样

var map3 = {
  // Key:    Value
  'first': 'partridge',
  'second': 'turtledoves',
  'fifth': 'golden rings',
};

// 常量map
final map4 = const {2: 'helium', 10: 'neon', 18: 'argon'};
map4[33] = "hello"; // 运行时报错，Unsupported operation: Cannot modify unmodifiable map
```
