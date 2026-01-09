#### FileProvider
- 安卓7开始执行
  - 安卓7之前：val uri = Uri.fromFile(File("")) // file://文件绝对路径，不安全，暴露了目录结构
  - 安卓7+：val uri = FileProvider.getUriForFile(...) // content://授权authorities名/path别名/文件名，隐藏了真实路径
- 使用场景：跨应用分享资源
  - 即这个资源O是属于应用A的，但是应用B需要对资源O进行读取或者写入
  - 比如安装Apk，要允许**安装程序应用B**读取，就需要通过FileProvider，这里可能是**应用A**触发的安装程序应用，也可能是系统的**文件管理器应用**触发的安装程序应用（用户从文件管理里找到了Apk文件并点击安装），谁触发，谁就需要对分享的uri做授权
  - 再比如从QQ分享图片到微信，也是这个道理，微信要读取一个文件uri，就需要这个uri已经被授权了，否则微信无权读取
  - 但是在微信里选择分享图片给好友，打开微信自己的图片选择器，这里可能使用的是 MediaStore 查询媒体数据库，此时不涉及FileProvider，因为不涉及跨应用。（安卓9及以下需要读取存储卡权限，安卓10-12 MediaStore查询媒体不需要权限，安卓13开始细化权限分图片视频音频，如READ_MEDIA_IMAGES）
- 使用步骤：
  - 1. AndroidManifest.xml 中定义，属于四大组件之一，ContentProvider
  - 2. 创建 filepaths.xml 文件，在 res/xml/ 目录下
    - 举例：<files-path name="myfiles" path="images/" />
      - 表示定义一个允许授权外部访问的目录，对应 context.filesDir 的值，即 data/data/应用包名/files/ 目录
      - path="images/"：表示 只能访问这个目录下的 images/ 目录下的文件
      - name="myfiles"：表示 path 目录 images/ 对应的别名叫 myfiles，那么文件`data/data/应用包名/files/images/abc.txt`实际对方应用看到的路径是`content://com.example.mydemo.fileprovider/myfiles/abc.txt`
      - 如果 path="."：表示 允许访问这个目录下的所有文件，即 data/data/应用包名/files/ 下的所有文件

```xml
<application
    <provider
        android:name="androidx.core.content.FileProvider"
        android:authorities="${applicationId}.fileprovider"
        android:exported="false"
        android:grantUriPermissions="true">

        <meta-data
            android:name="android.support.FILE_PROVIDER_PATHS"
            android:resource="@xml/filepaths" />
    </provider>
</application>
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<paths>
    <!-- 以下写法都限定了root目录下的具体目录为images，如果想放开它的root目录，则这样写path="." -->

    <!-- 对应 context.filesDir 目录（应用私有存储data/data/应用包名/files/） -->
    <files-path
        name="myfiles"
        path="images/" />

    <!-- 对应 context.cacheDir 目录（应用私有存储data/data/应用包名/cache/） -->
    <cache-path
        name="mycaches"
        path="images/" />

    <!-- 对应 Environment.getExternalStorageDirectory() 目录（外部公共存储sdcard/） -->
    <external-path
        name="myexternal"
        path="images/" />

    <!-- 对应 context.getExternalFilesDir(null) 目录（应用外部私有存储sdcard/Android/data/应用包名/files/） -->
    <external-files-path
        name="myexternalfiles"
        path="images/" />

    <!-- 对应 context.externalCacheDir 目录（应用外部私有存储sdcard/Android/data/应用包名/cache/） -->
    <external-cache-path
        name="myexternalcaches"
        path="images/" />

    <!-- 已经不推荐使用，因为从安卓Q开始，任何应用都可以通过MediaStore无需权限的写入数据到外部存储的图片视频音频目录 -->
    <!-- 对应 context.externalMediaDirs.first() 目录（应用外部私有存储sdcard/Android/media/应用包名/） -->
    <external-media-path
        name="myexternalmedias"
        path="images/" />
</paths>
```
