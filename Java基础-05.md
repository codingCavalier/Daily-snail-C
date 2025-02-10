#### 1、泛型
Java的泛型是采用擦拭法实现的；

擦拭法决定了泛型<T>：

* 不能是基本类型，例如：int；
* 不能获取带泛型类型的Class，例如：Pair<String>.class；
* 不能判断带泛型类型的类型，例如：x instanceof Pair<String>；
* 不能实例化T类型，例如：new T()。
* 泛型方法要防止重复定义方法，例如：public boolean equals(T obj)；

子类可以获取父类的泛型类型<T>。因为子类没有标记泛型，只有父类标记了，那么子类的class需要知道到底它本身可以存储什么类型的数据，也就是父类的泛型信息是存储到子类class里的，所以能拿到泛型类型。

泛型就是编写模板代码来适应任意类型；

泛型的好处是使用时不必对类型进行强制转换，它通过编译器对类型进行检查；

注意泛型的继承关系：可以把ArrayList<Integer>向上转型为List<Integer>（T不能变！），但不能把ArrayList<Integer>向上转型为ArrayList<Number>（T不能变成父类）。

#### 2、<? extends Number> 中的extends通配符
<? extends Number>上界通配符，表示可以传入Number或者它的子类。使用类似<? extends Number>通配符作为方法参数时表示：

方法内部可以调用获取Number引用的方法，例如：Number n = obj.getFirst(); <br>
方法内部无法调用传入Number引用的方法（null除外），例如：obj.setFirst(Number n); <br>
**即一句话总结：使用extends通配符表示可以读，不能写。**

#### 3、<? super Integer> 中的super通配符
因此，使用<? super Integer>通配符表示：

允许调用set(? super Integer)方法传入Integer的引用；<br>
不允许调用get()方法获得Integer的引用。<br>
唯一例外是可以获取Object的引用：Object o = p.getFirst()。<br>
**即一句话总结：使用super通配符表示可以写，不能读。**

#### 4、
