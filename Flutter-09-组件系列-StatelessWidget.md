#### 文本Text
- 继承自StatelessWidget，是最常用的组件之一
- 基本：Text('Hello, Flutter!')
- 带样式：Text('带样式的文本', style: TextStyle(fontSize: 24.0))
- 带支柱样式：StrutStyle
  - 使用场景：
    - 多行文本需要统一行高时​，保持相同行距
    - 不同大小字体混合排版时​，想要基线对齐
    - 列表项高度不一致时​，想要每行高度相同
    - 复杂文本布局时​，想要精确控制布局
  - 注意：
    - StrutStyle.height：表示一个倍数，用于乘上原行高计算新行高
    - 默认行高的计算：TextStyle.fontSize * 1.25，印刷业惯例，https://zhuanlan.zhihu.com/p/696502299
    - 实际行高的计算：(StrutStyle.fontSize 或 默认行高) * StrutStyle.height，其实比这个要复杂，具体逻辑还没搞清楚，有知道的朋友可以留言解释一下。
    - 如果StrutStyle设置了fontSize和height，那么行高会选择从它们的乘积，与默认行高，中最大的值，去设置。
      - 比如默认行高20 * 1.25 = 25，但是StrutStyle设置的行高是20 * 1 = 20，那么实际行高还是25，不会采用20
    - 如果StrutStyle没有设置fontSize但是设置了height，那么行高是默认行高和height的乘积。
    - 下图：左侧行高是10 * 1.25 * 2.0 = 25.0，右侧行高是20 * 1.0 = 20.0
    - <img width="291" height="293" alt="image" src="https://github.com/user-attachments/assets/0f69544d-218b-4a1c-9fce-8123ae1f8978" />
  - 有个问题：代码如下，情况ABC渲染效果相同，但是情况D渲染效果和ABC不同，有知道的朋友可以留言解释一下。
  - <img width="496" height="289" alt="image" src="https://github.com/user-attachments/assets/83bd7849-4930-44a0-8e41-f98cbd1c49a7" />

```dart
Container( // 情况A
  alignment: Alignment.center,
  child: Text(
    "你好\nHello",
    style: TextStyle(backgroundColor: Colors.deepOrange, fontSize: 50.0), // 1.25倍行距
    strutStyle: StrutStyle(fontSize: 9, height: 4.0),
    textAlign: TextAlign.start,
  ),
),
Container( // 情况B
  alignment: Alignment.center,
  child: Text(
    "你好\nHello",
    style: TextStyle(backgroundColor: Colors.deepOrange, fontSize: 50.0), // 1.25倍行距
    strutStyle: StrutStyle(fontSize: 14, height: 2.8),
    textAlign: TextAlign.start,
  ),
),
Container( // 情况C
  alignment: Alignment.center,
  child: Text(
    "你好\nHello",
    style: TextStyle(backgroundColor: Colors.deepOrange, fontSize: 50.0), // 1.25倍行距
    strutStyle: StrutStyle(fontSize: 22, height: 2.0),
    textAlign: TextAlign.start,
  ),
),
Container( // 情况D
  alignment: Alignment.center,
  child: Text(
    "你好\nHello",
    style: TextStyle(backgroundColor: Colors.deepOrange, fontSize: 50.0), // 1.25倍行距
    strutStyle: StrutStyle(fontSize: 10, height: 4.0),
    textAlign: TextAlign.start,
  ),
),
```

#### 图标Icon
- 继承自StatelessWidget，是最常用的组件之一
- Icon(Icons.holiday_village, color: Colors.green, size: 100)
- 

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
      // transform: Matrix4.rotationZ(30 * math.pi / 180), // 矩阵变换
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

  #### 内边距Padding
  - SingleChildRenderObjectWidget
