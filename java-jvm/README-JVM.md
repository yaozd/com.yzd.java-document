## A.������������������������FULL GC������Ҫԭ��
```
1.���ػ���-�����������ǿ���������ܵ�Ҳ������ڴ������
eg:guava�ı��ػ������Ｘ��M
dump�ڴ淢�֣�IdServer����Ƚϴ�����Ƕ�ʱ������������һ��ģ�gc����Ϊ�ʹ���Ľṹ���ڴ��еĶ����кܴ�Ĺ�ϵ������������guava�ı��ػ������Ｘ��M��11���ӻ����һ�Σ�ÿ����һ�����ݵĸ��£�����������old������promotion failed ����full gc�����ڸ���ҵ���ص����cacheʱ��Ϊ1Сʱ��
2.��̬�����������ڴ�й©��
eg:Static Vector v = new Vector(10);
3.��Դδ�ر���ɵ��ڴ�й©
4.���� override finalize()
eg:finalize ������ִ�е�ʱ�䲻ȷ�������������������ͷŽ�ȱ����Դ��ʱ�䲻ȷ����ԭ���ǣ� 
���������GC��ʱ�䲻ȷ�� 
5.��������ʹ�� static ��Ա����
6.��������
eg:�������ݿ����ӣ�dataSourse.getConnection()������������(socket)��io���ӣ���������ʽ�ĵ�������close���������������ӹرգ������ǲ����Զ���GC ���յġ�����Resultset ��Statement ������Բ�������ʽ���գ���Connection һ��Ҫ��ʽ���գ���ΪConnection ���κ�ʱ���޷��Զ����գ���Connectionһ�����գ�Resultset ��Statement ����ͻ�����ΪNULL���������ʹ�����ӳأ�����Ͳ�һ���ˣ�����Ҫ��ʽ�عر����ӣ���������ʽ�عر�Resultset Statement ���󣨹ر�����һ��������һ��Ҳ��رգ�������ͻ���ɴ�����Statement �����޷��ͷţ��Ӷ������ڴ�й©�����������һ�㶼����try����ȥ�����ӣ���finally�����ͷ����ӡ�
7.����ģʽ-(�еļ��ϱ���Ҫ���ô�С)
����ȷʹ�õ���ģʽ�������ڴ�й©��һ����������
�ο���
Java�й����ڴ�й©���ֵ�ԭ����ܼ���α����ڴ�й©������ϸ��
https://www.jb51.net/article/92311.htm

```
## B.JVM��Ҫ���ܲ���
- -XX:+DisableExplicitGC��ֹSystem.gc()����ó���Ա�����gc����Ӱ�����ܣ� 
- -XX:+UseParNewGC������������ö��̲߳��л��գ������յÿ죻 
- -XX:CMSInitiatingOccupancyFraction=60(���Ը���Ķ�old����ʼ�ռ����ѱ��������ᵽ��������ڻ������֮ǰ����û���㹻�ռ����)(��60���ο��������µ��ӳ�JavaӦ�õ����������Ż�)
- XMX��XMS����һ����MaxPermSize��MinPermSize����һ�����������Լ��������Ѵ�С������ѹ���� 
- -XX:+UseCMSCompactAtFullCollection	�����ڴ�ռ�ѹ����������ֹ�����ڴ���Ƭ
- -XX:CMSFullGCsBeforeCompaction=0	��ʾ���ٴ�Full GC��ʼѹ��������0��ʾÿ��Full GC������ִ��ѹ��������
- -XX:MaxTenuringThreshold=0	���������������GC�󻹴��ͽ����������0��ʾֱ�ӽ��������
- -XX:SurvivorRatio=8	�������Eden����Survivor������������ֵ��Ĭ��Ϊ8����8:1
- -Xms	���ڴ��ʼ��С����λm��g
- -Xmx��MaxHeapSize��	���ڴ���������С��һ�㲻Ҫ���������ڴ��80%
- -XX:PermSize	�Ƕ��ڴ��ʼ��С��һ��Ӧ�����ó�ʼ��200m�����1024m�͹���
- -XX:MaxPermSize	�Ƕ��ڴ���������С
- -XX:NewSize��-Xns��	������ڴ��ʼ��С
- -XX:MaxNewSize��-Xmn��	������ڴ���������С��Ҳ������д
- -Xss	��ջ�ڴ��С

�ο���
- [Java���ڴ�������ˣ�����һ�б�ɱ��](http://blog.51cto.com/lizhenliang/2164876)
- [�����µ��ӳ�JavaӦ�õ����������Ż�](http://www.importnew.com/11336.html)
- [JVM���ܲ�������ʵ��������ִ��Full GC����վ��ͣ��-byArvin�Ƽ�ϸ��](https://blog.csdn.net/u014236541/article/details/50008047)



## C.JVM��֧�ֵ�����߳���-(ϵͳ�̵߳��ڴ��õĲ���JVMMemory������ϵͳ��ʣ�µ��ڴ�)
```
JVM��֧�ֵ�����߳���
https://www.cnblogs.com/canacezhang/p/9318355.html
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
==
����쳣���Ȿ��ԭ�������Ǵ�����̫����̣߳����ܴ������߳����������Ƶģ��������쳣�ķ������ܴ������߳����ľ�����㹫ʽ���£�
(MaxProcessMemory - JVMMemory - ReservedOsMemory) / (ThreadStackSize) = Number of threads
MaxProcessMemory ָ����һ�����̵�����ڴ�
JVMMemory         JVM�ڴ�
ReservedOsMemory  �����Ĳ���ϵͳ�ڴ�
ThreadStackSize      �߳�ջ�Ĵ�С

��java����� ���㴴��һ���̵߳�ʱ�����������JVM�ڴ洴��һ��Thread����ͬʱ����һ������ϵͳ�̣߳������ϵͳ�̵߳��ڴ��õĲ���JVMMemory������ϵͳ��ʣ�µ��ڴ�(MaxProcessMemory - JVMMemory - ReservedOsMemory)��
�ɹ�ʽ�ó����ۣ����JVM�ڴ�Խ�࣬��ô���ܴ������߳�Խ�٣�Խ���׷���java.lang.OutOfMemoryError: unable to create new native thread��
�ף��е㱳���ǵĳ�����������������֤һ��,����ʹ������Ĳ��Գ��򣬼��������JVM���������Խ�����£�
```


### 1.[��Ŀǰ���ԡ�CMS����Ĭ����ѡ��GC���ԡ����������³�����G1���ʺ�](http://ifeve.com/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3g1%E5%9E%83%E5%9C%BE%E6%94%B6%E9%9B%86%E5%99%A8/)
```
�������G1�����ռ���
http://ifeve.com/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3g1%E5%9E%83%E5%9C%BE%E6%94%B6%E9%9B%86%E5%99%A8/
��Ŀǰ���ԡ�CMS����Ĭ����ѡ��GC���ԡ����������³�����G1���ʺϣ�
==
����˶��CPU��JVM�ڴ�ռ�ýϴ��Ӧ�ã�[���ٴ���4G]��
Ӧ�������й����л���������ڴ���Ƭ����Ҫ����ѹ���ռ�
��Ҫ���ɿء���Ԥ�ڵ�GCͣ�����ڣ���ֹ�߲�����Ӧ��ѩ������
```
### 2.ƽʱ�����д����ϵͳ��ʹ��CMS����ʹ��Ĭ������JDK7Ĭ����Ȼ����CMS����ôG1�����CMS�������ڣ�
```
�������G1�����ռ���
http://ifeve.com/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3g1%E5%9E%83%E5%9C%BE%E6%94%B6%E9%9B%86%E5%99%A8/
ƽʱ�����д����ϵͳ��ʹ��CMS����ʹ��Ĭ������JDK7Ĭ����Ȼ����CMS����ôG1�����CMS�������ڣ�

G1��ѹ���ռ䷽��������
G1ͨ�����ڴ�ռ�ֳ�����Region���ķ�ʽ�����ڴ���Ƭ����
Eden, Survivor, Old�����ٹ̶������ڴ�ʹ��Ч������˵�����
G1����ͨ������Ԥ��ͣ��ʱ�䣨Pause Time�������������ռ�ʱ�����Ӧ��ѩ������
G1�ڻ����ڴ�������ͬʱ���ϲ������ڴ�Ĺ�������CMSĬ������STW��stop the world����ʱ����
G1����Young GC��ʹ�á���CMSֻ����O��ʹ��
��Ŀǰ���ԡ�CMS����Ĭ����ѡ��GC���ԡ����������³�����G1���ʺϣ�

����˶��CPU��JVM�ڴ�ռ�ýϴ��Ӧ�ã����ٴ���4G��
Ӧ�������й����л���������ڴ���Ƭ����Ҫ����ѹ���ռ�
��Ҫ���ɿء���Ԥ�ڵ�GCͣ�����ڣ���ֹ�߲�����Ӧ��ѩ������
```
### 3.��stop-the-world��
```
��ΪJavaGCר�ң�1��������ǳ��Java�������ջ���
http://www.importnew.com/1993.html
�ص����⣬���Ǽ���̸�������գ���ѧϰGC֮ǰ��������Ӧ�ü�סһ�����ʣ���stop-the-world����Stop-the-world�����κ�һ��GC�㷨�з�����Stop-the-world��ζ�� JVM ��ΪҪִ��GC��ֹͣ��Ӧ�ó����ִ�С���Stop-the-world����ʱ������GC������߳����⣬�����̶߳����ڵȴ�״̬��ֱ��GC������ɡ�GC�Ż��ܶ�ʱ�����ָ����Stop-the-world������ʱ�䡣
```
### 5.�����GC�������
```
��ΪJavaGCר�ң�1��������ǳ��Java�������ջ���
http://www.importnew.com/1993.html
������ռ��GC�¼����������ڿռ�����ʱ������ִ�еĹ��̸���GC���Ͳ�ͬ����ͬ����ˣ��˽ⲻͬ��GC���ͽ�����������Ȿ�ڵ����ݡ�
JDK7һ����5��GC���ͣ�

Serial GC
Parallel GC
Parallel Old GC (Parallel Compacting GC)
Concurrent Mark & Sweep GC  (or ��CMS��)
Garbage First (G1) GC
���У�Serial GC��Ӧ�ñ����ڷ������ϡ�����GC�����ڵ���CPU���������ʱ���ʹ����ˡ�ʹ��Serial GC�������Ľ���Ӧ�õ�����ָ�ꡣ
���ڣ������ǹ�ͬѧϰÿһ��GC����
```
### 6.CMS GC (-XX:+UseConcMarkSweepGC)
```
��ΪJavaGCר�ң�1��������ǳ��Java�������ջ���
http://www.importnew.com/1993.html
���������ͼ����������, CMS GC����֮ǰ���͵ĸ����㷨��Ҫ���Ӻܶࡣ��һ����ʼ����ǣ�initial mark�� �Ƚϼ򵥡���һ����ֻ�ǲ�����Щ�����������������Ҵ������ˣ�ͣ�ٵ�ʱ��ǳ����ݡ���֮��Ĳ��б�ǣ� concurrent mark �����裬���б��Ҵ�������õĶ���ᱻȷ���Ƿ��Ѿ���׷�ٺ�У�顣��һ���Ĳ�֮ͬ�����ڣ��ڱ�ǵĹ����У��������߳���Ȼ��ִ�С������±�ǣ�remark�����裬���ٴμ����Щ�ڲ��б�ǲ��������ӻ���ɾ�������Ҵ�������õĶ�������ڲ��н����� concurrent sweep �����裬ת���������չ��̴����������չ������������̵߳�ִ�й�����չ����һ����ȡ������GC���ͣ���GC���µ���ͣʱ��Ἣ����ݡ�CMS GCҲ����Ϊ���ӳ�GC����������������Щ������Ӧʱ��Ҫ��ʮ�ֿ��̵�Ӧ��֮�ϡ�

��Ȼ������GC������ӵ��stop-the-worldʱ��̵ܶ��ŵ��ͬʱ��Ҳ������ȱ�㣺

 ���������GC����ռ�ø�����ڴ��CPU
 Ĭ������²�֧��ѹ������
��ʹ�����GC����֮ǰ����Ҫ���ؿ��ǡ������Ϊ�ڴ���Ƭ���������ѹ�����񲻵ò�ִ�У���ôstop-the-world��ʱ��Ҫ�������κ�GC���Ͷ���������Ҫ����ѹ������ķ���Ƶ���Լ�ִ��ʱ�䡣
```
### 7.�����µ��ӳ�JavaӦ�õ����������Ż�
```
�����µ��ӳ�JavaӦ�õ����������Ż�
http://www.importnew.com/11336.html
��ϸ����GC����
Ϊ����Ӧ�����ܵ�GC�����������Ż�GC��һЩ���������������ӳٵ���ЩGC����Ӧ�ó�ʱ��������й۲죬ȷ����������������Ӧ�ó���Ĵ���������������仯�Ķ��GC���ڡ�

Stop-the-world��������������ʱ����ͣӦ���̡߳�ͣ�ٵ�ʱ����Ƶ�ʲ�Ӧ�ö�Ӧ������SLA����������Ӱ�졣
����GC�㷨��Ӧ���߳̾���CPU���ڡ����������Ӧ��Ӱ��Ӧ����������
��ѹ��GC�㷨���������Ƭ��������full GC��ʱ��Stop-the-worldͣ�١�
�������չ�����Ҫռ���ڴ档һЩGC�㷨�������ߵ��ڴ�ռ�á����Ӧ�ó�����Ҫ�ϴ�Ķѿռ䣬Ҫȷ��GC���ڴ濪������̫��
�������˽�GC��־�ͳ��õ�JVM�����Լ򵥵���GC���к��б�Ҫ��GC�������Ŵ��븴�Ӷ��������߹������Ա仯���ı䡣
===
LinkedIn��̬��Ϣ����ƽ̨��GC�Ż�
���ڸ�ƽ̨ԭ��ϵͳ������ʹ��Hotspot JVM�������㷨�Ż��������գ�

��������������ʹ��ParNew���������������ʹ��CMS��
�������������ʹ��G1��G1��������Ѵ�СΪ6GB���߸���ʱ���ڵĵ���0.5���ȶ��ġ���Ԥ��ͣ��ʱ������⡣��������G1ʵ������У����ܵ����˸��ֲ�������û�еõ���ParNew/CMSһ����GC���ܻ�ͣ��ʱ��Ŀ�Ԥ��ֵ�����ǲ�ѯ��ʹ��G1�����ڴ�й©��ص�һ��bug[3]����������ȷ������ԭ��
ʹ��ParNew/CMS��Ӧ��ÿ����40-60ms��������ͣ�ٺ�ÿСʱһ��CMS���ڡ�JVMѡ�����£�

// JVM sizing options
-server -Xms40g -Xmx40g -XX:MaxDirectMemorySize=4096m -XX:PermSize=256m -XX:MaxPermSize=256m   
// Young generation options
-XX:NewSize=6g -XX:MaxNewSize=6g -XX:+UseParNewGC -XX:MaxTenuringThreshold=2 -XX:SurvivorRatio=8 -XX:+UnlockDiagnosticVMOptions -XX:ParGCCardsPerStrideChunk=32768
// Old generation  options
-XX:+UseConcMarkSweepGC -XX:CMSParallelRemarkEnabled -XX:+ParallelRefProcEnabled -XX:+CMSClassUnloadingEnabled  -XX:CMSInitiatingOccupancyFraction=80 -XX:+UseCMSInitiatingOccupancyOnly   
// Other options
-XX:+AlwaysPreTouch -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintGCDateStamps -XX:+PrintTenuringDistribution -XX:+PrintGCApplicationStoppedTime -XX:-OmitStackTraceInFastThrow
ʹ����Щѡ����ڼ�ǧ�ζ��������������Ӧ�ðٷ�֮99.9���ӳٽ��͵�60ms��

```
### 8. G1����һЩ�ڴ�й¶��bug
```
�����µ��ӳ�JavaӦ�õ����������Ż�
http://www.importnew.com/11336.html
[1] -XX:+BindGCTaskThreadsToCPUs�ƺ���Linuxϵͳ�ϲ������ã���Ϊhotspot/src/os/linux/vm/os_linux.cpp��distribute_processes������JDK7��JDK8û��ʵ�֡�
[2] -XX:+UseGCTaskAffinityѡ����JDK7��JDK8������ƽ̨�ƺ����������ã���Ϊ�����affinity������Զ������Ϊsentinel_worker = (uint) -1��Դ���hotspot/src/share/vm/gc_implementation/parallelScavenge/{gcTaskManager.cpp��gcTaskThread.cpp, gcTaskManager.cpp}��
[3] G1����һЩ�ڴ�й¶��bug������Java7u51û���޸ġ����bug����Java 8�����ˡ�
```
### 9.��һ�� FULL GC ����̸�Է����Ӱ��
```
��һ�� FULL GC ����̸�Է����Ӱ��
https://blog.csdn.net/Weiguang_123/article/details/48577175
�����Ż��ĵ�һ��
ʵ����ԣ�CMSInitiatingOccupancyFraction����Ϊ70�������������޸�XX:CMSInitiatingOccupancyFraction��ֵ��Ŀ���ǳ��Ը���Ķ�old����ʼ�ռ����ѱ��������ᵽ��������ڻ������֮ǰ����û���㹻�ռ���䡣
���������ϣ��۲�һ��ʱ�䣬������־û����promontion faild������full gc times��count��û�����Եı仯��
===
�����Ż��ĵڶ���
���淽����̫�ã���Ϊû���õ������ռ䣬�������ϴ���������CMSִ�л�Ƚ�Ƶ�����Ҹ�����һ�£������þ����ռ䣬���ǰѾ����ռ�Ӵ�����Ҳ������promotion failed��Ϊ�˽����ͣ�����promotion failed���⣬�������-XX:SurvivorRatio=1 ������MaxTenuringThresholdȥ����������û����ͣ�ֲ�����promotoin failed�����Ҹ���Ҫ���ǣ����ϴ������ô������ǳ�������Ϊ�ö���󵽲������ϴ��ͱ������ˣ�������CMSִ��Ƶ�ʷǳ��ͣ��ý�Сʱ��ִ��һ�Σ������������������������ˡ�

JVM_GC="-XX:+PrintGC -XX:+PrintGCTimeStamps -XX:+PrintGCDateStamps -XX:+PrintGCDetails -XX:+PrintHeapAtGC -XX:+PrintTenuringDistribution -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:CMSFullGCsBeforeCompaction=1 -XX:ParallelCMSThreads=4 -XX:CMSInitiatingOccupancyFraction=56 -XX:+CMSParallelRemarkEnabled -XX:+ExplicitGCInvokesConcurrent -XX:+CMSPermGenSweepingEnabled -XX:+CMSClassUnloadingEnabled"
JVM_SIZE="-Xms4500m -Xmx4500m"
JVM_HEAP="-Xmn1500m -XX:PermSize=128m -XX:MaxPermSize=256m -XX:SurvivorRatio=1"
===
��������˵����
-XX:CMSInitiatingOccupancyFraction=60(���Ը���Ķ�old����ʼ�ռ����ѱ��������ᵽ��������ڻ������֮ǰ����û���㹻�ռ����)
-XX:+UseCMSCompactAtFullCollection -XX:CMSFullGCsBeforeCompaction=1(CMS�������ջ������Ƭ��GC��ʱ������һ��)
-Xmn1500m(��������̬���ڴ�ռ�)
-XX:SurvivorRatio=1��MaxTenuringThresholdȥ����������û����ͣ�ֲ�����promotoin failed�����Ҹ���Ҫ���ǣ����ϴ������ô������ǳ�����
```
### 10.JVM���ܲ�������ʵ��������ִ��Full GC����վ��ͣ��-byArvin�Ƽ�ϸ��
```
JVM���ܲ�������ʵ��������ִ��Full GC����վ��ͣ��
https://blog.csdn.net/u014236541/article/details/50008047
�ҵ������������£�ϵͳ8G�ڴ棩��ÿ�켸����pvһ�����ⶼû�У���վû��ͣ�٣�2009��shedewang.comû����Ϊ�ڴ�����down������ 

$JAVA_ARGS .= " -Dresin.home=$SERVER_ROOT -server -Xms6000M -Xmx6000M -Xmn500M -XX:PermSize=500M -XX:MaxPermSize=500M -XX:SurvivorRatio=65536 -XX:MaxTenuringThreshold=0 -Xnoclassgc -XX:+DisableExplicitGC -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:CMSFullGCsBeforeCompaction=0 -XX:+CMSClassUnloadingEnabled -XX:-CMSParallelRemarkEnabled -XX:CMSInitiatingOccupancyFraction=90 -XX:SoftRefLRUPolicyMSPerMB=0 -XX:+PrintClassHistogram -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintHeapAtGC -Xloggc:log/gc.log "; 

˵��һ�£� -XX:SurvivorRatio=65536 -XX:MaxTenuringThreshold=0����ȥ���˾����ռ䣻 
-Xnoclassgc�������������գ����ܻ��һ�㣻 
-XX:+DisableExplicitGC��ֹSystem.gc()����ó���Ա�����gc����Ӱ�����ܣ� 
-XX:+UseParNewGC������������ö��̲߳��л��գ������յÿ죻 
��CMS�����Ķ��ǺͲ���������صģ������׵Ŀ������������� 
CMSInitiatingOccupancyFraction��������������кܴ��ɣ�����������(Xmx-Xmn)*(100-CMSInitiatingOccupancyFraction)/100>=Xmn�Ͳ������promotion failed�����ҵ�Ӧ����Xmx��6000��Xmn��500����ôXmx-Xmn��5500�ף�Ҳ�������ϴ���5500�ף�CMSInitiatingOccupancyFraction=90˵�����ϴ���90%����ʱ��ʼִ�ж����ϴ��Ĳ����������գ�CMS������ʱ��ʣ10%�Ŀռ���5500*10%=550�ף����Լ�ʹXmn��Ҳ�����������500�ף������ж��󶼰ᵽ���ϴ��550�׵Ŀռ�Ҳ�㹻�ˣ�����ֻҪ��������Ĺ�ʽ���Ͳ��������������ʱ��promotion failed�� 
SoftRefLRUPolicyMSPerMB�����������Ϊ�����е��ã��ٷ�������softly reachable objects will remain alive for some amount of time after the last time they were referenced. The default value is one second of lifetime per free megabyte in the heap���Ҿ���û��Ҫ��1�룻 

������������JVM������Ҳ�Ƚ϶࣬�������д󲿷���û������promotion failed�����߷�����̫Сû�л���������(Xmx-Xmn)*(100-CMSInitiatingOccupancyFraction)/100>=Xmn�����ʽ������ԭ����������promotion failed�ˣ�������ô����

```