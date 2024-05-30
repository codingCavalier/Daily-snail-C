
### 1. 简介

- Kotlin 是一门仅在标准库中提供最基本底层 API 以便各种其他库能够利用协程的语言。与许多其他具有类似功能的语言不同，async 与 await 在 Kotlin 中并不是关键字，甚至都不是标准库的一部分。此外，Kotlin 的 挂起函数 概念为异步操作提供了比 future 与 promise 更安全、更不易出错的抽象。<br>
- kotlinx.coroutines 是由 JetBrains 开发的功能丰富的协程库。它包含本指南中涵盖的很多启用高级协程的原语，包括 launch、 async 等等。<br>
- 本文是关于 kotlinx.coroutines 核心特性的指南，包含一系列示例，并分为不同的主题。<br>
- 为了使用协程以及按照本指南中的示例演练，需要添加对 kotlinx-coroutines-core 模块的依赖。<br>

### 2. 关于UI应用程序

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/f9c90acb-834c-4d0d-9bd3-29c2d14b0e6b)

