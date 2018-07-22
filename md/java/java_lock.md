# synchronized与lock比较
## synchronized
3中作用方式

- 修饰普通方法，作用于当前实例加锁
- 修饰静态方法，作用于当前类对象加锁
- 修饰代码块，指定加锁对象，对给定对象加锁

加锁原理
```java
JVM通过该ACC_SYNCHRONIZED标志来辨别一个方法是否声明为同步方法，从而执行相应的同步调用

每个对象都存在着一个 monitor 与之关联，在Java虚拟机(HotSpot)中，monitor是由ObjectMonitor实现的
ObjectMonitor中有两个队列，_EntryList 和 _WaitSet

_EntryList: 当多个线程同时访问一段同步代码时，首先会进入 _EntryList 集合

_WaitSet: 等待被唤醒的集合
```

### Lock

ReentrantLock
```java
ReentrantLock实现了Lock接口，加锁和解锁都需要显式写出，注意一定要在适当时候unlock。
```

### 总结
```java
1.synchronized
　　优点：实现简单，语义清晰，便于JVM堆栈跟踪，加锁解锁过程由JVM自动控制，提供了多种优化方案，使用更广泛

　　缺点：悲观的排他锁，不能进行高级功能

2.lock
　　优点：可定时的、可轮询的与可中断的锁获取操作，提供了读写锁、公平锁和非公平锁　　

　　缺点：需手动释放锁unlock，不适合JVM进行堆栈跟踪

3.相同点　
　　都是可重入锁
```