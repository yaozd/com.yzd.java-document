## 1.�ڴ�����Ĳ���ԭ�򼰽������
- [�ڴ�����Ĳ���ԭ�򼰽������](http://www.cnblogs.com/canacezhang/p/9526570.html)
- [Java�ڴ������OOM����ԭ�򼰽������(�ؿ�)](https://www.jb51.net/article/110743.htm)
- [Java�й����ڴ�й©���ֵ�ԭ����ܼ���α����ڴ�й©������ϸ�棩](https://www.jb51.net/article/92311.htm)
- [full gcƵ���ķ������������](https://www.2cto.com/kf/201604/499063.html)

```
һ�������ڴ������

1��java.lang.OutofMemoryError:Java heap space
2��java.lang.OutofMemoryError:PermGen space
3��java.lang.OutofMemoryError:unable to create new native thread
4��java.lang.OutofMemoryError:GC overhead limit exceeded
1��Java�ѿռ䲻������Ӧ�ó������������ڴ棬��Java���ڴ��Ѿ��޷�����Ӧ�ó�����ڴ����Ҫ�����׳������쳣��

2��Java���ô��ռ䲻�������ô��а�������ֽ���ͳ������أ�����ֽ�����غ����Ϣ����ʹ�Ŷ���ʵ���Ķ����ǲ�ͬ�ģ������JVM��ʵ�ֶ���������ô������������գ���ˣ�ֻҪ����صĹ���ͻ����������⡣һ���Ӧ�ó��򶼲�������������Ȼ��������Web������������������д�����JSP��JSP������ʱ����̬�ı����Java Servlet�࣬Ȼ����ص�����������ˣ�̫���JSP��Web���̿��ܲ�������쳣��

3������ԭ���Ǵ�����̫����̣߳����ܴ������߳����������Ƶģ������������쳣�ķ�����

4�����ڲ��л��߲�����������GC����ʱ�����������98%��ʱ��������GC���һ����˲���2%�Ķ��ڴ棬Ȼ���׳������쳣������ǰԤ�������������ڴ��С���Ӧ�ò�������������

���������쳣��OOM�й�ϵ�����ǣ���û�о��Թ�ϵ��

java.lang.StackOverflowError ��

java.net.SocketException: Too many open files

1����JVM���߳����ڵݹ���߷������ò��̫�࣬ռ�����̶߳�ջ�����µģ��̶߳�ջĬ�ϴ�СΪ1M��

2��������ϵͳ���ļ������ʹ���������Ƶģ���ĳ��Ӧ�ó���ʹ�õ��ļ����������������ƣ��ͻᵼ��������⡣

 

��������ԭ�򼰽���취


�����һ���� 
����java.lang.OutOfMemoryError: Java heap space��������java���ڴ治����һ��ԭ�����治������һ��ԭ���ǳ���������ѭ���� 
���������java���ڴ治���Ļ�������ͨ������JVM���������������� 
����< jvm-arg>-Xms3062m < / jvm-arg> 
����< jvm-arg>-Xmx3062m < / jvm-arg> 
���� 
��������� 
����java.lang.OutOfMemoryError: GC overhead limit exceeded 
���������͡���JDK6�����������ͣ���GCΪ�ͷź�С�ռ�ռ�ô���ʱ��ʱ�׳���һ������Ϊ��̫С�������쳣��ԭ��û���㹻���ڴ档 
����������������� 
����1���鿴ϵͳ�Ƿ���ʹ�ô��ڴ�Ĵ������ѭ���� 
����2��ͨ�����JVM���ã�������ʹ���ڴ棺 
����< jvm-arg>-XX:-UseGCOverheadLimit< /jvm-arg> 
���� 
����������� 
����java.lang.OutOfMemoryError: PermGen space��������P���ڴ治������ͨ������JVM�����ã� 
����< jvm-arg>-XX:MaxPermSize=128m< /jvm-arg> 
����< jvm-arg>-XXermSize=128m< /jvm-arg> 
������ע���� 
����JVM��Perm����Ҫ���ڴ��Class��Meta��Ϣ��,Class�ڱ�Loaderʱ�ͻᱻ�ŵ�PermGen space����������Ϊ���ϴ���GC�������������ڼ䲻�����������������Ĭ����64M��С����������Ҫ���صĶ���Ƚ϶�ʱ������64M�ͻᱨ�ⲿ���ڴ�����ˣ���Ҫ�Ӵ��ڴ���䣬һ��128m�㹻�� 
���� 
������ġ��� 
����java.lang.OutOfMemoryError: Direct buffer memory 
��������-XX:MaxDirectMemorySize= �����������JVM���ã� 
����< jvm-arg>-XX:MaxDirectMemorySize=128m< /jvm-arg> 
���� 
������塿�� 
����java.lang.OutOfMemoryError: unable to create new native thread 
������ԭ�򡿣�Stack�ռ䲻���Դ���������̣߳�Ҫô�Ǵ������̹߳��࣬Ҫô��Stack�ռ�ȷʵС�ˡ� 
�����������������JVMû���ṩ���������ܵ�stack�ռ��С�����������õ����߳�ջ�Ĵ�С����ϵͳ���û��ռ�һ����3G������Text/Data/BSS /MemoryMapping������֮�⣬Heap��Stack�ռ���������ޣ��Ǵ����˳��ġ��������������󣬿���ͨ������;������� 
����1.ͨ�� -Xss�����������ٵ����߳�ջ��С���������ܿ������̣߳���Ȼ����̫С��̫С�����StackOverflowError���� 
����2.ͨ��-Xms -Xmx ����������Heap��С�����ڴ��ø�Stack��ǰ���Ǳ�֤Heap�ռ乻�ã��� 
�ο���
JVM��֧�ֵ�����߳���
https://www.cnblogs.com/canacezhang/p/9318355.html
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
���� 
����������� 
����java.lang.StackOverflowError 
������ԭ�򡿣���Ҳ�ڴ���������һ�֣����߳�ջ�������Ҫô�Ƿ������ò�ι��ࣨ����������޵ݹ���ã���Ҫô���߳�ջ̫С�� 
��������������Ż�������ƣ����ٷ������ò�Σ�����-Xss���������߳�ջ��С
```

