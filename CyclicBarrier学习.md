## CyclicBarrier学习

#### 理论

CyclicBarrier的字面意思是可循环（Cyclic）使用的屏障（Barrier）。它要做的事情是，让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活，线程进入屏障通过CyclicBarrier的await()方法。

#### 实践

举例：

集齐7颗龙珠才能召唤神龙

```java
public class CyclicBarrierDemo {

    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7, () -> {
            System.out.println("****召唤神龙****");
        });
        for (int i = 1; i <= 7; i++) {
            int tmp = i;
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName()+"\t收集到第"+ tmp +"颗龙珠");

                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }, ""+i).start();
        }
    }

}
```

输出结果：

1	收集到第1颗龙珠
2	收集到第2颗龙珠
3	收集到第3颗龙珠
4	收集到第4颗龙珠
6	收集到第6颗龙珠
5	收集到第5颗龙珠
7	收集到第7颗龙珠
****召唤神龙****