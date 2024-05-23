
### 11. 扩展函数，如果成员函数和扩展函数相同，优先调用成员函数。扩展函数是静态解析的，这意味着调用的扩展函数是由函数调用所在的表达式的类型来决定的， 而_*不是由表达式运行时*求值结果决定的。

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/3ffbe055-288b-4073-9ca2-e03998cd9856)

### 12. 操作符覆写 operator override ，例如 compareTo invoke get plus 等操作符，利用中缀表达式 infix 实现优雅写法，链接：https://kotlinlang.org/docs/operator-overloading.html

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/5377a269-f947-4c60-aa8c-96436ba70118)

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/68b0288a-4167-4da6-b0ba-934e1da7d0c9)

### 13. Range 及 操作符 .. until in 等，链接：https://kotlinlang.org/docs/ranges.html

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/8fdbce4e-79d1-48a8-8a9a-a335521b4b54)

### 14. for 循环，任意类，实现了Iterable接口后，都可以用来 for 循环遍历

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/6aa031a1-b0de-4bca-b1b5-b742da011fe2)

### 15. 对集合的扩展函数，toList，toSet，sortedByDescending，filter，map，any，all，count，associate，groupBy，flatMap（将一堆集合铺平），max，min，sum 等

### 16. 对集合的扩展函数，partition，将一个集合根据给定条件拆分成两个集合

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/8310a55e-3282-485a-b0c4-944fcd340c1a)

### 17. 对集合的扩展函数，fold 和 reduce，runningFold，runningReduce，它俩的区别是 fold 有初始值，reduce没初始值而是将集合第一个元素作为初始值

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/2250029a-52fd-4b9b-bc31-65a7f45f1e62)

### 18. List 和 Sequence，拿 FilteringSequence 举例说明，每个 Sequence 都拿着上游的 Sequence ，当调用 hasNext 和 next 的时候，从上游依次过滤每个 Sequence 的迭代器，查找符合条件的值

每个 Sequence 内部维护一个状态机，用于表示“-1未计算值” “1有值” “0没元素，错误状态”，当调用hasNext的时候，从上游开始，依次计算每个步骤实际要产出的值，直到最后一步计算完毕，得出hasNext最终结果（有值true，没值false），然后记录下来，等到调用next的时候，把刚刚计算好的值返回出去

原始数据 10 15 20 25 30<br>
比10大的  x 15 20 25 30<br>
比15大的  x x  20 25 30<br>

第一次hasNext，就会计算得到20，因为10和15在计算过程中，达不到hasNext=true的结果

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/f55920b9-c26e-4456-8bda-29bfc799e555)
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/e9642ea0-3c68-4892-949d-777fa8f70a83)
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/83262683-1541-4643-a5ab-d96b34f5d750)

### 19. Properties 属性，和我们常说的字段 field 不是一个概念

1、var 可变，val 不可变<br>
2、完整的定义<br>
```kotlin
var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]
```
3、初始化、getter、setter都是可选的<br>
4、类型可以省略<br>
5、var 有默认的getter、setter，val 有默认的getter<br>
6、如果getter或者setter采用了默认实现，那么field将自动产生，或者在自定义实现里碰了field，那么才会有field<br>
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/ae1c51cb-a831-4449-b8d6-2b3c32f0a4ea)

7、lateinit 延迟初始化属性，判断是否初始化了，使用 ::propertyName.isInitialized
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/0acf2607-493f-4e71-a0dc-f08df8048f29)

### 20. 委托属性

1、语法是： val/var <属性名>: <类型> by <表达式>。在 by 后面的表达式是该 委托， 因为属性对应的 get()（与 set()）会被委托给它的 getValue() 与 setValue() 方法。 属性的委托不必实现任何的接口，但是需要提供一个 getValue() 函数（与 setValue()——对于 var 属性）

2、**标准委托：延迟属性 Lazy**
默认情况下，对于 lazy 属性的求值是**同步锁**的（synchronized）：该值只在一个线程中计算，并且所有线程会看到相同的值。<br>
如果初始化委托的同步锁不是必需的，这样多个线程可以同时执行，那么将 LazyThreadSafetyMode.PUBLICATION 作为参数传递给 lazy() 函数。**初始化允许多线程，谁先初始化完，就用谁的值**<br>
而如果你确定初始化将总是发生在与属性使用位于相同的线程， 那么可以使用 LazyThreadSafetyMode.NONE 模式：它不会有任何线程安全的保证以及相关的开销。

3、**标准委托：可观察属性 Observable**，简而言之，就是在赋值的前面或者后面，做一些额外处理，比如赋值前做检查，发现不符合条件就不赋值等等。或者赋值后做检查，发现符合条件就做一项操作，比如刷新ui等等。
Delegates.observable() 接受两个参数：初始值与修改时处理程序（handler）。 每当我们给属性赋值时会调用该处理程序（在赋值后执行）。它有三个参数：被赋值的属性、旧值与新值<br>
如果你想截获赋值并“否决”它们，那么使用 vetoable() 取代 observable()。 在属性被赋新值生效之前会调用传递给 vetoable 的处理程序。

4、委托给另一个属性：为将一个属性委托给另一个属性，应在委托名称中使用恰当的 :: 限定符，例如，this::delegate 或 MyClass::delegate。

5、**将属性储存在映射中**：原理是采用了反射的方式拿到对象的属性name，在map中查找符合属性name的key
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/990e7613-2237-4db8-be20-3e09f6758761)

6、局部委托属性：简而言之，把委托属性写方法内，比如 val a by lazy(block)，如果运行时逻辑调用不到a，那么block将免于调用和运算

属性委托的原理：就是调用了getValue和setValue方法
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/2ed1e62e-4f1a-4cbf-9930-d1304f3dbda2)

7、提供委托 操作符 provideDelegate ，实现了这个操作符的情况下，编译器会用它产生委托对象，产生过程中，我们可以拿到被委托的属性的name，做一些判断逻辑等。
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/2fa3d8e3-39a3-4ee5-82c4-6a9fd6953b12)

8、三个重要接口：
```kotlin
public fun interface ReadOnlyProperty<in T, out V> {
    /**
     * Returns the value of the property for the given object.
     * @param thisRef the object for which the value is requested.
     * @param property the metadata for the property.
     * @return the property value.
     */
    public operator fun getValue(thisRef: T, property: KProperty<*>): V
}
public interface ReadWriteProperty<in T, V> : ReadOnlyProperty<T, V> {
    /**
     * Returns the value of the property for the given object.
     * @param thisRef the object for which the value is requested.
     * @param property the metadata for the property.
     * @return the property value.
     */
    public override operator fun getValue(thisRef: T, property: KProperty<*>): V

    /**
     * Sets the value of the property for the given object.
     * @param thisRef the object for which the value is requested.
     * @param property the metadata for the property.
     * @param value the value to set.
     */
    public operator fun setValue(thisRef: T, property: KProperty<*>, value: V)
}
public fun interface PropertyDelegateProvider<in T, out D> {
    /**
     * Returns the delegate of the property for the given object.
     *
     * This function can be used to extend the logic of creating the object (e.g. perform validation checks)
     * to which the property implementation is delegated.
     *
     * @param thisRef the object for which property delegate is requested.
     * @param property the metadata for the property.
     * @return the property delegate.
     */
    public operator fun provideDelegate(thisRef: T, property: KProperty<*>): D
}
```

9、利用 PropertyDelegateProvider 可以在不用创建类的情况下，使用属性委托：
```kotlin
val provider = PropertyDelegateProvider { thisRef: Any?, property ->
    ReadOnlyProperty<Any?, Int> {_, property -> 42 }
}

val delegate: Int by provider
```
