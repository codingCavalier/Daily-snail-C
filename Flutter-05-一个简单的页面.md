- main方法是程序主入口
- runApp启动app
- DemoApp是主页
- 连接外部设备，例如手机或者平板。选择设备，选择要运行的dart文件，点击运行
- <img width="314" height="56" alt="image" src="https://github.com/user-attachments/assets/acf9209a-164a-45b8-9109-a3cbfd6bf915" />
- 也可以直接运行到网页里或者windows端
- <img width="164" height="69" alt="image" src="https://github.com/user-attachments/assets/17a9e9f0-5066-4a43-8eaa-d3f65425d5fe" />
- 平板内运行效果
- <img width="960" height="600" alt="image" src="https://github.com/user-attachments/assets/8731ecac-67b5-449d-9abb-d21ad453845b" />
- label这里写安卓的应用名称
- <img width="784" height="112" alt="image" src="https://github.com/user-attachments/assets/4cf9fc15-7e32-400b-8b5d-bff327e23823" />

```dart
import 'package:flutter/material.dart';

void main(List<String> args) {
  runApp(MaterialApp(
    title: "安卓切换任务管理时的任务名",
    home: DemoApp(),
  ));
}

class DemoApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        title: "MaterialApp title",
        home: Scaffold(
          appBar: AppBar(
            title: Text("App Bar 展示内容"),
          ),
          body: Center(
            child: Text("Hello Flutter"),
          ),
          bottomNavigationBar: BottomNavigationBar(
            type: BottomNavigationBarType.fixed,
            items: [
              BottomNavigationBarItem(icon: Icon(Icons.work), label: "工作"),
              BottomNavigationBarItem(icon: Icon(Icons.security), label: "安全"),
            ],
            onTap: (index) {
              print('$index 被点击了');
            },
          ),
        ));
  }
}
```
