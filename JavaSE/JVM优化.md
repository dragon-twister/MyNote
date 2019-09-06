# JVM优化

[TOC]


**jvm优化思路**

JVM的调优主要是对内存管理方面的调优，优化的方向分为以下4点：
- HeapSize
堆的大小，也可以说Java虚拟机使用内存的策略，这点是非常关键的。
- 2.GarbageCollector  通过配置相关的参数进行Java中的垃圾收集器的4个算法(策略)进行使用。
- 3.StackSize             栈是JVM的内存指令区,每个线程都有他自己的Stack，Stack的大小限制着线程的数量。
- 4.DeBug/Log           在JVM中还可以设置对JVM运行时的日志和JVM挂掉后的日志输出，这点非常的关键，根据各类JVM的日志输出才能配置合适的参数

