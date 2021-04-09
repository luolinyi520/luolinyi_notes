## java线程池详解

#### 七大参数

![image-20210208115124114](C:\Users\Administrator\Desktop\zzzz.png)

#### 四种拒绝策略

| 类型                | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| AbortPolicy         | 直接抛出RejectedException异常阻止系统正常运行                |
| CallerRunsPolicy    | ”调用者运行”一种调节机制，该策略既不会抛弃任务，也不会抛出异常，而是将某些任务回退到调用者，从而降低新任务的流量。 |
| DiscardOldestPolicy | 抛弃队列中等待最久对的任务，然后把当前任务加入队列中尝试再次提交当前任务。 |
| DiscardPolicy       | 直接丢弃任务，不予任何处理也不抛出异常，如果允许任务丢失，这是最好的一种方案。 |

AbortPolicy演示：

```java
 	@Test
    public void testAbortPolicy() {
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(2,
                5,
                1L,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<>(3),
                Executors.defaultThreadFactory(),
                // 直接抛异常                                                       
                new ThreadPoolExecutor.AbortPolicy());
        for (int i = 1; i <= 9; i++) {
            threadPoolExecutor.execute(() -> {
                System.out.println(Thread.currentThread().getName());
            });
        }
    }
```

输出内容：

pool-1-thread-1
pool-1-thread-1
pool-1-thread-2
pool-1-thread-1
pool-1-thread-2
pool-1-thread-3
pool-1-thread-4
pool-1-thread-5


java.util.concurrent.RejectedExecutionException: Task com.lly.test.ThreadPoolTest$$Lambda$253/1181869371@7cbd213e 

------

CallerRunsPolicy演示：

```java
	@Test
    public void testCallerRunsPolicy() {
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(2,
                5,
                1L,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<>(3),
                Executors.defaultThreadFactory(),
                // 多余的任务，交给main线程处理                                                     
                new ThreadPoolExecutor.CallerRunsPolicy());
        for (int i = 1; i <= 10; i++) {
            threadPoolExecutor.execute(() -> {
                System.out.println(Thread.currentThread().getName());
            });
        }
    }
```

输出内容：

main
main
pool-1-thread-1
pool-1-thread-1
pool-1-thread-1
pool-1-thread-1
pool-1-thread-2
pool-1-thread-3
pool-1-thread-4
pool-1-thread-5

------

DiscardOldestPolicy演示：

```java
	@Test
    public void testDiscardOldestPolicy() {
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(2,
                5,
                1L,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<>(3),
                Executors.defaultThreadFactory(),
                // 丢弃等待最久的任务
                new ThreadPoolExecutor.DiscardOldestPolicy());
        for (int i = 1; i <= 10; i++) {
            threadPoolExecutor.execute(() -> {
                System.out.println(Thread.currentThread().getName());
            });
        }
    }
```

输出内容：

pool-1-thread-1
pool-1-thread-1
pool-1-thread-1
pool-1-thread-1
pool-1-thread-2
pool-1-thread-3
pool-1-thread-4
pool-1-thread-5

------

DiscardPolicy演示：

```java
    @Test
    public void testDiscardPolicy() {
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(2,
                5,
                1L,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<>(3),
                Executors.defaultThreadFactory(),
                // 直接丢弃任务
                new ThreadPoolExecutor.DiscardPolicy());
        for (int i = 1; i <= 10; i++) {
            threadPoolExecutor.execute(() -> {
                System.out.println(Thread.currentThread().getName());
            });
        }
    }
```

输出内容：

pool-1-thread-1
pool-1-thread-1
pool-1-thread-1
pool-1-thread-1
pool-1-thread-2
pool-1-thread-3
pool-1-thread-4
pool-1-thread-5

#### 工作原理

![image-20210208115444573](C:\Users\Administrator\Desktop\sss.png)

1. 在创建了线程池后，等待提交过来的任务请求。

2. 当调用execute()方法添加一个请求任务时，线程池会做如下判断：

   2.1 如果正在运行的线程数量小于corePoolSize，那么马上创建线程运行这个任务；

   2.2 如果正在运行的线程数量大于或等于corePoolSize，那么将这个任务放入队列；

   2.3 如果这时候队列满了且正在运行的线程数量还小于maximumPoolSize，那么还是要创建非核心线程立刻运行这个任务；

   2.4 如果队列满了且正在运行的线程数量大于maximumPoolSize，那么线程池会启动饱和拒绝策略来执行；

3. 当一个线程完成任务时，它会从队列中取下一个任务来执行。

4. 当一个线程无事可做超过一定的时间（KeepAliveTime）时，线程池会判断：

   如果当前运行的线程数大于corePoolSize，那么这个线程就会被停掉。

   所以线程池的所有任务完成后它最终会收缩到corePoolSize的大小。

#### 创建方式

不推荐用JDK自带的辅助类Executors进行创建线程池，而要自定义线程池，原因是通过Executors类创建的线程池，等待队列的默认值是Integer.MAX_VALUE，在高并发场景下，会导致OOM。出自于阿里编码规范中有严格进行说明的。

```java
		// 不推荐的线程池创建方式
        ExecutorService executorService = Executors.newFixedThreadPool(5);
        // 推荐的创建方式
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(2, 
                5,
                1L, 
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<Runnable>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy());
```

#### 配置合理的线程数

从这两方面进行考虑：

- CPU密集型

  CPU密集的意思是该任务需要大量的运算，而没有阻塞，CPU一直全速运行。

  CPU密集任务只有在真正的多核CPU上才可能得到加速（通过多线程），而在单核CPU上，无论你开几个模拟的多线程该任务都不可能得到加速，因为CPU总的运算能力就那些。

  CPU密集型任务配置尽可能少的线程数量：

  一般公式：CPU核数+1个线程的线程池

- IO密集型

  第一种：

  由于IO密集型任务线程并不是一直在执行任务，则应配置尽可能多的线程，如CPU核数*2

  第二种：

  IO密集型，即该任务需要大量的IO，即大量的阻塞。

  在单线程上运行IO密集型的任务会导致浪费大量的CPU运算能力浪费在等待。

  所以在IO密集型任务中使用多线程可以大大的加速程序运行，即使在单核CPU上，这种加速主要就是利用了被浪费掉的阻塞时间。

  IO密集型时，大部分线程都阻塞，故需要多配置线程数：

  参考公式：CPU核数/1-阻塞系数			阻塞系数在0.8~0.9之间

  比如8核CPU： 8 / 1 - 0.9 = 80个线程数

  

  