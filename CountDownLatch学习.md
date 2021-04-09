## CountDownLatch学习

#### 理论

CountDownLatch主要有2个方法，当一个或多个线程调用await方法时，调用线程会被阻塞。

其它线程调用countDown方法会将计数器减1(调用countDown方法的线程不会阻塞)，当计数器的值变为0时，因调用await方法被阻塞的线程会被唤醒，继续执行。

#### 实践

以生活中的case，进行举例。

5个同学上完晚自习，班长最后锁门。

代码如下：

```java
public class CountDownLatchDemo {

    public static void main(String[] args) {
        int num = 5;
        CountDownLatch countDownLatch = new CountDownLatch(num);
        for (int i = 1; i <= num; i++) {
            int tmp = i;
            new Thread(() -> {
                System.out.println(""+ tmp+"上完晚自习，离开教室");
                countDownLatch.countDown();
            }, ""+i).start();
        }
        try {
            countDownLatch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("班长最后锁门");
    }
}
```

输出结果：

1上完晚自习，离开教室
2上完晚自习，离开教室
3上完晚自习，离开教室
4上完晚自习，离开教室
5上完晚自习，离开教室
班长最后锁门