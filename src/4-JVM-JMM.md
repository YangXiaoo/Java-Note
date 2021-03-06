> ## JVM虚拟机与JMM内存模型

## JVM, JMM
- JVM: 虚拟机内存模型
- JMM: Java内存模型

## JVM内存模型(介绍下 Java 内存区域/运行时数据区)? 程序计数器, 栈, 堆, 方法区?
- 程序计数器: 记录指令执行位置
- 栈: 存放已知基本类型数据以及对象指针
- 堆: 数组与对象, 垃圾回收主要区域
- 方法区: 类的信息, 常量, 静态变量

## JDK1.7之前常量池在哪里? 之后在哪里?
之前字符串常量池在方法区, 之后在堆中

## JDK1.8永久代变为了什么?
变为了元空间, 不属于JVM内存, 防止内存溢出

## Java内存模式对final的实现? 什么是引用逃逸?
对final修饰的变量使用内存屏障.
this逃逸指初始化对象过程中有一部分成员没有初始化就将这个对象通过this引用出去, 导致其它线程得到的对象不完整

## 什么是元空间?
JDK1.8开始HotSpot的实现中永久区改为元空间, 不属于JVM内存了, 而使用本地内存, 解决内存溢出问题

## Java基本类型、引用类型在内存中的存储原理?
基本类型存储在栈中, 引用类型的引用存储在栈中, 实例对象在堆中

## JVM如何判断两个类是否相同?
类全限定名和类加载器

## 分析 `Object obj = new Object();` 对象创建过程?
通过new创建一个对象, 对象存放在堆中, obj变量放在栈中指向堆中的对象.

## `String.intern()`?
若字符串常量池中国已有该字符串则直接返回.JDK1.6及之前的字符串常量池在方法区, 之后的在堆中.

## `String s = new String("abc");`创建了几个对象?
先创建"abc"字符串, 然后new 一个对象,s 指向该对象

## 栈中对象引用有几种方法? 详细介绍一下区别? 
- 句柄: 指向对象数据以及对象类型
- 指针: 直接指向实例对象, 实例对象对象头中有对象类型

## JVM年轻代与老年代? 年轻代垃圾回收过程? 
JVM主要分为年轻代和老年代, 年轻代又分为survivor(from和to)和Edien区域.年轻代与老年代默认比例1:2
Edien区域满后会触发major gc, 过程大致为: 先对Eiden区域进行垃圾回收, Eiden区域存活的对象复制到survivor区域的to区域中, from区域的对象根据其年龄进行转移, 若其年龄大于称为老年代设置的阈值时转移到老年代, 否则复制到to区域中; 复制完之后to区域和from区域交换角色.

## 永久代会发生垃圾回收吗?
永久代本身没有垃圾回收算法, 但会使用full gc来进行回收.

## JVM年轻代中Eden与survivor的比例?
8:1

## 垃圾回收算法有哪些?
- 引用计数法
- GC ROOT

## 垃圾回收策略?
- 复制算法: JVM年轻代回收算法
- 标记-整理: 回收过期对象, 然后对回收区域进行整理压缩, 不会产生垃圾碎片
- 标记-清理: 直接回收过期对象, 不会对回收区域进行压缩, 会产生垃圾碎片
- 分代

## 垃圾收集器有哪些? CMS特点?
- Serial: 年轻代垃圾回收, 单线程, 需要stop-the-world
- ParNew: Serial的多线程版本, 年轻代
- Parallel Scavenge: 吞吐量优先, 年轻代使用复制算法, 老年代使用标记整理算法
- Serial Old: 老年代收集器, 单线程
- Parallel Old: Serial Old的多线程版本
- CMS:  最短回收停顿时间为目标的收集器.初始标记(STW)-并发标记-重新标记(STW)-并发回收.在并发标记时会产生浮动垃圾, 并且需要在内存用尽完之前回收, 

## CMS中什么是浮动垃圾?
在并发标记过程中产生的垃圾.

## 比较一下G1与CMS?
- CMS:  使用标记清理算法, 会产生浮动垃圾
- G1:  使用标记整理, 不会产生垃圾碎片; 可预测的停顿; 保留分代

## 整个JVM大小?
- JDK1.8之前 JVM = 年轻代+老年代+永久代
- JDK1.8之后 JVM = 年轻代+老年代

## 年老代堆空间被占满如何解决? 持久代被占满如何解决? 堆栈溢出如何解决?
- 年老代堆空间被占满: survivor设置大一些; 老年代设置大一些
- 持久代被占满:  持久代设置大一些
- 堆栈溢出: 递归或循环调用

## 如何解决异常Fatal: Stack size too small?
Xss设置线程栈大小

## 如何解决异常java.lang.OutOfMemoryError: unable to create new native thread?
可能是单个线程栈太大, 设置小一点, 以便产生更多的线程.

## JMM内存模型中的规定了哪八种操作? 什么是重排序?
八种操作: 
- lock
- unlock
- read
- load
- use
- assign
- write
- store

重排序: 系统或处理器会根据性能在不影响逻辑情况下对指令进行重排以提高性能

## 内存模型三大特性? 原子性、可见性、有序性如何实现?
- 原子性: synchronized实现
- 可见性:  volatile即可实现, synchronized也可以实现
- 有序性: volatile, synchronized

## 堆上的内存如何释放, 栈上的内容如何释放?
- 堆上的内存需要major gc或 full gc释放
- 栈上的内容在方法调用结束后会释放

## Java内存泄露的最直接表现? 
抛出异常

## 什么是内存溢出, 什么是内存泄露? Java会不会发生内存泄露?
- 内存溢出: 无法申请到内存空间
- 内存泄露: 垃圾没有被回收, 一直占用内存空间, 无法得到释放

## 什么情况下会发生内存溢出?
堆设置太小, 递归深度过大

## 老年代溢出原因? 永久代溢出原因?
- 老年代溢出: 创建大数组或大字符串
- 永久代溢出: 通过反射创建太多对象, 永久代存放过多类信息

## 如果对象的引用被置为null, 垃圾回收器是否会立即释放该对象占用的内存?
不会立即释放, 会在下一个垃圾回收时进行垃圾回收

## Java中对象什么时候可以被垃圾回收?
没有变量指向该对象时, 会在下一个垃圾回收期被回收

## 你能保证GC吗?
无法保证GC. 即使使用System.gc(), 这只会建议JVM进行垃圾回收, 不会立即执行.

## finalize什么时候使用? 为什么避免使用? 
- 重写finalize给对象一次挽救机会, 将对象与GC Roots上的对象进行关联, 使得不会被垃圾回收器收集.
- 避免使用的原因: 无法预测执行时间, 可能整个程序声明周期也不会执行

## JVM如何确定一个对象是不是有引用?
通过GC Roots方法

## GC Roots包含哪些?
- 栈中的引用对象
- 本地方法中的引用对象
- 方法区指向对象的常量

## GC Roots 对不可用对象的判断过程?
一般经过两次GC Roots.

第一次GC Roots遍历对象链, 如果发现某个对象不可达: 如果该对象重写finalize方法且没有运行过, 那么就运行finalize方法让该对象自救一次, 如果自救成功则在下一次遍历过程中从即将被回收的对象集合中移除; 如果失败, 则下一次遍历过程中被回收.

## 什么时候新生代会发生GC? 老年代发生GC条件? Full GC 触发条件? +
- 当Edien区域满了会发生Major gc
- 老年代满了会发生full gc

full gc出触发条件: 
- 老年代满
- 空间分配担保失败
- CMS抛出异常需要full gc
- 调用System.gc()命令

## 永久代回收条件?
三个条件: 
- 该类的所有对象被回收
- 类加载器被回收
- 不能通过反射获得该类

## GC为什么要分代?
根据对象生命周期, 根据对象生命周期使用不同的垃圾回收策略.年轻代因为对象朝生夕死, 频繁发生垃圾回收, 并且大部分对象都会被回收掉, 所以一次major gc后Edien区域存活对象少, 这时使用复制算法就很快, 而老年代因为对象大部分存活比较长, 一次垃圾回收后存活对象多不适合复制算法而适合标记整理算法.

另外在判断对象是否可以回收过程中需要遍历所有对象, 若每次gc都会对整个堆中的对象都进行遍历, 那么将会非常耗时.

## JVM中大对象被分配到哪里? 长期存活对象进入哪里? 什么是空间分配担保?
1. JVM中大对象被分配到老年代
2. 长期存活的对象被分配到老年代
3. 空间分配担保: 在进行Major GC过程中, JVM需要判断老年代连续空间大小是否大于Major GC历代进入老年代对象大小的平均值, 若大于则进行一次有风险的Major GC; 若小于则进行一次full gc.

## 进入老年代的几种情况?
- 长期存活的对象
- 大对象
- 动态年龄判断

## JRE判断程序是否结束的标准?
前台线程执行完

## 什么是安全点?
指令复用即可以长时间执行的阶段, 一般发生在方法调用, 循环跳转, 异常抛出等情况

## 什么是happens-before(先发行为原则)?
规定某些指令必须按照顺序执行

## 对象创建的过程?
- 类加载
- 申请空间, 指针碰撞法和空闲列表法
- 初始化地址空间
- 初始化类

## 对象内存布局?
- 对象头
- 对象实例数据
- 填充

## 被动引用有哪些情况?
- 子类调用父类非final修饰的static成员, 父类会初始化
- 子类调用父类final static修饰的成员, 父类不会初始化
- 创建数组, 不会初始化

## 静态解析和动态解析?
## 静态分配和动态分配?
- 静态分配: 重载
- 动态分配: 重写

## 频繁FullGC如何排查?
先明确fullgc触发条件, 根据那些条件来排查
触发条件: 
- CMS垃圾回收失败
- 空间分配失败
- 老年代满
- 永久代满
- 调用system.gc

排查: 
- jstat -gc [pid] 		# 查看gc情况
- jmap -heap pid		# 显示java堆详细信息
- jmap -histo [pid] 	# 查看堆中对象统计


## JVM中存在大量图片如何解决?
使用软引用, 内存不足时回收

## 双重校验锁为什么要加锁后再次判断是否为空? 为什么外面还要判断? 
为防止加锁时其它线程刚好创建一个实例

外面判断的原因是为了提高性能, 因为并不是每次都需要加锁来创建对象