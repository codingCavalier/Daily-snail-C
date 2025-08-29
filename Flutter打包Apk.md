### 检查环境
1. 基本检查: 终端窗口运行 `flutter doctor` 命令或者 `flutter doctor -v` 命令，确保和打包安卓相关的是否都 `√` 了
2. 常用命令:
- flutter clean // 清理构建，类似.\gradlew clean
- flutter pub get // 检查同步所需依赖，类似点击<img width="28" height="29" alt="image" src="https://github.com/user-attachments/assets/cf512efd-58cb-4ab8-9571-aa9663aca65b" />
- flutter build apk 或者 flutter build apk --release // 构建release包
- flutter build apk --debug // 构建debug包
- flutter run // 直接运行到已连接的设备上

### 可能遇到的错误

#### android-licenses 通不过
- flutter doctor --android-licenses // 处理认证问题
- 事实证明，android-licenses 通不过，好像并不影响编译
<img width="766" height="21" alt="image" src="https://github.com/user-attachments/assets/4f97a4f5-5a51-4048-bd93-c6b4c6ef25e8" />
<img width="575" height="29" alt="image" src="https://github.com/user-attachments/assets/9d448562-fcbf-4d7a-aec6-32cdef76eae8" />
<img width="256" height="29" alt="image" src="https://github.com/user-attachments/assets/ec36960d-0128-474a-a261-15ec9d0c7806" />

#### org.gradle.java.home, Java home supplied is invalid
- 说明配置的这个值 org.gradle.java.home 所指向的 jdk 路径不对。
- 删除此配置，或者给它配上正确的 jdk 路径

#### NDK at /xxx/27.0.12077973 did not have a source.properties file
- 看看是不是这个版本的 ndk 没下载好，把ndk下载好就行了
- 每个版本的flutter会有自己默认使用的 ndk 版本，你的不一定就是27.0.12077973，但是问题根源是 ndk 没下载好

#### Unknown Kotlin JVM target: 21
- 说明 Android Studio 版本太新了，默认使用了 jdk21，而所使用的 kotlin 版本还没支持到 jdk21，要么升级 kotlin，要么降低jdk版本
- flutter doctor -v // 查看jdk版本，大概率是21（如图，默认使用当前Android Studio自带的jdk）
- flutter config --list // 查看jdk-dir，可能没配置
- flutter config --jdk-dir="jdk17路径" // 统一配置flutter构建工具链使用的jdk版本，包括Gradle也会用这个版本
- Flutter 默认jdk寻找顺序: Android Studio 自带的 JDK（xxx\Android Studio\jbr）；系统环境变量 JAVA_HOME 指向的 JDK；系统环境变量 PATH 中的第一个 Java 可执行文件；
<img width="697" height="141" alt="image" src="https://github.com/user-attachments/assets/4c15d7aa-0626-4cfa-8b44-a7828a398530" />

#### Android Gradle plugin requires Java 17 to run. You are currently using Java 11.
- 说明 gradle 用的jdk还是11版本
- 在 /项目/android/ 下的 gradle.properties 文件中配置 org.gradle.java.home=jdk17路径，地址别带引号

#### 错误: 找不到符号 @NonNull io.flutter.plugin.common.PluginRegistry.Registrar registrar) {
- Flutter 3.27.4 还有这个API，但是从3.29.0开始，Flutter 官方移除了这个类，所以要下载一个 3.27.4 版本的Flutter SDK

#### 'compileDebugJavaWithJavac' task (current target is 1.8) and 'compileDebugKotlin' task (current target is 17) jvm target compatibility should be set to the same Java version.
- 原因是依赖的某些库依然是 jvm target 1.8，需要兼容一下，让子项目依然 jvm target 1.8
- 在 /项目/android/ 下的 build.gradle 中与 allprojects 平级增加如下配置
```kotlin
subprojects {
    tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile) {
        kotlinOptions {
            jvmTarget = "1.8"
        }
    }
}
```

#### Namespace not specified. Specify a namespace in the module's build file.
- 原因是子项目没有配namespace，兼容一下
- 在 /项目/android/ 下的 build.gradle 中与 allprojects 平级增加如下配置
```kotlin
subprojects {
    afterEvaluate { project -> // 这项配置必须在 evaluationDependsOn(':app') 前面
        if (project.hasProperty("android")) {
            def android = project.extensions.getByType(com.android.build.gradle.BaseExtension)
            if (android.namespace == null) {
                android.namespace = project.group.toString()
            }
        }
    }
}
```

#### AAPT: error: resource android:attr/lStar not found.
- 原因是编译版本不符合，用到的资源找不到，指定SDK版本
- 在 /项目/android/ 下的 build.gradle 中与 allprojects 平级增加如下配置
```kotlin
subprojects {
    afterEvaluate { project -> // 这项配置必须在 evaluationDependsOn(':app') 前面
        if (project.plugins.hasPlugin("com.android.application") ||
                project.plugins.hasPlugin("com.android.library")) {
            def android = project.extensions.getByType(com.android.build.gradle.BaseExtension)
            android.compileSdkVersion = 36
            android.buildToolsVersion = "36.0.0"
        }
    }
}
```

#### Because xxxx depends on flutter_vlc_player >=7.4.3 which requires SDK version ^3.7.0, version solving failed.
- flutter_vlc_player: ^7.4.3 需要 Dart 3.7.0，而Flutter 和 Dart 是绑定的，升级 Dart 就要升级 Flutter，如果不方便升级 Flutter，则只能降低成 flutter_vlc_player: ^7.4.2

#### 报错：Exception in thread "main" java.lang.UnsupportedClassVersionError: 
com/android/prefs/AndroidLocationsProvider has been compiled by a more recent version of the Java Runtime (class file version 55.0), this version of the Java Runtime only recognizes class file versions up to 52.0

- 原因是使用高版本jdk11(class 55)编译了java代码，然后运行在了低版本jdk8(class 52)的环境中
- 可能的原因是在 Android Studio 的 Terminal 控制台运行了 `.\gradlew clean` 之类的命令，这个控制台就是 `Android SDK Command-line Tools`，说明这个tools的版本用的是低版本
- IDE -> Settings -> 搜索SDK -> Android SDK -> SDK Tools -> 卸载已安装Android SDK Command-line Tools，然后安装所用JDK版本对应的版本的Tools，比如17

