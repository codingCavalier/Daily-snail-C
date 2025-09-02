### 1. 引入KSP
- 在项目级别的`build.gradle`中增加KSP插件
```groovy
plugins {
    // 其他省略...
    // KSP 插件（1.8.0是项目用的kotlin版本，1.0.9是对应KSP插件版本，它们一一对应，具体去这里查：https://github.com/google/ksp/releases?page=10）
    id 'com.google.devtools.ksp' version '1.8.0-1.0.9' apply false
    // kotlin-jvm 插件（注解处理模块要用，因为注解处理模块是java-library，要支持kotlin语言，就需要这个插件把kotlin代码编译成.class字节码文件）
    id 'org.jetbrains.kotlin.jvm' version '1.8.0' apply false
}
```
- 从KSP 1.0.9开始，**不再需要**手动配置告诉gradle生成的源码位置

### 2. 创建子模块（注解处理模块）
- 新建一个子模块，类型选library，<img width="269" height="40" alt="image" src="https://github.com/user-attachments/assets/6f87256d-641b-4d09-a6fa-ac03eb901ae5" />，命名推荐叫`annotation-processor`
- 该子模块的`build.gradle`大概长这样：
```groovy
plugins {
    id 'java-library'
    id 'org.jetbrains.kotlin.jvm'
}

java {
    sourceCompatibility = JavaVersion.VERSION_1_8
    targetCompatibility = JavaVersion.VERSION_1_8
}

dependencies {
    // ksp 相关依赖
    implementation("com.google.devtools.ksp:symbol-processing-api:1.8.0-1.0.9")
    // 生成.kt文件的辅助工具类
    implementation("com.squareup:kotlinpoet:1.14.2")
}
```
- 确保项目级别的`settings.gradle`中有`include ':annotation-processor'`配置项

- 在该子模块中创建注解
```kotlin
@Target(AnnotationTarget.CLASS)
@Retention(AnnotationRetention.SOURCE)
annotation class AutoRegister(val value: String)
```

- 在该子模块中创建注解处理器
```kotlin
class AutoRegisterAnnotationProcessor(
    private val codeGenerator: CodeGenerator,
    private val logger: KSPLogger
) : SymbolProcessor {
    // 忽略具体处理注解和产生.kt文件的代码...
    override fun process(resolver: Resolver): List<KSAnnotated> {
        logger.info("AutoRegisterAnnotationProcessor 开始运行！")
        return emptyList()
    }

    override fun finish() {
        super.finish()
        logger.info("AutoRegisterAnnotationProcessor 运行结束！")
    }

    override fun onError() {
        super.onError()
        logger.info("AutoRegisterAnnotationProcessor 出错了！")
    }
}
```

- 在该子模块中创建注解处理器提供者
```kotlin
class AutoRegisterSymbolProcessorProvider : SymbolProcessorProvider {

    override fun create(environment: SymbolProcessorEnvironment): SymbolProcessor {
        return AutoRegisterAnnotationProcessor(
            codeGenerator = environment.codeGenerator,
            logger = environment.logger
        )
    }
}
```

- 在该子模块中创建注解处理器注册文件：src/main/resources/META-INF/services/com.google.devtools.ksp.processing.SymbolProcessorProvider，并在文件中写入注解处理器提供者的全类名：xxx.xxx.xxx.AutoRegisterSymbolProcessorProvider
<img width="548" height="204" alt="image" src="https://github.com/user-attachments/assets/210141eb-1ba3-4784-a2de-c6a230fc593c" />
<img width="654" height="86" alt="image" src="https://github.com/user-attachments/assets/e5ee9d11-64c4-4d18-a193-3a82f09cf59d" />

### 3. 使用注解处理器
- 在要使用注解处理器的模块中，使用注解
```kotlin
@Keep
@AutoRegister("1234567890")
class ABC {
}
```

- 在要使用注解处理器的模块的`build.gradle`中，应用KSP插件：
```groovy
apply plugin: 'com.google.devtools.ksp'

dependencies {
    // 其他省略...
    // 依赖注解处理模块，因为要使用里面的注解类
    implementation project(':annotation-processor')
    // 告诉KSP插件，注解处理器的位置
    ksp(project(':annotation-processor'))
}
```

- 在控制台运行`.\gradlew :当前模块名:build --info`或者`.\gradlew :当前模块名:compileKotlin --info`或者`.\gradlew :当前模块名:compileDebugKotlin --info`
- 有日志输出，说明基础搭建顺利完成：
<img width="523" height="71" alt="image" src="https://github.com/user-attachments/assets/80e1f857-7a15-4880-a8e2-3fd308a6d882" />

### 4. 生成的文件
最终生成的文件在`使用注解处理器模块`的这个目录下
<img width="496" height="186" alt="image" src="https://github.com/user-attachments/assets/0fd39fc3-f881-493f-bb79-5422db4d72aa" />


