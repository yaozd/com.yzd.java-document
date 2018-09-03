## A.产生大对象情况（大对象是造成FULL GC产生重要原因）
```
1.本地缓存-（二级缓存是可以提高性能但也会产生内存溢出）
eg:guava的本地缓存对象达几百M
dump内存发现，IdServer对象比较大，这个是定时更新任务引起。一般的，gc的行为和代码的结构及内存中的对象有很大的关系，代码中用了guava的本地缓存对象达几百M，11分钟会更新一次，每当做一次数据的更新，会产生大对象到old区，会promotion failed 导致full gc，现在根据业务特点调整cache时间为1小时。
2.静态集合类引起内存泄漏：
eg:Static Vector v = new Vector(10);
3.资源未关闭造成的内存泄漏
4.避免 override finalize()
eg:finalize 方法被执行的时间不确定，不能依赖与它来释放紧缺的资源。时间不确定的原因是： 
虚拟机调用GC的时间不确定 
5.尽量避免使用 static 成员变量
6.各种连接
eg:比如数据库连接（dataSourse.getConnection()），网络连接(socket)和io连接，除非其显式的调用了其close（）方法将其连接关闭，否则是不会自动被GC 回收的。对于Resultset 和Statement 对象可以不进行显式回收，但Connection 一定要显式回收，因为Connection 在任何时候都无法自动回收，而Connection一旦回收，Resultset 和Statement 对象就会立即为NULL。但是如果使用连接池，情况就不一样了，除了要显式地关闭连接，还必须显式地关闭Resultset Statement 对象（关闭其中一个，另外一个也会关闭），否则就会造成大量的Statement 对象无法释放，从而引起内存泄漏。这种情况下一般都会在try里面去的连接，在finally里面释放连接。
7.单例模式-(中的集合必须要设置大小)
不正确使用单例模式是引起内存泄漏的一个常见问题
参考：
Java中关于内存泄漏出现的原因汇总及如何避免内存泄漏（超详细版
https://www.jb51.net/article/92311.htm

```
## B.JVM重要性能参数
- -XX:+DisableExplicitGC禁止System.gc()，免得程序员误调用gc方法影响性能； 
- -XX:+UseParNewGC，对年轻代采用多线程并行回收，这样收得快； 
- -XX:CMSInitiatingOccupancyFraction=60(尝试更早的对old区开始收集，已避免上面提到的情况：在回收完成之前，堆没有足够空间分配)(“60”参考：高吞吐低延迟Java应用的垃圾回收优化)
- XMX和XMS设置一样大，MaxPermSize和MinPermSize设置一样大，这样可以减轻伸缩堆大小带来的压力。 
- -XX:+UseCMSCompactAtFullCollection	开启内存空间压缩和整理，防止过多内存碎片
- -XX:CMSFullGCsBeforeCompaction=0	表示多少次Full GC后开始压缩和整理，0表示每次Full GC后立即执行压缩和整理
- -XX:MaxTenuringThreshold=0	在年轻代经过几次GC后还存活，就进入老年代，0表示直接进入老年代
- -XX:SurvivorRatio=8	年轻代中Eden区与Survivor区的容量比例值，默认为8，即8:1
- -Xms	堆内存初始大小，单位m、g
- -Xmx（MaxHeapSize）	堆内存最大允许大小，一般不要大于物理内存的80%
- -XX:PermSize	非堆内存初始大小，一般应用设置初始化200m，最大1024m就够了
- -XX:MaxPermSize	非堆内存最大允许大小
- -XX:NewSize（-Xns）	年轻代内存初始大小
- -XX:MaxNewSize（-Xmn）	年轻代内存最大允许大小，也可以缩写
- -Xss	堆栈内存大小

参考：
- [Java堆内存又溢出了！教你一招必杀技](http://blog.51cto.com/lizhenliang/2164876)
- [高吞吐低延迟Java应用的垃圾回收优化](http://www.importnew.com/11336.html)
- [JVM性能参数调优实践，不会执行Full GC，网站无停滞-byArvin推荐细读](https://blog.csdn.net/u014236541/article/details/50008047)



## C.JVM可支持的最大线程数-(系统线程的内存用的不是JVMMemory，而是系统中剩下的内存)
```
JVM可支持的最大线程数
https://www.cnblogs.com/canacezhang/p/9318355.html
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
==
这个异常问题本质原因是我们创建了太多的线程，而能创建的线程数是有限制的，导致了异常的发生。能创建的线程数的具体计算公式如下：
(MaxProcessMemory - JVMMemory - ReservedOsMemory) / (ThreadStackSize) = Number of threads
MaxProcessMemory 指的是一个进程的最大内存
JVMMemory         JVM内存
ReservedOsMemory  保留的操作系统内存
ThreadStackSize      线程栈的大小

在java语言里， 当你创建一个线程的时候，虚拟机会在JVM内存创建一个Thread对象同时创建一个操作系统线程，而这个系统线程的内存用的不是JVMMemory，而是系统中剩下的内存(MaxProcessMemory - JVMMemory - ReservedOsMemory)。
由公式得出结论：你给JVM内存越多，那么你能创建的线程越少，越容易发生java.lang.OutOfMemoryError: unable to create new native thread。
咦，有点背我们的常理，恩，让我们来验证一下,依旧使用上面的测试程序，加上下面的JVM参数，测试结果如下：
```


### 1.[就目前而言、CMS还是默认首选的GC策略、可能在以下场景下G1更适合](http://ifeve.com/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3g1%E5%9E%83%E5%9C%BE%E6%94%B6%E9%9B%86%E5%99%A8/)
```
深入理解G1垃圾收集器
http://ifeve.com/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3g1%E5%9E%83%E5%9C%BE%E6%94%B6%E9%9B%86%E5%99%A8/
就目前而言、CMS还是默认首选的GC策略、可能在以下场景下G1更适合：
==
服务端多核CPU、JVM内存占用较大的应用（[至少大于4G]）
应用在运行过程中会产生大量内存碎片、需要经常压缩空间
想要更可控、可预期的GC停顿周期；防止高并发下应用雪崩现象
```
### 2.平时工作中大多数系统都使用CMS、即使静默升级到JDK7默认仍然采用CMS、那么G1相对于CMS的区别在：
```
深入理解G1垃圾收集器
http://ifeve.com/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3g1%E5%9E%83%E5%9C%BE%E6%94%B6%E9%9B%86%E5%99%A8/
平时工作中大多数系统都使用CMS、即使静默升级到JDK7默认仍然采用CMS、那么G1相对于CMS的区别在：

G1在压缩空间方面有优势
G1通过将内存空间分成区域（Region）的方式避免内存碎片问题
Eden, Survivor, Old区不再固定、在内存使用效率上来说更灵活
G1可以通过设置预期停顿时间（Pause Time）来控制垃圾收集时间避免应用雪崩现象
G1在回收内存后会马上同时做合并空闲内存的工作、而CMS默认是在STW（stop the world）的时候做
G1会在Young GC中使用、而CMS只能在O区使用
就目前而言、CMS还是默认首选的GC策略、可能在以下场景下G1更适合：

服务端多核CPU、JVM内存占用较大的应用（至少大于4G）
应用在运行过程中会产生大量内存碎片、需要经常压缩空间
想要更可控、可预期的GC停顿周期；防止高并发下应用雪崩现象
```
### 3.“stop-the-world”
```
成为JavaGC专家（1）—深入浅出Java垃圾回收机制
http://www.importnew.com/1993.html
回到正题，咱们继续谈垃圾回收，在学习GC之前，你首先应该记住一个单词：“stop-the-world”。Stop-the-world会在任何一种GC算法中发生。Stop-the-world意味着 JVM 因为要执行GC而停止了应用程序的执行。当Stop-the-world发生时，除了GC所需的线程以外，所有线程都处于等待状态，直到GC任务完成。GC优化很多时候就是指减少Stop-the-world发生的时间。
```
### 5.老年代GC处理机制
```
成为JavaGC专家（1）—深入浅出Java垃圾回收机制
http://www.importnew.com/1993.html
老年代空间的GC事件基本上是在空间已满时发生，执行的过程根据GC类型不同而不同，因此，了解不同的GC类型将有助于你理解本节的内容。
JDK7一共有5种GC类型：

Serial GC
Parallel GC
Parallel Old GC (Parallel Compacting GC)
Concurrent Mark & Sweep GC  (or “CMS”)
Garbage First (G1) GC
其中，Serial GC不应该被用在服务器上。这种GC类型在单核CPU的桌面电脑时代就存在了。使用Serial GC会显著的降低应用的性能指标。
现在，让我们共同学习每一种GC类型
```
### 6.CMS GC (-XX:+UseConcMarkSweepGC)
```
成为JavaGC专家（1）—深入浅出Java垃圾回收机制
http://www.importnew.com/1993.html
就像你从上图看到的那样, CMS GC比我之前解释的各种算法都要复杂很多。第一步初始化标记（initial mark） 比较简单。这一步骤只是查找那些距离类加载器最近的幸存对象。因此，停顿的时间非常短暂。在之后的并行标记（ concurrent mark ）步骤，所有被幸存对象引用的对象会被确认是否已经被追踪和校验。这一步的不同之处在于，在标记的过程中，其他的线程依然在执行。在重新标记（remark）步骤，会再次检查那些在并行标记步骤中增加或者删除的与幸存对象引用的对象。最后，在并行交换（ concurrent sweep ）步骤，转交垃圾回收过程处理。垃圾回收工作会在其他线程的执行过程中展开。一旦采取了这种GC类型，由GC导致的暂停时间会极其短暂。CMS GC也被称为低延迟GC。它经常被用在那些对于响应时间要求十分苛刻的应用之上。

当然，这种GC类型在拥有stop-the-world时间很短的优点的同时，也有如下缺点：

 它会比其他GC类型占用更多的内存和CPU
 默认情况下不支持压缩步骤
在使用这个GC类型之前你需要慎重考虑。如果因为内存碎片过多而导致压缩任务不得不执行，那么stop-the-world的时间要比其他任何GC类型都长，你需要考虑压缩任务的发生频率以及执行时间。
```
### 7.高吞吐低延迟Java应用的垃圾回收优化
```
高吞吐低延迟Java应用的垃圾回收优化
http://www.importnew.com/11336.html
仔细考量GC需求
为降低应用性能的GC开销，可以优化GC的一些特征。吞吐量、延迟等这些GC特征应该长时间测试运行观察，确保特征数据来自于应用程序的处理对象数量发生变化的多个GC周期。

Stop-the-world回收器回收垃圾时会暂停应用线程。停顿的时长和频率不应该对应用遵守SLA产生不利的影响。
并发GC算法与应用线程竞争CPU周期。这个开销不应该影响应用吞吐量。
不压缩GC算法会引起堆碎片化，导致full GC长时间Stop-the-world停顿。
垃圾回收工作需要占用内存。一些GC算法产生更高的内存占用。如果应用程序需要较大的堆空间，要确保GC的内存开销不能太大。
清晰地了解GC日志和常用的JVM参数对简单调整GC运行很有必要。GC运行随着代码复杂度增长或者工作特性变化而改变。
===
LinkedIn动态信息数据平台的GC优化
对于该平台原型系统，我们使用Hotspot JVM的两个算法优化垃圾回收：

新生代垃圾回收使用ParNew，老年代垃圾回收使用CMS。
新生代和老年代使用G1。G1用来解决堆大小为6GB或者更大时存在的低于0.5秒稳定的、可预测停顿时间的问题。在我们用G1实验过程中，尽管调整了各种参数，但没有得到像ParNew/CMS一样的GC性能或停顿时间的可预测值。我们查询了使用G1发生内存泄漏相关的一个bug[3]，但还不能确定根本原因。
使用ParNew/CMS，应用每三秒40-60ms的新生代停顿和每小时一个CMS周期。JVM选项如下：

// JVM sizing options
-server -Xms40g -Xmx40g -XX:MaxDirectMemorySize=4096m -XX:PermSize=256m -XX:MaxPermSize=256m   
// Young generation options
-XX:NewSize=6g -XX:MaxNewSize=6g -XX:+UseParNewGC -XX:MaxTenuringThreshold=2 -XX:SurvivorRatio=8 -XX:+UnlockDiagnosticVMOptions -XX:ParGCCardsPerStrideChunk=32768
// Old generation  options
-XX:+UseConcMarkSweepGC -XX:CMSParallelRemarkEnabled -XX:+ParallelRefProcEnabled -XX:+CMSClassUnloadingEnabled  -XX:CMSInitiatingOccupancyFraction=80 -XX:+UseCMSInitiatingOccupancyOnly   
// Other options
-XX:+AlwaysPreTouch -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintGCDateStamps -XX:+PrintTenuringDistribution -XX:+PrintGCApplicationStoppedTime -XX:-OmitStackTraceInFastThrow
使用这些选项，对于几千次读请求的吞吐量，应用百分之99.9的延迟降低到60ms。

```
### 8. G1存在一些内存泄露的bug
```
高吞吐低延迟Java应用的垃圾回收优化
http://www.importnew.com/11336.html
[1] -XX:+BindGCTaskThreadsToCPUs似乎在Linux系统上不起作用，因为hotspot/src/os/linux/vm/os_linux.cpp的distribute_processes方法在JDK7或JDK8没有实现。
[2] -XX:+UseGCTaskAffinity选项在JDK7和JDK8的所有平台似乎都不起作用，因为任务的affinity属性永远被设置为sentinel_worker = (uint) -1。源码见hotspot/src/share/vm/gc_implementation/parallelScavenge/{gcTaskManager.cpp，gcTaskThread.cpp, gcTaskManager.cpp}。
[3] G1存在一些内存泄露的bug，可能Java7u51没有修改。这个bug仅在Java 8修正了。
```
### 9.从一次 FULL GC 卡顿谈对服务的影响
```
从一次 FULL GC 卡顿谈对服务的影响
https://blog.csdn.net/Weiguang_123/article/details/48577175
二、优化的第一步
实验策略：CMSInitiatingOccupancyFraction调整为70，这样单纯的修改XX:CMSInitiatingOccupancyFraction阀值，目的是尝试更早的对old区开始收集，已避免上面提到的情况：在回收完成之前，堆没有足够空间分配。
发布到线上，观察一段时间，发现日志没有了promontion faild，但是full gc times和count并没有明显的变化。
===
三、优化的第二步
上面方法不太好，因为没有用到救助空间，所以年老代容易满，CMS执行会比较频繁。我改善了一下，还是用救助空间，但是把救助空间加大，这样也不会有promotion failed。为了解决暂停问题和promotion failed问题，最后设置-XX:SurvivorRatio=1 ，并把MaxTenuringThreshold去掉，这样即没有暂停又不会有promotoin failed，而且更重要的是，年老代和永久代上升非常慢（因为好多对象到不了年老代就被回收了），所以CMS执行频率非常低，好近小时才执行一次，这样，服务器都不用重启了。

JVM_GC="-XX:+PrintGC -XX:+PrintGCTimeStamps -XX:+PrintGCDateStamps -XX:+PrintGCDetails -XX:+PrintHeapAtGC -XX:+PrintTenuringDistribution -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:CMSFullGCsBeforeCompaction=1 -XX:ParallelCMSThreads=4 -XX:CMSInitiatingOccupancyFraction=56 -XX:+CMSParallelRemarkEnabled -XX:+ExplicitGCInvokesConcurrent -XX:+CMSPermGenSweepingEnabled -XX:+CMSClassUnloadingEnabled"
JVM_SIZE="-Xms4500m -Xmx4500m"
JVM_HEAP="-Xmn1500m -XX:PermSize=128m -XX:MaxPermSize=256m -XX:SurvivorRatio=1"
===
参数解释说明：
-XX:CMSInitiatingOccupancyFraction=60(尝试更早的对old区开始收集，已避免上面提到的情况：在回收完成之前，堆没有足够空间分配)
-XX:+UseCMSCompactAtFullCollection -XX:CMSFullGCsBeforeCompaction=1(CMS垃圾回收会产生脆片，GC的时候整理一下)
-Xmn1500m(增加年轻态的内存空间)
-XX:SurvivorRatio=1（MaxTenuringThreshold去掉，这样即没有暂停又不会有promotoin failed，而且更重要的是，年老代和永久代上升非常慢）
```
### 10.JVM性能参数调优实践，不会执行Full GC，网站无停滞-byArvin推荐细读
```
JVM性能参数调优实践，不会执行Full GC，网站无停滞
https://blog.csdn.net/u014236541/article/details/50008047
我的最终配置如下（系统8G内存），每天几百万pv一点问题都没有，网站没有停顿，2009年shedewang.com没有因为内存问题down过机。 

$JAVA_ARGS .= " -Dresin.home=$SERVER_ROOT -server -Xms6000M -Xmx6000M -Xmn500M -XX:PermSize=500M -XX:MaxPermSize=500M -XX:SurvivorRatio=65536 -XX:MaxTenuringThreshold=0 -Xnoclassgc -XX:+DisableExplicitGC -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:CMSFullGCsBeforeCompaction=0 -XX:+CMSClassUnloadingEnabled -XX:-CMSParallelRemarkEnabled -XX:CMSInitiatingOccupancyFraction=90 -XX:SoftRefLRUPolicyMSPerMB=0 -XX:+PrintClassHistogram -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintHeapAtGC -Xloggc:log/gc.log "; 

说明一下， -XX:SurvivorRatio=65536 -XX:MaxTenuringThreshold=0就是去掉了救助空间； 
-Xnoclassgc禁用类垃圾回收，性能会高一点； 
-XX:+DisableExplicitGC禁止System.gc()，免得程序员误调用gc方法影响性能； 
-XX:+UseParNewGC，对年轻代采用多线程并行回收，这样收得快； 
带CMS参数的都是和并发回收相关的，不明白的可以上网搜索； 
CMSInitiatingOccupancyFraction，这个参数设置有很大技巧，基本上满足(Xmx-Xmn)*(100-CMSInitiatingOccupancyFraction)/100>=Xmn就不会出现promotion failed。在我的应用中Xmx是6000，Xmn是500，那么Xmx-Xmn是5500兆，也就是年老代有5500兆，CMSInitiatingOccupancyFraction=90说明年老代到90%满的时候开始执行对年老代的并发垃圾回收（CMS），这时还剩10%的空间是5500*10%=550兆，所以即使Xmn（也就是年轻代共500兆）里所有对象都搬到年老代里，550兆的空间也足够了，所以只要满足上面的公式，就不会出现垃圾回收时的promotion failed； 
SoftRefLRUPolicyMSPerMB这个参数我认为可能有点用，官方解释是softly reachable objects will remain alive for some amount of time after the last time they were referenced. The default value is one second of lifetime per free megabyte in the heap，我觉得没必要等1秒； 

网上其他介绍JVM参数的也比较多，估计其中大部分是没有遇到promotion failed，或者访问量太小没有机会遇到，(Xmx-Xmn)*(100-CMSInitiatingOccupancyFraction)/100>=Xmn这个公式绝对是原创，真遇到promotion failed了，还得这么处理。

```