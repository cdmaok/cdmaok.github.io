---
layout: post
title: Java Multi-thread
category: Java
description: Java 多线程小结
---

### ConcurrentHashMap
  * hashMap 不是线程安全的，其背后实现是一个链表，当多线程拿到同一个指针时，有一个写入的对象会被覆盖掉
  * 与之对应线程安全的HashTable 效率底下，只有一把锁，影响了性能
  * 当数据很大时，一把锁显然是不合适，一种解决方案是将背后的链表分成多个区间，每个区间有一个写锁，当多个线程写不同的数据段的时候，不发生写冲突
  * 其他collection的线程安全类可见concurrent系列

### 线程池
```Java
new ThreadPoolExeecutor(corePoolSize,maximumPoolSize,keepAliveTime,runnableTaskQueue,handler)
//runnableTaskQueue: 任务队列，包括ArrayBlockingQueue,LinkedBlockingQueue等基于不同的数据结构
// 如果线程耗 CPU，大小配置为 CPU_core + 1
// 如果线程耗 IO， 大小配置为 CPU_core * 2
// CPU_core = Runtime.getRuntime().availableProcessors()
// 提交进程(无返回)
threadPool.execute(new Runnable()){
  @override
  public void run(){
    //...
  }
}
//提交进程（有返回）
Future<Object> future = threadPool.submmit(hasReturnValueTask);
try{
  Object o = future.get();
}catch(Exception e){

}
```

### 阻塞队列(BlockingQueue)
生产者-消费者是典型的应用场景，如果队列已满时，生产者无法插入，线程一直被阻塞；队列为空时，消费者线程一直被阻塞,简单介绍各种方法  

方法 | 抛出异常 | 返回特殊值 | 一直阻塞 | 超时退出  
---  | -------
插入 | add(e) | offer(e) | put(e) | offer(e,time,unit)
移除 | remove() | poll() | take() | poll(time,unit)
检查 | element() | peek() | ---- | -----

### Fork/Join 框架
把一个大任务分割成若干个小任务，然后最终汇总每个小任务结果得到最终结果的框架。例如计算1+2+3+4，设置两个线程，分别计算1+2,3+4的值，然后汇总两个线程的计算结果。

```Java
package test;  

import java.util.concurrent.ExecutionException;  
import java.util.concurrent.ForkJoinPool;  
import java.util.concurrent.Future;  
import java.util.concurrent.RecursiveTask;  

/*
 * this is to sum(1,2,3,...,100) by fork/join framework
 */
public class Sum extends RecursiveTask<Integer>{  
  //可递归任务  
	private static final int threshold = 2;  
	private int start;  
	private int end;  

	public Sum(int start, int end) {  
		this.start = start;  
		this.end = end;  
	}  

	@Override  
	protected Integer compute() {  
		int sum = 0;  
		boolean canCompute = (end - start) <= threshold;  
		if (canCompute) {  
			for (int i = start; i <= end; i++) {  
				sum += i;  
			}  
		}else {  
			// split into small tasks  
			int middle = ( start + end ) / 2;  
			Sum leftsum = new Sum(start, middle);  
			Sum rightsum = new Sum(middle + 1, end);  
			leftsum.fork();  
			rightsum.fork();  
			sum = leftsum.compute() + rightsum.compute();  
		}  
		return sum;  
	}  

	public static void main(String[] args) throws InterruptedException, ExecutionException {  
    // 建立进程池，然后提交这个任务。  
    ForkJoinPool fjp = new ForkJoinPool();  
		Sum sum = new Sum(1, 100);  
    // 同样，如果用fjp.execute(task)也可以，只是没有返回值。  
		Future<Integer> result = fjp.submit(sum);  
		System.out.println(result.get());  

	}  

}  

```

Note：来自InfoQ -- 《java并发编程的艺术》迷你书
