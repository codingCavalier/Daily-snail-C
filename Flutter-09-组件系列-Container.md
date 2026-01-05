#### 文本
- 基本：Text('Hello, Flutter!')
- 带样式：Text('带样式的文本', style: TextStyle(fontSize: 24.0))

#### 容器Container
- 继承自StatelessWidget，是最常用的组件之一
- 属性：
  - child：只能有一个child，但是如果child是容器，就可以变相的放多个子组件了。
  - alignment：控制唯一的这个child在Container范围内的位置，一共9个枚举值（例如：Alignment.bottomRight是右下角）
  - width/height：指定容器的宽高，如果不指定，则容器充满父布局。这个宽高是已经包含了padding的尺寸的
  - padding：指定左上右下各个边的内边距，EdgeInsetsGeometry类型，和安卓的padding同理
  - margin：指定左上右下各个边的内边距，
  - color/decoration：指定背景装饰，**不能同时设置**，color就是背景色，decoration优先使用BoxDecoration，和安卓的Shape很像，可以设置背景色+描边以及描边粗细和位置等
  - foregroundDecoration：指定前景装饰
  - transform：变换矩阵，例如旋转，transform: Matrix4.rotationZ(30 * math.pi / 180)
  - <img width="960" height="600" alt="image" src="https://github.com/user-attachments/assets/52fd33aa-28f5-4dda-97be-6fce1069be13" />

- 注意：
  - 如果不设置宽高，也不设置alignment，那么Container会采用**包裹内容**的方式计算自己的大小
  - 不设置圆角时，ShapeDecoration 会有默认外部圆角
  - <img width="269" height="389" alt="image" src="https://github.com/user-attachments/assets/7d6d1d9b-16f5-4760-87a2-74f9a9238271" />
  - 内边距的100不是从高度320的顶部0的位置开始计算的，而是减去了decoration的border的内描边宽度后开始计算的
  - <img width="378" height="359" alt="image" src="https://github.com/user-attachments/assets/9694f391-14e4-484d-af66-84cd71f72c1f" />
  - 
- 不旋转的效果如下（代码在下面）：
- <img width="960" height="600" alt="image" src="https://github.com/user-attachments/assets/3f7f053c-01f3-453f-990a-a3780fd2645c" />

  ```dart
  return Container(
      alignment: Alignment.center, // 本身是居左居上的，这个center可以控制成居左居中。因为未指定宽高，默认是包裹内容
      color: Colors.green,
      child: Row(
        // 未指定宽度，默认最大化计算
        mainAxisAlignment: MainAxisAlignment.center, // 控制行内内容的对齐方式，这里为居中对齐
        children: [
          Container(
            alignment: Alignment.center,
            // 控制Container内的"你好"文本的对齐方式，这里为居中对齐
            decoration: ShapeDecoration(
              // 使用 ShapeDecoration 做对比
              color: Colors.red,
              shape: RoundedRectangleBorder(
                side: BorderSide(
                  color: Colors.black.withAlpha(50), // 描边颜色
                  width: 100.0, // 描边宽度
                  style: BorderStyle.solid,
                  strokeAlign: BorderSide.strokeAlignCenter, // 沿边缘线居中绘制描边（即一半描边处于红色装饰外）
                ),
                // borderRadius: BorderRadius.circular(20.0), // 不设置圆角时，ShapeDecoration 会有默认外部圆角
              ),
            ),
            // 内边距的100不是从高度320的顶部0的位置开始计算的，而是减去了decoration的border的内描边宽度后开始计算的
            padding: EdgeInsetsGeometry.all(100.0),
            height: 320,
            child: Text(
              "你好",
              textAlign: TextAlign.start,
              style: TextStyle(backgroundColor: Colors.deepOrange, fontSize: 24.0),
            ),
          ),
          Container(
            alignment: Alignment.center,
            decoration: BoxDecoration(
              color: Colors.red,
              border: Border.all(
                color: Colors.blue.withAlpha(50),
                width: 100.0, // 描边宽度
                style: BorderStyle.solid,
                strokeAlign: BorderSide.strokeAlignCenter,
              ),
              // borderRadius: BorderRadius.circular(20.0), // 不设置圆角时，BoxDecoration 没有默认外部圆角
            ),
            padding: EdgeInsetsGeometry.all(100.0),
            // 外边距100
            margin: EdgeInsetsGeometry.all(100.0),
            height: 320,
            child: Text(
              "你好",
              textAlign: TextAlign.start,
              style: TextStyle(backgroundColor: Colors.deepOrange, fontSize: 24.0),
            ),
          ),
        ],
      ),
    );
  ```
  
