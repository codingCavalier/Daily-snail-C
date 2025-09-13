### 1. 查找so和assets来自哪个库

放在 app 的 build.gradle 文件的 android 代码块外面

```Kotlin
tasks.whenTaskAdded { task ->

    // 如果不知道task的name，可以把所有name都打印出来，然后就知道了
    // println("------------------- ${task.name} -------------------")

    if (task.name=='mergeAppDebugNativeLibs' || task.name=='mergeAppDebugAssets') {
        task.doFirst {
            println("------------------- find so files start -------------------")
            println("------------------- find so files start -------------------")
            println("------------------- find so files start -------------------")

            it.inputs.files.each { file ->
                printDir(new File(file.absolutePath))
            }

            println("------------------- find so files end -------------------")
            println("------------------- find so files end -------------------")
            println("------------------- find so files end -------------------")
        }
    }
}

def printDir(File file) {
    if (file != null) {
        if (file.isDirectory()) {
            file.listFiles().each {
                printDir(it)
            }
        } else if (file.absolutePath.endsWith(".so")) {
            println "find so file: $file.absolutePath"
        } else if (file.absolutePath.endsWith("ttf")) {
            println "find Fonts: $file.absolutePath"
        }
    }
}
```

### 2. 查看项目整体情况
./gradlew projects --info

### 3. 查看某个库的依赖来自哪里
./gradlew :app:dependencyInsight --dependency coroutines-android --configuration debugRuntimeClasspath
- app是模块名，可以自由更换
- coroutines-android是要查找的依赖的名字的一部分，模糊查询，写的越精准，查的结果也越精准
- configuration参数推荐用 debugRuntimeClasspath 或者 releaseRuntimeClasspath
