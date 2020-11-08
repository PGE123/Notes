# JVM





 ## JVM体系结构



### JVM架构图

![JVM架构图](D:\google\JVM架构图.png)






### JVM体系结构图



参考博客：
https://blog.csdn.net/qq_41701956/article/details/81664921

![自定义JVM体系图](D:\google\自定义JVM体系图.png)





### 类加载器和双亲委派机制

​		

​	相关不错的文档：https://blog.csdn.net/javazejian/article/details/73413292

### Native

**什么是native?**

​	Native在Java中是一个关键字,简单的来说，Native主要作用于方法上，是能让Java调用非Java代码的接口。一个Native方法是指该方法的实现由非Java的语言实现，一般由c或者c++实现，并且定义的一个Native方法不用提供实现，主要是因为java无法对操作系统底层进行操作，但是可以通过 JNI（本地方法接口）调用本地方法库来实现。



**Native Method Stack **

本地方法栈，主要用来登记被Native修饰的方法，在Execution Engine 执行时加载本地方法库



**Native Method Interface**

本地方法接口（JNI）



**为什么要用Native方法**

* 与Java环境交互

  **有时Java应用需要与Java外面的环境交互，这是本地方法存在的主要原因**。你可以想想Java需要与一些底层系统，如操作系统或某些硬件交换信息时的情况。本地方法正是这样一种交流机制：它为我们提供了一个非常简洁的接口，而且我们无需去了解Java应用之外的繁琐的细节

* 与操作系统交互
  JVM支持着Java语言本身和运行时库，它是Java程序赖以生存的平台，它由一个解释器（解释字节码）和一些连接到本地代码的库组成。然而不管怎样，它毕竟不是一个完整的系统，它经常依赖于底层系统的支持。这些底层系统常常是强大的操作系统。**通过使用本地方法，我们得以用Java实现了jre的与底层系统的交互，甚至JVM的一些部分就是用c写的**。还有，如果我们要使用一些Java语言本身没有提供封装的操作系统的特性时，我们也需要使用本地方法

* Sun's Java
  **Sun的解释器是用C实现的，这使得它能像一些普通的C一样与外部交互**





### PC 寄存器

​		程序计数器：Program counter register

​		每一个线程都有一个程序计数器，是线程私有的，就是一个指针，指向方向区中的方法字节码（用来存储指向如一条指令的地址、将要执行的指令代码），在执行引擎读取下一条指令，是一个非常小的内存空间，几乎可以忽略不计



### 方法区

​	Method Area：

​		方法区是被所有线程共享，所有字段和方法字节码，以及一些特殊方法，如构造函数、接口代码也在此定义，

简单说，所有定义的方法的信息都保存在该区域，**此区域属于共享区间**；

​		<font color="red">静态变量、常量、类信息（构造方法、接口定义）、运行时的常量池存在方法区中，但是 实例变量都存在堆内存中，和方法区无关;</font>
​	 

### 深入理解栈

​	栈内存：

​			在函数中定义的**一些基本数据类型的变量和对象的引用**都是在函数的栈内存中分配的，在一段代码块定义一个变量时，java就在这个栈中为这个变量分配内存空间，当超过该变量的作用域时，java会自动释放该变量的内存空间，该内存空间可以立刻另作他用。



#### 对象实例化过程



​	![](D:\google\对象实例化过程 (1).png)

引用变量是普通变量，定义时在栈中分配内存，引用变量在程序运行到作用域外被释放，而数组和对象本身在堆中分配，即使程序运行到使用new产生数组和对象的语句所产生的代码块之外，数组和对象本身在堆中也不会被释放，**只有在引用变量没有指向对象和数组的时候，才变成垃圾**，不能再被使用，但是仍然占着内存，在随后的不确定时间被垃圾回收器释放掉，这个也是java比较占内存的主要原因，实际上，**栈中的变量指向堆中的变量，这就是java中的指针！！**





### 堆

​	**堆内存用于存放由new创建的对象和数组**。在堆中分配的内存，由java虚拟机自动垃圾回收器来管理。在堆中产生了一个数组或者对象后，还可以在栈中定义一个特殊的变量，这个变量的取值等于数组或者对象在堆内存中的首地址，在栈中的这个特殊的变量就变成了数组或者对象的引用变量，以后就可以在程序中使用栈内存中的引用变量来访问堆中的数组或者对象，引用变量相当于为数组或者对象起的一个别名，或者代号。



![image-20201107104120499](C:\Users\PGE\AppData\Roaming\Typora\typora-user-images\image-20201107104120499.png)

​					

* GC回收主要在伊甸园和养老区
* 假设内存满了，会出现OOM的错误（堆内存不够）java.lang.OutOfMemoryError: Java heap space
* 在JDK8之后，永久存储区改成了元空间

![image-20201107164228505](C:\Users\PGE\AppData\Roaming\Typora\typora-user-images\image-20201107164228505.png)

堆内存分配：
JVM初始分配的内存由-Xms指定，默认是物理内存的1/64
JVM最大分配的内存由-Xmx指定，默认是物理内存的1/4

-Xms8m -Xmx8m 

-XX:+PrintGCDetails //打印GC垃圾回收信息

-XX:+HeapDumpOnOutOfMemoryError  // oomDump



### GC



**复制算法**

![image-20201108090927570](C:\Users\PGE\AppData\Roaming\Typora\typora-user-images\image-20201108090927570.png)

1.在Eden中，GC会把幸存下来的对象移到幸存区，一旦Eden被GC回收后，就被清空；

2.在幸存区中，谁空谁是to，如果两边都有对象，就会把一边的对象复制到另一边，从而会有一个空的幸存区，就是to区，不管什么情况，总有一个幸存区是空的

3.在幸存区中，当一个对象经历了15次GC后，都还在，就会进入老年区

-XX:MaxTenuringThreshold=100//通过这个参数可以设定进入老年区的时间 

优点：没有内存碎片

缺点：浪费了内存空间，总有一半幸存区是空的，

使用最佳场景： 在新生区中，对象存活度较低的时候，当所有对象都幸存着（100%极端情况），复制带来的成本太高



**标记清楚算法**



![image-20201108093547650](C:\Users\PGE\AppData\Roaming\Typora\typora-user-images\image-20201108093547650.png)





优点：不需要额外的空间

缺点：两次扫描，严重浪费时间，会产生内存碎片


**标记压缩算法**：

![image-20201108093741204](C:\Users\PGE\AppData\Roaming\Typora\typora-user-images\image-20201108093741204.png)



**标记清除算法**：先清除几次，再进行算法（改进）



**总结**

* 内存使用效率：复制算法>标记清楚算法>标记压缩算法
* 内存整齐度：复制算法=标记压缩算法>标记清除算法
* 内存利用率：标记压缩算法=标记清楚算法>复制算法



没有最优的算法，只有最合适的算法----->**GC:分代收集算法**

新生区：存活率低，适合用复制算法

养老区：内存大小较大，垃圾回收没有那么频繁，适合用标记清楚和标记压缩算法





### JMM（Java Memory Model）

参考博客：https://blog.csdn.net/zjcjava/article/details/78406330

1.什么是JMM？
	JMM是java内存模型

2.作用


​		JMM定义了线程和主内存之间的抽象关系：线程之间的共享变量存储在主内存（main memory）中，每个线程都有一个私有的本地内存（local memory），本地内存中存储了该线程以读/写共享变量的副本。本地内存是JMM的一个抽象概念，并不真实存在。本地内存它涵盖了缓存，写缓冲区，寄存器以及其他的硬件和编译器优化之后的一个数据存放位置



![img](https://img-blog.csdn.net/20160507135725155)

3.如何来学习