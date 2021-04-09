## Semaphore学习

#### 理论

信号量主要用于两个目的，一个是用于多个共享资源的互斥使用，另一个用于并发线程数的控制。

#### 实践

举例：

抢车位，6辆车抢3个车位

```java
import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;

public class SemaphoreDemo {
    public static void main(String[] args) {
        // 模拟3个停车位
        Semaphore semaphore = new Semaphore(3);
        // 模拟6部汽车
        for (int i = 1; i <= 6; i++) {
            new Thread(() -> {
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+"\t抢到车位");
                    TimeUnit.SECONDS.sleep(3);
                    System.out.println(Thread.currentThread().getName()+"\t停车3秒后离开车位");
                } catch (Exception e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();
                }
            }, ""+i).start();
        }
    }
}
```

输出结果：

1	抢到车位
3	抢到车位
2	抢到车位
3	停车3秒后离开车位
1	停车3秒后离开车位
2	停车3秒后离开车位
6	抢到车位
5	抢到车位
4	抢到车位
4	停车3秒后离开车位
6	停车3秒后离开车位
5	停车3秒后离开车位