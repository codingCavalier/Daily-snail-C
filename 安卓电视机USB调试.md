### 打不开USB调试
- 电视可能不是触摸屏，需要插鼠标才能点击
- 打开`开发人员选项`需要点击某些版本号的位置，可能是这三个位置：名字，版本号，整条的右侧空白区域
<img width="718" height="412" alt="a5c72b39d88875fdd2df543351a2fbbc" src="https://github.com/user-attachments/assets/9343f11b-b837-4477-8b72-2d6e1c53664c" />

- 点击次数可能不确定，3次、5次、7次等等，最好多试试
- 找生产厂商的说明书，看看有没有打开调试的方法
- 网上搜索打开调试的方式，例如：https://www.znds.com/tv-1010861-1-1.html
- 下载 安卓原生的设置App，因为电视上的原生设置App可能被卸载了
- 下载 AdbWireless这个apk，安装到设备上：https://github.com/LJason77/ADBWireless/tree/master，再试试能不能连上Adb
- 下载 AirDroid 远程控制设备，https://www.airdroid.cn/download/

设备上安装<img width="120" height="120" alt="image" src="https://github.com/user-attachments/assets/ddcec347-6923-4d45-b2d5-dc1a387241ff" />的<img width="98" height="211" alt="image" src="https://github.com/user-attachments/assets/4f85318c-d530-4115-b2b8-b9f6d77d527d" />
，电脑上安装<img width="141" height="217" alt="image" src="https://github.com/user-attachments/assets/278dbef3-f622-44e6-992a-9b4d3df404da" />

### 调试软件
- 在可能重要的环节，打印日志
- 输出日志到文件：`adb logcat -c` 清理日志，`adb logcat > {日志文件路径}` 开启输出日志，日志会源源不断写入这个文件，开始操作设备
- UncaughtExceptionHandler 改成吐司，因为不方便看日志的话，这样会比较快一点知道是报的什么错
- 连不上adb的话，在应用内执行`adb logcat > {日志文件路径}` 把日志输出到一个地方，然后打开查看
- 如果崩溃：关闭截屏试试
- 如果崩溃：关闭推流试试
- 停止获取设备型号等操作

### 网络连接不上
- 应用内打印ip地址，看看是否正确
- 关闭热点等不寻常的开关，再试试
