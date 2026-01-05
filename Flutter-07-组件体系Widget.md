#### Widget类
- 位置在`flutter/src/widgets/framework.dart`文件中
- 是个抽象类，含有以下直接子类
  - 1. StatelessWidget
  - 2. StatefulWidget  
  - 3. RenderObjectWidget
  - 4. ProxyWidget
  - 5. PreferredSizeWidget

#### StatelessWidget
- 无状态管理的组件
  - 组件不可变，一旦创建就不可修改
  - 性能较好，可以频繁重建
  - 用于静态展示或仅依赖父组件数据的场景

#### StatefulWidget
- 有状态管理的组件
  - 组件有可变状态，可以响应交互
  - 通过 setState() 触发重建
  - 用于需要管理内部状态的场景

#### RenderObjectWidget
- 渲染对象组件（类似安卓里的布局）
  - 直接与底层的 RenderObject 关联
  - 控制布局、绘制、命中测试
  - 性能关键部件使用
- 主要子类有
  - 1. SingleChildRenderObjectWidget
    - SizedBox, Container, Padding, Align, Transform
  - 2. MultiChildRenderObjectWidget
    - Row, Column, Stack, Flex
  - 3. LeafRenderObjectWidget
    - Opacity, ClipRect, ClipOval

#### ProxyWidget
- 代理组件
  - 包装另一个 Widget
  - 通常用于修改子组件的行为或属性
  - 实现 InheritedWidget 等模式的基础
- 主要子类有
  - 1. InheritedWidget - 数据传递
  - 2. ParentDataWidget - 布局信息传递

#### PreferredSizeWidget
- 偏好尺寸组件
- 不是真正的 Widget 子类，但实现了 Widget 接口
- 用于指定偏好尺寸
- 常用于 AppBar、TabBar 等


