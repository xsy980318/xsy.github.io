# 并发

### volatile 的应用
> volatile 是轻量级的 synchronized ，在多处理器开发中保证了共享变量的“可见性”（当一个线程修改一个变量的值，另一个线程能够读到这个修改的值）。它比 synchronized 的使用和执行成本更低，因为它不会引起线程上下文的切换和调度。

- volatile 作用
		 保证被 votile 修饰的共享变量对所有线程可见
		 禁止指令重排序

- 如何禁止重排优化
	 通过插入内存屏障指令禁止在内存模型前后的指令执行重排序优化

- volatile 变量为何立即可见
		 当写一个变量时，JMM会把该线程对应的工作内存中的共享变量值刷新到主内存中
		 当读一个变量时，JMM会把线程对应的工作内存置为无效

- votile 和 Synchronized 区别
		 volatile 本质是在告诉JVM当前变量在工作内存中的值是不确定的，需要从主内存中取；Synchronized 则是锁定当前变量，只有当前线程可以访问该变量
		 volatile 仅能实现变量的修改可见性，不能保证原子性；而 synchronized 可以保证变量修改的可见性和原子性
		 volatile 标记的变量不会被编译器优化， synchronize 标记的变量可以被编译器优化

- 应用场景
	 一写多读的并发场景，最典型的应用是 CopyOnWriteArrayList，它在修改数据时会把整个集合的数据全部复制出来，对写操作加锁，修改完成后，再用 setArray() 把 array 指向新的集合。

### happens-before
> happens-before 是 JMM 最核心的概念

- 定义
	 如果一个操作 happens-before 另一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前
	 两个操作之间存在 happens-before 关系，并不意味着 Java 平台的具体实现必须要按照这个关系指定的顺序来执行，如果重排序之后的执行结果，与按 happens-before 关系来执行的结果一致，那么这种重排序并不非法

as-if-serial 语义保证单线程内程序的执行结果不被改变，即有序性

happens-before 关系保证正确同步的多线程的执行结果不被改变

### synchronized
- 底层实现
	 JVM 底层是通过监视锁来实现 synchronized 同步。监视锁即 monitor ，是每个对象与生俱来的一个隐藏字段，在对象头中。使用 synchronized 时， JVM 会根据 synchronized 的当前使用环境，找到对应对象的 monitor ，再根据 monitor 的状态进行加、解锁的操作
	 两个字节码指令： monitorenter/monitorexit 获取/释放锁
- 实现同步的基础
		 对于普通同步方法，锁是当前实例对象
		 对于静态同步方法，锁是当前类的 class 对象
		 对于同步方法块，锁是 synchronized 括号里配置的对象

- JVM 对 synchronized 进行的优化
	 - 重量级锁
	 	 获取不到锁就立马进入阻塞状态的锁，就称之为重量级锁
		 
	 - 自旋锁
	 	 如果此时拿不到锁，不马上进入阻塞状态，而是等待一段时间，类似于线程在做空循环，如果循环一定的次数还拿不到锁，才会进入阻塞状态
		 
	 - 自适应自旋锁
	 	 不需要认为指定循环几次，它自己本身会进行判断需要循环几次
		 
	 - 轻量级锁
	 	 轻量级锁认为，当你在方法里面执行的时候，其实是很少刚好有人也来执行这个方法，所以，当我们进入一个方法的时候根本就不用加锁，我们只需要做一个标记就可以了
		 采用CAS机制（保证操作的原子性），把这个方法的状态标记为已经有人在执行了，退出这个方法时，再把这个状态改为没有人在执行了
		 
	 - 偏向锁
	 	 比轻量级锁更加省事，偏向锁认为其实对于一个方法，是很少有两个线程来执行的，其实就是一个线程在执行这个方法而已，相当于单线程的情况，没必要加锁
		 如果这个方法没有人来过，那么一个线程首次进入这个方法时，会采用CAS机制，把这个方法标记为有人在执行了，并且也会把该线程的ID也记录进去，当这个线程退出这个方法时，它不会改变这个方法的状态，而是直接退出来
		 当其他线程进入这个方法时发现ID不是自己，偏向锁会升级成轻量级锁

- synchronized 和 Lock 的区别
		 synchronized 是JVM实现的； Lock 是JDK实现的
		 新版本的Java对 synchronized 进行了很多优化，例如自旋锁等
		 synchronized 锁是非公平的；reentrantlock 默认情况下也是非公平的，但也可以是公平的 
		 Reentrantlock 等待可中断；synchronized 等待不可中断

### CAS
> compareAndSwap

从内存中读取对应的值，进行操作，将读取出来的值和内存中的值进行判断，如果相等就写入，不等的话就跳回步骤一重新执行（compareAndSet），对应的是操作系统中的一条硬件操作指令，是具备原子性的，线程安全

为了预防ABA问题，引入版本控制（AtomicStampedReference）

### 线程安全
- 数据单线程内可见
- 只读对象
	 例如 String 、Integer 等
- 线程安全类
- 同步与锁机制

### 线程池
- 优点
		 降低资源消耗
		 提高响应速度
		 提高线程的可管理性

- 相关类图

- ThreadPoolExecutor 的核心参数
		 corePoolSize：表示常驻核心线程。如果等于 0 ，则任务执行完之后，没有任何请求进入时销毁线程池的线程；如果大于 0 ，即时本地任务执行完毕后，核心线程也不会销毁
		 maximumPoolSize：表示线程池能够容纳的同时执行的最大线程数
		 keepAliveTime：表示线程池中的线程空闲时间，当空闲时间达到 keepAliveTime 值时，线程会被销毁，直到剩下 corePoolSize 个线程为止
		 TimeUnit：表示时间单位
		 workQueue：表示缓存队列
		 threadFactory：表示线程工厂。在虚拟机栈分析时，就可以知道线程任务是由哪个线程工厂产生的
		 handler：表示执行拒绝策略的对象。比如保存到数据库进行削峰填谷，在空闲时再提取出来执行，也可以转向某个页面，或者打印日志

- 创建线程池的方法
		 Executors.newFixedThreadPool
		 Executors.newCachedThreadPool
		 Executors.newScheduledThreadPool ：支持定时及周期性任务执行，不回收工作线程
		 Executors.newSingleThreadPool：创建一个单线程的线程池，相当于单线程串行执行所有任务，保证按任务的提交顺序依次执行
		 Executors.newWorkStealingPool

- 线程池执行原理
		 如果当前运行的线程少于 corePoolSize，则创建新线程来执行任务，需要获取全局锁
		 如果运行的线程等于或多于 corePoolSize ，则将任务加入工作队列
		 如果无法将任务加入工作队列，则创建新的线程来处理任务，需要获取全局锁
		 如果创建新线程导致当前线程数超出 maximumPoolSize ，任务将被拒绝，会执行拒绝策略



### ThreadLoacal
> ThreadLocal 是每一个线程单独持有的。因为每一个线程都有独立的变量副本，其他线程不能访问，所以不存在线程安全问题。

ThreadLocal 有个静态内部类 ThreadLocalMap ，它还有一个静态内部类 Entry ，Entry 继承自 WeakReference ，没有方法，只有一个 value 成员变量，它的 key 是threadlocal对象

	 1 个 Thread 有且仅有 1 个 ThreadLocalMap 对象
	 1 个 Entry 对象的 Key 弱引用指向 1 个 ThreadLocal 对象
	 1 个 ThreadLocalMap 对象（即 threadLocals）存储多个 Entry 对象，即 Entry 数组
	 1 个ThreadLocal 对象可以被多个线程所共享
	 ThreadLocal 对象不持有 Value， Value 由线程的 Entry 对象持有

ThreadLocal 通常用于同一个线程内，跨类、跨方法传递数据。如果没有 ThreadLocal ，那么相互之间的信息传递，势必要返回值和参数，这样无形之中，有些类甚至有些框架会互相耦合

**ThreadLocal 副作用**
- 脏数据
	 线程复用会产生脏数据。由于线程池会重用 Thread 对象，那么与 Thread 绑定的类的静态属性 ThreadLocal 变量也会被重用

- 内存泄漏
	 在源码注释中提示使用 static 关键字来修饰 ThreadLocal ，在此场景下，寄希望于 ThreadLocal 对象失去引用后，触发弱引用机制来回收 Entry 的 Value 就不现实了

	 **解决方法**：
	 在每次用完 ThreadLocal 时，必须要及时调用 remove() 方法清理

### 并发工具类
- CountDownLatch
	 允许一个或多个线程等待其他线程完成操作
	 构造器接收一个 int 类型的参数作为计数器，当调用 countDown() 方法时，N 会减一， await() 方法会阻塞当前线程，直到 N 变为0

- CyclicBarrier
	 让一组线程到达一个同步点时被阻塞，直到最后一个线程到达时，屏障才会开门，所有被拦截的线程才会继续执行
	 构造器接收一个 int 参数，表示屏障拦截的线程数量，每个线程调用 await() 方法告诉 CyclicBarrier 已经到达了屏障，然后被阻塞

- CountDownLatch 和 CyclicBarrier 的区别
	 CountDownLatch 的计数器只能使用一次，CyclicBarrier 的计数器可以使用 reset() 方法重置

- Semaphore
	 用来控制同时访问特定资源的线程数量，它通过协调各个线程，以保证合理的使用公共资源
	 可以用于做流量控制，特别是公有资源有限的应用场景，比如数据库连接
	 构造方法接收一个 int 参数，表示可用的许可证数量，可以使用 acquire() 方法获取一个许可证，使用完之后调用 release() 方法归还许可证

- Exchanger
	 是一个用于线程间协作的工具类，用于进行线程间的数据交换。它提供一个同步点，在这个同步点，两个线程可以交换彼此的数据。这两个线程通过 exchange(data) 方法来交换数据，如果第一个线程先执行这个方法，它会一直等待第二个线程也执行 exchange(data) 方法，当两个线程都到达同步点时，这两个线程就可以交换数据，将本线程生产出来的数据传递给对方
	 可以用于遗传算法，也可用于校对工作


### ConcurrentHashMap
- 1.7 和 1.8 区别
	 - 1.7 采用 Segment + HashEntry 的方式进行实现，Segment 数组的意义就是将一个大的 table 分割成多个小的 table 来进行加锁，而每一个 Segment 元素存储的是 HashEntry 数组 + 链表， Segment 是一种可重入锁，继承了 ReentrantLock
	 - 1.8 采用 Synchronized + CAS + Node + Unsafe 实现，取消了 Segment 字段，数组中存储的就是Node 。它对 value 和 next 属性设置了 volatile 同步锁，它不允许调用 setValue 方法直接改变 Node 的value 域

- 1.7 put 操作
	 先定位到 segment ，判断是否需要扩容，concurrnetHashMap 不会对整个容器进行扩容，而只对某个 segment 进行扩容，定位添加元素的位置，然后将其放进 HashEntry 中

- 1.7 get 操作
	 在 get 过程中并不需要加锁，除非读到的值是空才会加锁重读，在 get 方法中将需要用到的共享变量全部定义成 volatile 类型

- 1.7 size 操作
	 先尝试两次通过不锁住 segment 的方式来统计各个 segment 的大小，如果统计的过程中，容器的count 发生变化，则再采用加锁的方式统计所有 segment 的大小。使用 modcount 变量判断是否发生了变化

- 1.8 put 操作
	 如果相应位置的Node还未初始化，则通过CAS插入相应的数据；如果相应位置的 Node 不为空，且当前该节点不处于移动状态，则对该节点加 synchronized 锁

- 1.8 size 操作
	 用一个 volatile 类型的变量 baseCount 记录元素的个数，当插入新数据或则删除数据时，会通过addCount() 方法更新 baseCount。部分元素的变化个数保存在CounterCell数组中，通过累加baseCount和CounterCell数组中的数量，即可得到元素的总个数

**JAVA8的ConcurrentHashMap为什么放弃了分段锁？**
	 加入多个分段锁浪费内存空间，很臃肿
	 生产环境中， map 在放入时竞争同一个锁的概率非常小，分段锁反而会造成更新等操作的长时间等待
	 为了提高 GC 的效率


**ConcurrentHashMap(JDK1.8)为什么要使用synchronized而不是如ReentranLock这样的可重入锁**
	 减少内存开销 假设使用可重入锁来获得同步支持，那么每个节点都需要通过继承AQS来获得同步支持。但并不是每个节点都需要获得同步支持的，只有链表的头节点（红黑树的根节点）需要同步，这无疑带来了巨大内存浪费
	 获得JVM的支持 可重入锁毕竟是API这个级别的，后续的性能优化空间很小。 synchronized则是JVM直接支持的

### 红黑树
	 和AVL数类似，都是在进行插入和删除元素时，通过特定的旋转来保持自身平衡，从而获得较高的查找性能
	 与AVL树相比，红黑树并不追求所有递归子树的高度差不超过1，而是保证从根节点到叶子节点的最长路径不超过最短路径的2倍，所以他的最坏运行时间也是O(longN)
	 在插入时，红黑树和AVL树都能在至多两次旋转内恢复平衡；在删除时，红黑树能在至多三次旋转内恢复平衡，AVL树至多需要logN次旋转
	 面对频繁插入和删除，红黑树更为合适；面对大量查询，AVL树更加合适

- 五个约束条件
		 节点只能是红色或黑色
		 根节点必须是黑色
		 所有NIL节点都是黑色的
		 一条路径下上不能出现相邻的两个红色节点
		 在任何递归子树内，根节点到叶子节点的所有路径上包含想同数目的黑色节点

























