# JVM
  
[TOC]

## 前言 


**JDK、JRE、JVM的关系**

- JDK  
JDK(Java Development Kit) 是 Java 语言的软件开发工具包（SDK）。JDK 物理存在，是 programming tools、JRE 和 JVM 的一个集合  

![](http://upload-images.jianshu.io/upload_images/634730-73b9b41d97382d8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

- JRE  
JRE（Java Runtime Environment）Java 运行时环境，JRE 物理存在，主要由Java API 和 JVM 组成，提供了用于执行 java 应用程序最低要求的环境。  

- JVM  
JVM(Java Virtual Machine) 是一种软件实现，执行像物理机程序的机器（即电脑）。  
本来，JVM 通过执行 Java bytecode 可以使 java 代码运行在各种硬件之上。  

**JVM 的整体架构**

先看一下 java 代码执行过程  

![](http://upload-images.jianshu.io/upload_images/634730-089a64921220b40d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

*   Class Loader
*   Excution Engine
*   Runtime Data Areas

## Class Loader

类加载器负责加载程序中的类型（类和接口），并赋予唯一的名字。



**JDK 默认提供了三种 ClassLoader**

![](http://upload-images.jianshu.io/upload_images/634730-ffd9553bc18ea213.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

**关系**

1.  启动类加载器(Bootstrp loader):  是在Java虚拟机启动后初始化的。
2.  扩展类加载器(Bootstrp loader): 负责加载 ExtClassLoader,并且将 ExtClassLoade r的父加载器设置为 Bootstrp loader。
3.  应用程序加载器(Bootstrp loader): 加载完 ExtClassLoader 后，就会加载 AppClassLoader,并且将 AppClassLoader 的父加载器指定为 ExtClassLoader。

<table>

<thead>

<tr>

<th>Class Loader</th>

<th style="text-align:center">实现</th>

<th>负责加载</th>

</tr>

</thead>

<tbody>

<tr>

<td>Bootstrp loader</td>

<td style="text-align:center">C++</td>

<td>%JAVA_HOME%/jre/lib,-Xbootclasspath参数指定的路径以及%JAVA_HOME%/jre/classes中的类</td>

</tr>

<tr>

<td>ExtClassLoader</td>

<td style="text-align:center">Java</td>

<td>%JAVA_HOME%/jre/lib/ext，此路径下的所有classes目录以及java.ext.dirs系统变量指定的路径中类库</td>

</tr>

<tr>

<td>AppClassLoader</td>

<td style="text-align:center">Java</td>

<td>classpath所指定的位置的类或者是jar文档，它也是Java程序默认的类加载器</td>
</tr>

</tbody>

</table>

**双亲委托模型**  

Java中ClassLoader的加载采用了双亲委托机制，采用双亲委托机制加载类的时候采用如下的几个步骤：

当一个类加载器收到类加载请求:

1.  当前ClassLoader首先从自己已经加载的类中查询是否此类已经加载，如果已经加载则直接返回原来已经加载的类。

2.  当前classLoader的缓存中没有找到被加载的类的时候，委托父类加载器去加载，父类加载器采用同样的策略，首先查看自己的缓存，然后委托父类的父类去加载，一直到bootstrp ClassLoader.

3.  当所有的父类加载器都没有加载的时候，再由当前的类加载器加载，并将其放入它自己的缓存中，以便下次有加载请求的时候直接返回。

**为什么使用双亲委托模型——ClassLoader 隔离问题**  

每个类装载器都有一个自己的命名空间用来保存已装载的类。当一个类装载器装载一个类时，它会通过保存在命名空间里的类全局限定名(Fully Qualified Class Name)进行搜索来检测这个类是否已经被加载了。  
大家觉得一个运行程序中有没有可能同时存在两个包名和类名完全一致的类？  
JVM 及 Dalvik 对类唯一的识别是 ClassLoader id + PackageName + ClassName，所以一个运行程序中是有可能存在两个包名和类名完全一致的类的。并且如果这两个”类”不是由一个 ClassLoader 加载，是无法将一个类的示例强转为另外一个类的，这就是 ClassLoader 隔离。  

**类装载器特点**  

Java提供了动态加载特性；他会在运行时的第一次引用到一个class的时候对它进行装载（Loading）、链接(Linking)和初始化(Initialization)，而不是在编译时进行。不同的JVM的实现不同，本文所描述的内容均只限于Hotspot Jvm。JVM的类装载器负责动态装载，Java的类装载器有如下几个特点：

*   **层级结构：**Java里的类装载器被组织成了有父子关系的层级结构。Bootstrap类装载器是所有装载器的父亲。
*   **代理模式：** 基于层级结构，类的代理可以在装载器之间进行代理。当装载器装载一个类时，首先会检查它在父装载器中是否进行了装载。如果上层装载器已经装载了这个类，这个类会被直接使用。反之，类装载器会请求装载这个类
*   **可见性限制：**一个子装载器可以查找父装载器中的类，但是一个父装载器不能查找子装载器里的类。
*   **不允许卸载：**类装载器可以装载一个类但是不可以卸载它，不过可以删除当前的类装载器，然后创建一个新的类装载器装载。

**过程**

![image](https://images2015.cnblogs.com/blog/592743/201603/592743-20160305205938080-405016617.png)

![](http://upload-images.jianshu.io/upload_images/634730-34263b8587735b2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  


- 加载：获取.class文件的二进制流将类信息、静态变量、字节码、常量这些.class文件中的内容放入方法区中。
在内存中生成一个代表这个.class文件的java.lang.Class对象，作为方法区这个类的各种数据的访问入口。一般这个Class是在堆里的，不过HotSpot虚拟机比较特殊，这个Class对象是放在方法区中的
        
- 验证：这一阶段的目的是为了确保.class文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。


- 准备：为类变量分配变量，并设置初始值


- 解析：解析阶段是虚拟机将常量池内的符号引用替换为直接引用的过程

- 初始化：给static变量赋值以及执行静态代码块。

JVM规范定义了上面的几个任务，不过它允许具体执行的时候能够有些灵活的变动。

## 执行引擎（Execution Engine）

通过类装载器装载的，被分配到JVM的运行时数据区的字节码会被执行引擎执行。执行引擎以指令为单位读取 Java 字节码。它就像一个 CPU 一样，一条一条地执行机器指令。每个字节码指令都由一个1字节的操作码和附加的操作数组成。执行引擎取得一个操作码，然后根据操作数来执行任务，完成后就继续执行下一条操作码。  
不过 Java 字节码是用一种人类可以读懂的语言编写的，而不是用机器可以直接执行的语言。因此，执行引擎必须把字节码转换成可以直接被 JVM 执行的语言。字节码可以通过以下两种方式转换成合适的语言。

**解释器：** 一条一条地读取，解释并且执行字节码指令。因为它一条一条地解释和执行指令，所以它可以很快地解释字节码，但是执行起来会比较慢。这是解释执行的语言的一个缺点。字节码这种“语言”基本来说是解释执行的。

**即时（Just-In-Time)编译器：** 即时编译器被引入用来弥补解释器的缺点。执行引擎首先按照解释执行的方式来执行，然后在合适的时候，即时编译器把整段字节码编译成本地代码。然后，执行引擎就没有必要再去解释执行方法了，它可以直接通过本地代码去执行它。执行本地代码比一条一条进行解释执行的速度快很多。编译后的代码可以执行的很快，因为本地代码是保存在缓存里的。

Java 字节码是解释执行的，但是没有直接在 JVM 宿主执行原生代码快。为了提高性能，Oracle Hotspot 虚拟机会找到执行最频繁的字节码片段并把它们编译成原生机器码。编译出的原生机器码被存储在非堆内存的代码缓存中。通过这种方法（JIT），Hotspot 虚拟机将权衡下面两种时间消耗：将字节码编译成本地代码需要的额外时间和解释执行字节码消耗更多的时间。

![](http://upload-images.jianshu.io/upload_images/634730-05d712735a355e9d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  



这里插入一下 Android 5.0 以后用的 ART 虚拟机使用的是 AOT 机制。

> Dalvik 是依靠一个 Just-In-Time (JIT)编译器去解释字节码。开发者编译后的应用代码需要通过一个解释器在用户的设备上运行，这一机制并不高效，但让应用能更容易在不同硬件和架构上运 行。ART 则完全改变了这套做法，在应用安装时就预编译字节码到机器语言，这一机制叫 Ahead-Of-Time (AOT）编译。在移除解释代码这一过程后，应用程序执行将更有效率，启动更快。

## 运行时数据区

> [JVM内存模型](http://note.youdao.com/noteshare?id=3323ed8adf397c06aaba984230bb4a56&sub=A1F14A606AEE433EBFEA3EA629409B44)

## 垃圾回收

> [JVM垃圾回收](http://note.youdao.com/noteshare?id=d5460bf7118625440a2814beaa17062c&sub=A7DC0B8260834AB481BDE2B6F1B7A674)

## JVM优化

>[JVM优化](http://note.youdao.com/noteshare?id=c06c2277c9557f06b151dcc171b68f76&sub=65FD7E150B7344CE8BDC863DBD2CED36)