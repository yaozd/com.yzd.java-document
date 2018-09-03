## 1.内存溢出的产生原因及解决方法
- [内存溢出的产生原因及解决方法](http://www.cnblogs.com/canacezhang/p/9526570.html)
- [Java内存各部分OOM出现原因及解决方法(必看)](https://www.jb51.net/article/110743.htm)
- [Java中关于内存泄漏出现的原因汇总及如何避免内存泄漏（超详细版）](https://www.jb51.net/article/92311.htm)
- [full gc频繁的分析及解决案例](https://www.2cto.com/kf/201604/499063.html)

```
一、产生内存溢出的

1、java.lang.OutofMemoryError:Java heap space
2、java.lang.OutofMemoryError:PermGen space
3、java.lang.OutofMemoryError:unable to create new native thread
4、java.lang.OutofMemoryError:GC overhead limit exceeded
1、Java堆空间不够，当应用程序申请更多的内存，而Java堆内存已经无法满足应用程序对内存的需要，将抛出这种异常。

2、Java永久代空间不够，永久代中包含类的字节码和长常量池，类的字节码加载后的信息，这和存放对象实例的堆区是不同的，大多数JVM的实现都不会对永久带进行垃圾回收，因此，只要类加载的过多就会出现这个问题。一般的应用程序都不会产生这个错误，然而，对于Web服务器来讲，会产生有大量的JSP，JSP在运行时被动态的编译成Java Servlet类，然后加载到方法区，因此，太多的JSP的Web工程可能产生这个异常。

3、本质原因是创建了太多的线程，而能创建的线程数是有限制的，导致了这种异常的发生。

4、是在并行或者并发回收器在GC回收时间过长、超过98%的时间用来做GC并且回收了不到2%的堆内存，然后抛出这种异常进行提前预警，用来避免内存过小造成应用不能正常工作。

下面两个异常与OOM有关系，但是，又没有绝对关系。

java.lang.StackOverflowError …

java.net.SocketException: Too many open files

1、是JVM的线程由于递归或者方法调用层次太多，占满了线程堆栈而导致的，线程堆栈默认大小为1M。

2、是由于系统对文件句柄的使用是有限制的，而某个应用程序使用的文件句柄超过了这个限制，就会导致这个问题。

 

二、产生原因及解决办法


【情况一】： 
　　java.lang.OutOfMemoryError: Java heap space：这种是java堆内存不够，一个原因是真不够，另一个原因是程序中有死循环； 
　　如果是java堆内存不够的话，可以通过调整JVM下面的配置来解决： 
　　< jvm-arg>-Xms3062m < / jvm-arg> 
　　< jvm-arg>-Xmx3062m < / jvm-arg> 
　　 
【情况二】 
　　java.lang.OutOfMemoryError: GC overhead limit exceeded 
　　【解释】：JDK6新增错误类型，当GC为释放很小空间占用大量时间时抛出；一般是因为堆太小，导致异常的原因，没有足够的内存。 
　　【解决方案】： 
　　1、查看系统是否有使用大内存的代码或死循环； 
　　2、通过添加JVM配置，来限制使用内存： 
　　< jvm-arg>-XX:-UseGCOverheadLimit< /jvm-arg> 
　　 
【情况三】： 
　　java.lang.OutOfMemoryError: PermGen space：这种是P区内存不够，可通过调整JVM的配置： 
　　< jvm-arg>-XX:MaxPermSize=128m< /jvm-arg> 
　　< jvm-arg>-XXermSize=128m< /jvm-arg> 
　　【注】： 
　　JVM的Perm区主要用于存放Class和Meta信息的,Class在被Loader时就会被放到PermGen space，这个区域成为年老代，GC在主程序运行期间不会对年老区进行清理，默认是64M大小，当程序需要加载的对象比较多时，超过64M就会报这部分内存溢出了，需要加大内存分配，一般128m足够。 
　　 
【情况四】： 
　　java.lang.OutOfMemoryError: Direct buffer memory 
　　调整-XX:MaxDirectMemorySize= 参数，如添加JVM配置： 
　　< jvm-arg>-XX:MaxDirectMemorySize=128m< /jvm-arg> 
　　 
【情况五】： 
　　java.lang.OutOfMemoryError: unable to create new native thread 
　　【原因】：Stack空间不足以创建额外的线程，要么是创建的线程过多，要么是Stack空间确实小了。 
　　【解决】：由于JVM没有提供参数设置总的stack空间大小，但可以设置单个线程栈的大小；而系统的用户空间一共是3G，除了Text/Data/BSS /MemoryMapping几个段之外，Heap和Stack空间的总量有限，是此消彼长的。因此遇到这个错误，可以通过两个途径解决： 
　　1.通过 -Xss启动参数减少单个线程栈大小，这样便能开更多线程（当然不能太小，太小会出现StackOverflowError）； 
　　2.通过-Xms -Xmx 两参数减少Heap大小，将内存让给Stack（前提是保证Heap空间够用）。 
参考：
JVM可支持的最大线程数
https://www.cnblogs.com/canacezhang/p/9318355.html
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
　　 
【情况六】： 
　　java.lang.StackOverflowError 
　　【原因】：这也内存溢出错误的一种，即线程栈的溢出，要么是方法调用层次过多（比如存在无限递归调用），要么是线程栈太小。 
　　【解决】：优化程序设计，减少方法调用层次；调整-Xss参数增加线程栈大小
```

