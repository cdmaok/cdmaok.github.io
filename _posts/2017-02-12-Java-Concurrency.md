---
layout: post
title: Java Concurrency
category: Java
description: Java multi-thread programming
---

Note from Java Concurrency in Practice

```Java
@NotThreadSafe
public class UnsafeSequence{
  private int value;

  /*返回一个序列值*/
  public int getNext(){
    return value++;
  }
}
// 同步代码块和原子变量不应该同时使用
@ThreadSafe
public class Sequence{
  private int value;

  /* return a value*/
  public synchronized int getNext(){
    return value++;
  }
}

@ThreadSafe
public class Sequence{
  private AtomicLong value;
  public AtomicLong getNext(){
    return value++
  }
}

```

volatile变量是一种比synchronized轻量级的同步机制，更重要的是它的可见性，比使用锁的代码更加脆弱，也更加难以理解，并不能保证原子性（读->改->写），可以作为标志变量，无法作为计数器。

线程封闭->避免同步
1. 局部变量
2. ThreadLocal标志
3. 不可变对象 (1.创建之后不能修改；2.所有域为final类型；3.正确创建)
4. 安全发布 (1.静态初始化中初始化，不适用构造函数；2. 引用保存到volatile/AtomicRef; 3. 保存到final 域；4. 保存到锁保护的域中，容器内置锁)

设计线程安全的类
```Java
public final class Counter{
  public synchronized ..(){..}
  ..
  public synchronized ..(){..}
}
```

##并发容器
1. ConcurrentHashMap vs Collections.synchronized.Map 前者是分段锁（基于散列的锁），但size和IsEmpty之类的操作都不是精确值
2. CopyOnWriteArrayList --> synchronizedList 修改时重新复制list，保留原引用
3. 阻塞队列
   BlockingQueue -> (Consumer,Producer)  put/offer
   LinkedList -> LinkedBlockingQueue
   Arraylist -> ArrayBlockingQueue
   PriorityBlockingQueue/SynchronousQueue
4. 容器可以视为内置锁，没有容器的话请用内部工具类
内部工具类 (闭锁 CountdownLatch 初始化等待事件/ FutureTask get方法，提前启动计算 / 信号量 控制数量/ 栅栏 等待线程)

显式工具补充

构造缓存 example
```Java
1. HashMap + 同步机制  --> 同步机制很重，很多东西被阻塞
2. ConcurrentHashMap 轻量级 但有可能因为机器的时序做不到被重复核算
3. FutureTask 还是有可能存在上述的问题
4. FutureTask + 原子操作
```

线程池，通过重用现有的现成而不是创建现成，可以在处理多个请求时分摊在现成创建和销毁中的产生的巨大开销。

实现Executor的线程池 exec(Runnable)/submit(Runnable)
newFixedThreadPool 固定长度（数量）
newCachedThreadPool 供>求 回收，供<求 继续分配
newSingleThreadPool 单线程轮换请求
newScheduledPool 定时执行

## 中断
日志服务example
```Java
// BlockingQueue 生产-消费模型会丢失日志

public class LogService{
  private final BlockingQueue<String> queue;
  private final LoggerThread loggerThread;
  private final PrintWriter printWriter;
  private boolean isShutDown;
  private int resveration;

  public void start(){loggerThread.start()}

  public void stop(){
    synchronized(this){
      isShutDown = true;
      loggerThread.interrupt();
    }
  }

  public void log(String msg) throws InterruptedException{
    synchronized(this){
      if(isShutDown){
        throw new IllegalStateException;
      }
      ++ resveration;
    }
    queue.put(msg);
  }

  private class LoggerThread extends Thread{
    public void run(){
      try{
        while(true){
          try{
            synchronized(LogService.this){
              if(isShutDown && resveration == 0){
                break;
              }
              String msg = queue.take();
              synchronized(LogService.this){--resveration;}
              writer.println(msg);
            }catch (InterruptedException e){
              // retry
            }
          }
        }
      }finally{
        writer.close();
      }
    }
  }
}
```

## 评估
1. 服务时间/延迟时间/吞吐量/效率/可伸缩性/容量
2. Tool vmstat from Linux / perform from window
'''
垃圾回收的执行时序是无法预测的，因此在执行测试时，垃圾回收器可能在任何时刻运行。如果测试程序执行了N次迭代都没有触发垃圾回收操作，但在第N+1次迭代时触发了垃圾回收操作，那么即使运行次数相差不大，仍可能在最终测试的每次迭代时间上带来很大的影响。
有两种策略可以防止垃圾回收操作对测试结果产生影响。第一种策略是确保垃圾回收操作在测试运行的整个期间都不会执行（在调用JVM时指定-verbose：gc来判断是否执行了垃圾回收操作），第二种是确保垃圾回收操作在测试期间执行多次，这样测试程序就能反映运行期间的内存分配和垃圾回收等开销。
'''

Lock ReentrantLock
内置锁无法中断一个等待获取锁的现成
高级内置锁
定时锁等待/可中断锁等待/非结构块加锁
读写锁

```Java
//同步实现
@ThreadSafe
public class BoundedBuffer<V> extends BaseBoundedBuffer<V>{

  public BoundedBuffer(int size){super(size);}

  public synchronized void put(V v) throws InterruptedException{
    while(isFull()){
      wait(); //通知线程挂起
    doPut(v);
    notifyAll(); //唤醒线程
    }
  }

  public synchronized V take() throws InterruptedException{
    while(isEmpty()){
      wait();
    }
    V v = doTake();
    notifyAll();
    return v;
  }

}
```
