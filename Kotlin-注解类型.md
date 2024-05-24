
### AnnotationTarget 注解的作用域

```kotlin
public enum class AnnotationTarget {
    /** Class, interface or object, annotation class is also included */
    CLASS, // 可以放在类、接口、对象上，注解类也包括在内
    /** Annotation class only */
    ANNOTATION_CLASS, // 只能放在注解类上
    /** Generic type parameter */
    TYPE_PARAMETER, // 泛型参数
    /** Property */
    PROPERTY, // （计算）属性（该注解Java不可见）
    /** Field, including property's backing field */
    FIELD, // 字段变量，包括PROPERTY的备用字段（backing field）
    /** Local variable */
    LOCAL_VARIABLE, // 局部变量
    /** Value parameter of a function or a constructor */
    VALUE_PARAMETER, // 方法、构造函数的参数
    /** Constructor only (primary or secondary) */
    CONSTRUCTOR, // 构造函数
    /** Function (constructors are not included) */
    FUNCTION, // 方法（不含构造函数）
    /** Property getter only */
    PROPERTY_GETTER, // getter 方法
    /** Property setter only */
    PROPERTY_SETTER, // setter 方法
    /** Type usage */
    TYPE, // 泛型支持
    /** Any expression */
    EXPRESSION, // 表达式
    /** File */
    FILE, // 给文件加注解
    /** Type alias */
    @SinceKotlin("1.1")
    TYPEALIAS // 给别名加注解
}
```

#### TYPE
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/0631f6d7-6d54-4f16-af21-37f7fd9b1190)
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/5b1a2ea9-777e-4ce2-92bf-a6485ff57073)

#### TYPE_PARAMETER
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/46a1aecf-f716-4e53-8622-91134b72e689)
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/a76f7730-897e-40e9-a7a1-e93866c2ab74)

#### FILE
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/e2efeb49-55bf-4140-a293-d48003665c6b)
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/1f8e200e-718e-4917-bf71-969ebd19065c)

### AnnotationRetention 注解的保留期

```kotlin
/**
 * Contains the list of possible annotation's retentions.
 *
 * Determines how an annotation is stored in binary output.
 */
public enum class AnnotationRetention {
    /** Annotation isn't stored in binary output */
    SOURCE, // 注解只在源码阶段保留，在编译器进行编译时它将被丢弃忽视。用于编码期代码检查。
    /** Annotation is stored in binary output, but invisible for reflection */
    BINARY, // 注解被编译到二进制文件，但不会被加载到 JVM 中。反射不可见，用于编译期，比如插桩。
    /** Annotation is stored in binary output and visible for reflection (default retention) */
    RUNTIME // 注解被编译到二进制文件，它会被加载进入到 JVM 中，所以在程序运行时可以获取到它们。默认值。可用于运行时反射获取注解信息。
}
```
