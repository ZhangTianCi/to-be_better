# 引用的类型

1. 强
   存在对象引用。x=null：打断引用

2. 软 SoftReference
   系统内存不够的情况下，会被回收。
   在系统将要发生内存溢出异常之前，将会把这些对象列进回收范围进行第二次回收。

3. 弱 WeakReference

   ![弱引用](.\images\弱引用.png)只要遇到GC，就会被回收。
   如果有另外一个强引用指向他的时候，如果强引用消失，就不用管了。
   一般用在容器中
   <font color="red">WeakHashMap</font>
   <font color="red">AQS的unlock</font>

4. 虚 堆外内存
   回收：C/C++ delete free等命令java unsafe

***** System.GC()是fullGC

