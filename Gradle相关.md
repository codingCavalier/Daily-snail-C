1、查找so和assets来自哪个库

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
