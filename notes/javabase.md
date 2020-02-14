# Java基础
 ### 面向对象

 > 将问题分解成一个一个步骤，对每个步骤进行抽象，形成对象，通过不同对象之间的调用，组合解决问题

- 面向对象四大基本特征：封装 继承 多态 抽象
- 五大基本原则（S.O.L.I.D）：单一职责 开放封闭 里氏替换 接口隔离 依赖倒置

### 平台无关性的实现
- Java语言规范：通过规定基本数据类型的取值范围和行为
- Class文件：所有Java文件都编译成统一的class文件
- Java虚拟机：通过虚拟机将class文件转成对应平台的二进制文件

### 值传递和引用传递
> Java中只有值传递

值传递是指在调用函数时将实际参数复制一份传递到函数中，函数无法改变原始对象
引用传递是指在调用函数时将实际参数直接传递到函数中，函数可以改变原始对象

### 重载和重写的区别
**重载**
> - 参数列表必须不同
- 可以改变返回值类型
- 可以改变访问修饰符
- 可以声明新的或更广的异常

**重写**
> - 参数列表必须完全相同
- 返回类型也必须完全相同
- 访问限制级别一定不能比重写方法的高
- 异常只能更小

### 八个基础数据类型
> byte short int long float double char boolean

> float 范围：1.4e-45 ～ 3.4e+38 && -3.4e+38 ～ -1.4e-45
double 范围：4.9e-324 ～ 1.798e+308  && -1.798e+308 ～ -4.9e-324

### Object类的方法
> getClass clone toString finalize equals hashcode wait notify notifyAll

### 抽象类和接口
- 区别：
		抽象类中可以没有抽象方法，也可以普通方法和抽象方法共存
		接口中的方法在 JDK8 之前只能是抽象的，在 JDK8 之后提供了 default 实现普通方法
		抽象类和类一样只能是单继承，接口可以实现多个父类
		抽象类中可以存在普通的成员变量，接口中只允许出现常量，必须是 staitc final 类型且必须被初始化
		抽象类中可以有构造方法，但不能直接调用，接口中不能有构造方法

### 关键字
- transient
> 
		只要这个类实现了 serializable 接口，这个类的所有方法和属性都会自动序列化
		如果某个属性前加了 transient 关键字，该属性就不会被序列化
		只能修饰变量，不能修饰方法和类
		一个静态变量不管是否被 transient 修饰，均不会被序列化

### 集合类

- ArrayLIst 和 LinkedList Vector 区别
> ArrayList 是一个大小可变的动态数组
LinkedList 是一个双向链表
Vector 和 ArrayList 相似，但是一个强同步类，效率不高

- HashMap 和 HashTable 的区别
> HashMap 是非线程安全的，HashTable 是线程安全
HashMap 继承 AbstractMap ， HashTable 继承自 Dictionary ，两个类都实现了 Map 接口
HashTable 的键和值都不允许为 null ，HashMap 中 null 可以为键，但只能有一个，null为值可以存在多个

- ConcurrentHashMap 和 HashMap 区别
> 前者是线程安全， 后者为非线程安全

- Set 如何保证元素不重复
> Set 分为 HashSet 和 TreeSet 两种
HashSet 基本操作都是 HashMap 实现的，也是用 HashMap 存储数据，利用 hashcode 和 equals 方法判断重复
TreeSet 是基于红黑树实现的，用 compareTo 方法来判断重复

### IO
- 同步/异步 阻塞/非阻塞
> 同步/异步是描述被调用方的，阻塞/非阻塞是描述调用方的

- 五种 IO 模型
		阻塞式 IO 模型
		非阻塞式 IO 模型
		IO 复用模型
		信号驱动 IO 模型
		异步 IO 模型

### 反射
> 指程序在运行时可以获取自身的信息

- Class 类
虚拟机为每种类型管理一个独一无二的 Class 对象。也就是说每个类型都有一个 Class 对象。运行程序时，Java虚拟机(JVM)首先检查是否所要加载的类对应的Class对象是否已经加载。如果没有加载，JVM就会根据类名查找.class文件，并将其Class对象载入。

### 代理
- 静态代理
在编译期间就确定好了

- 动态代理
可以在运行期间动态生成
反射是动态代理的一种实现方式
Java提供两种实现方式：JDK 动态代理和 cglib 动态代理

### 序列化
> 将对象转换为可传输格式的过程，是一种数据可持久化手段












