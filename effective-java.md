# Effective Java
***
## Chapter-2 创建和销毁对象

### Item 1
使用静态方法替代构造函数

优势：
  1. 方法有名字，可以清楚的描述方法的功能
  2. 与构造函数相比，静态方法每次调用时不需要创建一个新的对象
  3. 可以得到返回类型的任意一个子类型（返回接口类型？）
  4. 减少创建新对象时冗长的泛型

劣势：
  1. 没有public或者protected类型构造函数的类无法被继承
  2. 不同静态方法之间不容易区分

### Item 2
构造函数参数比较多时，使用builder方式

- 使用构造函数：1是构造方法会很多；2是当使用很多参数时，加入中间某个参数不需要，但还是必须给定一个默认值
- 使用JavaBean方式（setter）：调用多个setter的过程中，不能保证一致性；排除了JavaBean不可变的可能性
- Builder模式：调用构造函数（静态方法）使用必需的参数创建一个builder，然后设置可选的参数，最后调用build方法生成对象

参数多于一只手的情况下，适合用builder模式，特别是大部分参数可选，比构造函数方式更易读写，比JavaBean方式更安全

```java
Foobar fb = new Foobar.Builder(a, b).foo(c).bar(d).build();
```

### Item 3
确保单例属性使用private构造函数或者enum类型

枚举（enum）是实现单例的最好方式

### Item 4
使用私有构造方法保证不被调用，必要时可在私有构造方法里抛出异常

### Item 5
不要创建不必要的对象

```java
String s = new String("test"); // DO NOT DO THIS
```

经常使用的对象可以预先初始化

另一个经常创建不必要对象的地方是简单类型的autoboxing，最好使用简单类型，同时注意意外的封装

### Item 6
去掉不需要的对象引用

but，将不需要的对象引用置为null应该是特列，而不是规范

一般的说，如果一个类管理自己的内存，就应该小心内存泄露；另一个通常引起内存泄露的是缓存（cache）；第三个来源是listener和callback

### Item 7
避免Finalizer（析构器？）

finalizer是不可预料的，大多数情况下是危险的，一般也是不需要的

不要期待finalizer帮助关闭资源，用过的资源及时关闭

## Chapter 3 所有对象共有的方法

### Item 8
重写equals方法遵循特定约定

以下情况尽量避免重写equals方法

- 每个类的实例是唯一的
- 不需要关注是否逻辑上相等
- 父类已实现了equals方法且子类与父类行为一致
- 类是private或者package-private，可以确信equals不会被调用

重写equals方法的约定：

- Reflexive：对于非null的引用x，x.equals(x)必须返回true
- Symmetric：对于非null的引用x、y，x.equals(y)必须返回true当且仅当y.equals(x)返回true
- Transitive：对于非null的引用x、y、z，如果x.equals(y)返回true，y.equals(z)返回true，那么x.equals(z)必然返回true
- Consistent：对于非null的引用x、y，多次调用应该返回一致的结果
- 对任意非null的引用x，x.equals(null)总是返回false

如何写equals方法：

1. 使用`==`检查参数是否是当前对象的引用
2. 使用`instanceOf`检查参数是否是正确的类型
3. 转换参数到正确的类型
4. 对于类的每个重要字段，检查参数是否与当前对象相符
5. 当你完成`equals`方法，问自己三个问题：对称吗，可传递吗，一致吗？
  - 重写`equals`方法时总是重写`hashCode`方法
  - 不要耍小聪明
  - `equals`声明里不要将`Object`替换为其他类型

### Item 9
重写`equals`方法是总是重写`hashCode`方法

两个对象相等，hashCode必定相等

计算hashCode的方法：

1. 选择一个非零的int常数，如17，命名为`result`
2. 对于每一个重要的字段f（`equals`中使用的字段），做如下计算：
    - a.对f计算一个`int`的hash code
        * i.   f是`boolean`类型，c = f ? 1 : 0
        * ii.  f是`byte`，`char`，`short`，`int`类型，`c = (int) f`
        * iii. f是`long`类型，`c =  (int) (f ^ (f >>> 32))`
        * iv.  f是`float`类型，`c = Float.floatToIntBits(f)`
        * v.   f是`double`类型，先计算`Double.doubleToLongBits`，然后使用2.a.iii计算
        * vi.  f是对象引用，简单说，调用对象的`hashCode`方法，如果对象是null，返回0或是其他常数
        * vii. f是数组，对数组的每个元素分别对待，使用以上的方法计算，如果每个元素都重要，可以直接使用`Arrays.hashCode`方法
    - b.按如下方式合并c到`result`
        * `result = 31 * result + c;`
3. 返回`result`
4. 当完成hashCode方法后，使用单元测试测试一下

- 计算hashCode时必须包含`equals`使用的所有字段
- 不要为了提高性能而减少使用的字段
- 31这个数字是有原因的？

### Item 10
总是重写`toString`方法

基本原则：简洁有用的描述，方便用户阅读
- 提供一个好的toString方法可以使你的类更易使用
- toString方法应该返回对象包含的所有有用信息
- 不管是不是你指定格式，你应该在文档里清晰的写明意图
- toString返回的有价值的信息，可通过编程访问

### Item 11
谨慎的重写`clone`

`Cloneable`接口被设计为一个`mixin interface`，主要的缺陷是缺少`clone`方法，Object的`clone`方法是`protected`类型。对于一个仅仅implements Cloneable接口的对象，如果不用反射，是无法调用clone方法的。

- 如果在一个`nonfinal`类中实现`clone`方法，你应该通过`super.clone`获取对象
- 一个类实现Cloneable接口，期待提供一个合适用途的`public clone`方法

效果上，clone相当于构造函数。你必须保证对原始对象是无害的，不变的。

一个好点的方法是提供复制构造函数或者复制工厂
```java
public Yum(Yum yum);
public static Yum newInstance(Yum yum);
```

### Item 12
考虑实现`Comparable`接口

实现Comparable有以下约定：

在以下的描述中，符号sgn(expression)表示的数学符号函数，根据expression是负数、0、正数，sgn定义为返回-1，0，1。
- 对于所有的x和y，实现必须保证`sgn(x.compareTo(y)) == -sgn(y.compareTo(x))` (这意味着，`x.compreTo(y)`必须抛出异常当且仅当`y.compareTo(x)`抛出异常)
- 实现也必须保证关系是传递的，如果`x.compareTo(y) > 0`，`y.compareTo(x) > 0`，意味着`x.compreTo(z) > 0`
- 最后，实现必须保证，对于所有的z，`x.compareTo(y) == 0`意味着`sgn(x.compareTo(z)) == sgn(y.compareTo(z))`
- 强烈推荐，但不是必须的，`(x.compareTo(y) == 0) == (x.equals(y))`

## Chapter 4 类和接口

### Item 13
最小化类和成员的可访问性(封装)

经验法则十分简单：尽可能使类或成员不可访问

如果一个最顶层的类/接口的访问修饰符可以为`package-private`，do it
如果一个最顶层的`package-private`类/接口只有一个地方使用，考虑使用私有内部类

实例字段永远不该是public，类的公共可变对象字段不是线程安全的

非0长度的数组是可变的，所以在类中定义一个`public static final`的数组是不对的
常量字段应该是不可变的对象

### Item 14
在`public`类里，使用访问方法，而不是`public`字段

如果一个类在包外是可被访问的，提供访问方法
如果一个类是package-private或者private的内部类，暴露它的字段没什么太大问题

### Item 15
最小化可变性

以下5条规则可实现一个不可变类：

1. 不要提供任何方法来修改对象的状态
2. 保证类不能被扩展
3. 所有字段都是不可变的final
4. 所有字段都是private
5. 保证所有可变组件访问的唯一性
	- 不可变对象简单
	- 不可变对象本质上是线程安全的，不需要同步

### Item 16
优先使用组合而不是继承

