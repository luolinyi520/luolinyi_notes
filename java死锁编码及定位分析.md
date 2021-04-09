## java死锁编码及定位分析

#### 什么是死锁

死锁是指两个或两个以上的线程在执行过程中，因争夺资源而造成的一种互相等待的现象，若无外力干涉那它们都将无法推进下去。如果系统资源充足，线程的资源请求都能够得到满足，死锁出现的可能性就很低，否则就会因争夺有限的资源而陷入死锁。

![image-20210209101939602](C:\Users\Administrator\Desktop\死锁\原理.png)

#### 自己写一个死锁

```java
import java.util.concurrent.TimeUnit;

class DeadLockThread implements Runnable {
    private String lockA;
    private String lockB;

    public DeadLockThread(String lockA, String lockB) {
        this.lockA = lockA;
        this.lockB = lockB;
    }

    @Override
    public void run() {
        synchronized (lockA) {
            System.out.println(Thread.currentThread().getName()+"\t 自己持有："+lockA+"\t尝试获得："+lockB);
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (lockB) {
                System.out.println(Thread.currentThread().getName()+"\t 成功拿到："+lockB);
            }
        }
    }
}

public class DeadLockTest {
    public static void main(String[] args) {
        String lockA = "lockA";
        String lockB = "lockB";
        new Thread(new DeadLockThread(lockA, lockB), "t1").start();
        new Thread(new DeadLockThread(lockB, lockA), "t2").start();

        /**
         * linux  ps -ef|grep java  ls -l
         * windows下的java运行程序 也有类似ps的查看进程的命令，但是目前我们需要查看的只是java
         *        jps = java ps     jps -l
         */
    }
}
```

#### 定位分析

1.通过 jps -l 查看运行的java进程ID

![image-20210209104120143](C:\Users\Administrator\Desktop\死锁\jps.png)

2.使用jstack XXX(PID)打印堆栈信息，从而发现会有死锁的日志，产生了互相等待的现象

![image-20210209104446070](C:\Users\Administrator\Desktop\死锁\jstack.png)