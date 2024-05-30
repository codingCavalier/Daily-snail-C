![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/2527cf10-e707-42cc-b08d-006b36795ff7)

### 二、使用

1、查看音视频文件信息：<br>
例如：<br>
* 查看视频所有帧的信息（json格式）： <br>
输入：ffprobe -i {视频地址} -show_frames -print_format json <br>
* 查看视频所有帧的信息（json格式）并输出到文件： <br>
输入：ffprobe -i {视频地址} -show_frames -print_format json >{输出文件地址如C:\Users\xxx\Desktop\output.json} <br>

2、获取某个时间戳位置的图片：<br>
ffmpeg -i {视频地址} -ss {秒支持小数} -f image2 {输出文件地址} <br>
例如： <br>
* 获取0.1秒处的视频图片 <br>
输入：ffmpeg -i {视频地址} -ss 0.1 -f image2 {输出文件地址} <br>

3、截取视频片段：<br>
ffmpeg -i {视频地址} -ss 5 -t 10 {输出文件地址} <br>
表示从第5秒开始截取，一共截取10秒时长 <br>
ffmpeg -ss 5 -i {视频地址} -t 10 -c:v copy -c:a copy {输出文件地址} <br>
会比上面更快，因为是直接跳到第5秒的位置开始解码，-c:v copy -c:a copy表示视频、音频编码格式不变 <br>

4、截取视频区域：<br>
ffmpeg -i {视频地址} -vf crop=338:190:596:0 {输出文件地址} <br>
crop=width:height:x:y，其中 width 和 height 表示裁剪后的尺寸，x:y 表示裁剪区域的左上角坐标 <br>

5、将视频导出成图片：<br>
ffmpeg -i {视频地址} -vf fps=24 {输出文件地址如C:\xx\xx\folder\output_%3d.jpg} <br>
fps=24：每秒导出24帧图片，%3d表示编号是三位数 <br>

6、缩放视频尺寸：<br>
将原视频缩放成1920x1080尺寸： <br>
ffmpeg -i {视频地址} -vf scale=1920:1080 {输出文件地址} <br>
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/50661d7e-d8ac-4261-9b76-273b0500145f)
