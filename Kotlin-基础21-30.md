
### 1. DSL 全称是 Domain Specific Language，即领域特定语言。顾名思义 DSL 是用来专门解决某一特定问题的语言，比如我们常见的 SQL 或者正则表达式等，DSL 没有通用编程语言（Java、Kotlin等）那么万能，但是在特定问题的解决上更高效。

类型安全的构建器可以创建基于 Kotlin 的适用于采用半声明方式构建复杂层次数据结构领域专用语言（domain-specific languages 简称 DSL）。Kotlin的DSL属于内部DSL，DSL是一种风格，提升特定场景下代码的可读性和安全性。

以下是构建器的一些示例应用场景：

1、使用 Kotlin 代码生成标记语言，例如 HTML 或 XML；<br>
2、以编程方式布局 UI 组件：Anko；<br>
3、为 Web 服务器配置路由：Ktor。<br>

原理：Kotlin 的高阶函数（以其他函数作为入参 的函数）允许指定一个 lambda 类型的参数，且当 lambda 位于参数列表的最后位置时可以脱离圆括号，满足 DSL 中的大括号语法要求。我们知道了实现大括号语法的核心就是将对象创建及初始化逻辑封装成带有尾 lambda 的高阶函数中

定义模型结构比如HTML对象，定义相对应的方法，同时接收一个lambda表达式，比如fun html(block: HTML.() -> Unit) : HTML，其中里面的block是一个带接收者的函数类型

**限制接收者作用域**，使用@DslMarker注解：给超类添加注解，编译器会认为子类已被标注，这样做可以限制的是不使用this明确指出接收者的情况，如果使用this还是可以跨层级调用，但是至少有一些限制了，会让开发者清楚自己当前所写的逻辑

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/56eff845-53ee-4175-afc7-0e0780259dba)
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/5152e8ae-6380-45c1-a985-f7356428d4a1)

### 如何实现优雅的DSL

- 使用带有尾 lambda 的高阶函数实现大括号的层级调用
- 为 lambda 添加 Receiver，通过 this 传递上下文
- 通过扩展函数优化代码风格，DSL 中避免出现命令式的语义
- 使用 infix 减少点号圆括号等符号的出现，提高可读性
- 使用 @DslMarker 限制 DSL 作用域，避免出错
- 使用 Context Receivers 传递多个上下文，DSL 更聪明（非正式语法，未来有变动的可能）
- 使用 inline 提升性能，同时使用 @PublishedApi 避免不必要的代码暴露（inline 要求调用的方法必须是 public 的）
