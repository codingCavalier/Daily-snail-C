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
- textAlign：文本对齐，枚举值，例如 TextAlign.end 是右对齐（当 textDirection 是 TextDirection.ltr 时）
- textDirection：文本方向，枚举值，例如 TextDirection.rtl 是从右向左
- locale：语言，值的类型是 Locale，如 Locale('zh', 'CN') 表示中文，zh是语言代码，CN是国家代码
- softWrap：是否换行，true 表示自动换行
- overflow：溢出处理，枚举值，例如 TextOverflow.ellipsis 是添加省略号处理
- textScaler：字体大小缩放，TextScaler.noScaling 是不缩放，TextScaler.linear(0.5) 是缩小到0.5倍
- maxLines：最大行数，值是数字
- semanticsLabel：语义标签，用于解释原文本的内容是什么意思，猜测用于Web的情况比较多
- semanticsIdentifier：语义标签唯一ID，该ID可以通过自动化工具识别，而不依赖于文本的实际内容，这些内容在本质上可能是动态的。
- textWidthBasis：文本宽度基准，枚举值
  - TextWidthBasis.longestLine：表示以最长的行的宽度作为基准，即只占这么宽（自动换行后的一行也算一行）
  - TextWidthBasis.parent：则是多行文本时（即文本长度足以自动换行时）宽度占满父布局宽度，然后自动换行；单行文本时（即文本长度不足以自动换行时）宽度包裹内容
  - <img width="506" height="309" alt="image" src="https://github.com/user-attachments/assets/947a9d8f-eea5-4846-a4e2-97d473e85dee" />
- textHeightBehavior：定义如何在文本上方和下方应用[TextStyle.height]。
  - applyHeightToFirstAscent：是否将计算完的行高应用到第一行（自动换行后的第一行）的ascent上，默认true，表示ascent参与行高的计算，即行高值大，则ascent也同样增大，行高值小，则ascent也同样减小。false时，则表示采用字体默认ascent
  - applyHeightToLastDescent：是否将计算完的行高应用到最后一行（自动换行后的最后一行）的descent上，逻辑同上。
  - 左侧：applyHeightToFirstAscent=false, applyHeightToLastDescent=true，右侧true, true
  - <img width="505" height="194" alt="image" src="https://github.com/user-attachments/assets/44ef19e7-dec5-449b-b111-7edf8937cfe3" />
  - 左侧：applyHeightToFirstAscent=true, applyHeightToLastDescent=false，右侧true, true
  - <img width="504" height="193" alt="image" src="https://github.com/user-attachments/assets/779e8f40-38bc-4fb0-8676-1bf6b0ad6c2f" />
  - leadingDistribution：行距，枚举值，两行文字基线距离减去字号对应的字体高度后，剩余的高度（例如基线距离是12磅，字高是10磅，那么leading就是2磅）
    - TextLeadingDistribution.proportional：将文本的[leading]**按比例**分配到文本的 ascent 和 descent，以适应字体的上升下降比例。默认计算方式是：TextStyle.height * TextStyle.fontSize - TextStyle.fontSize，如果没有设置 TextStyle.height，则使用字体内部默认的leading
    - TextLeadingDistribution.even：将文本的[leading]**均匀地**分配到文本的 ascent 和 descent，如果 TextStyle.height 设置的比1.0小，那么leading将会是负值，即两行文字有重叠。even 是 CSS 常用处理方式。
    - textHeightBehavior里的leadingDistribution没有试出效果，下图是**TextStyle里的leadingDistribution试出的效果**，左侧：proportional，右侧：even
    - <img width="512" height="311" alt="image" src="https://github.com/user-attachments/assets/591deeb5-c739-42cb-b637-7d267098761e" />
- selectionColor：文本被选中时的颜色，试了Text组件是不能选择文本的，下图是**SelectableText试出的结果**
  - <img width="287" height="189" alt="image" src="https://github.com/user-attachments/assets/7ddc15ac-8573-43ad-ba5c-7037b080114f" />

```dart
/// StrutStyle 问题代码
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
- icon：IconData类型，例如Icons.abc
- size：尺寸大小，默认24
- color：填充的颜色
- shadows：阴影，支持多个阴影叠加，注意阴影的上下层顺序
- textDirection：绘制方向，枚举值，例如TextDirection.rtl，表示从右往左画，因为有些图标支持反向绘制，比如Icons.arrow_back这个箭头
- ltr方向画（原图）：
- <img width="80" height="81" alt="image" src="https://github.com/user-attachments/assets/0401d0f0-27ed-4fe4-8b15-d475f0de1114" />
- rtl方向画：
- <img width="106" height="88" alt="image" src="https://github.com/user-attachments/assets/0b6603c1-8cfa-4ead-840e-dd65026cf614" />


- applyTextScaling：当您有一个与文本关联的图标时，这特别有用，true表示使用文本的TextScaler参数来缩放图标，使得图标的视觉效果和文本大小一致
- blendMode

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
