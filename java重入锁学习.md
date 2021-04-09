## java重入锁学习

1. #### 什么是重入锁

   同一个线程能够多次获得同一把锁，不需要等待锁的释放。（比如自己花自己的钱，不用给自己写借条一样的道理）

2. #### 通过synchronized关键字实现

   ```java
   public class LockDemo1 {
   
       static Object objectLock = new Object();
   
       public static void main(String[] args) {
           new Thread(() -> {
               synchronized (objectLock) {
                   System.out.println(Thread.currentThread().getName()+"\t外层");
                   synchronized (objectLock) {
                       System.out.println(Thread.currentThread().getName()+"\t中层");
                       synchronized (objectLock) {
                           System.out.println(Thread.currentThread().getName()+"\t内层");
                       }
                   }
               }
           }, "t1").start();
       }
   
   }
   ```

   总结：

   ​	synchronized是隐士锁，由JVM自动控制的，在生成class文件的时候，会加上monitorenter、monitorexit指令，加锁、释放锁就是通过这2个指令来控制的。

3. #### 通过Lock实现

   ```java
   public class LockDemo2 {
       static Lock lock = new ReentrantLock();
   
       public static void main(String[] args) {
           new Thread(() -> {
               lock.lock();
               try {
                   System.out.println("外层");
                   lock.lock();
                   try {
                       System.out.println("中层");
                       lock.lock();
                       try {
                           System.out.println("内层");
                       } finally {
                           lock.unlock();
                       }
                   } finally {
                       lock.unlock();
   //                    try {
   //                        TimeUnit.SECONDS.sleep(3);
   //                    } catch (InterruptedException e) {
   //                        e.printStackTrace();
   //                    }
                   }
               } finally {
                   lock.unlock();
               }
           }, "t1").start();
   
   //        new Thread(() -> {
   //            lock.lock();
   //            System.out.println("test");
   //            lock.unlock();
   //        }, "t2").start();
       }
   
   }
   ```

   总结：

   ​	Lock接口是显示锁，需要调用lock()方法加锁、unlock()方法解锁，必须成对出现，不然后面加锁是会一直等待锁的释放的。把上面的代码注释打开，你会发现输出的test会在程序最后输出。

4. #### 通过LockSupport实现

   待补充。。。

   