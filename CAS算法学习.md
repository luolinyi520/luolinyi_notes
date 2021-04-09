## CAS算法学习

#### 概念

CAS就是compareAndSwap的缩写，一句话描述：比较并交换。是一个轻量级的同步机制.

CAS算法涉及到3个操作数:

- 需要读写的内存值V
- 进行比较的值A
- 需要更新的值B

#### 怎么使用

以AtomicInteger类进行说明

```java
public class CASDemo {

    public static void main(String[] args) {
        // 初始值为1
        AtomicInteger atomicInteger = new AtomicInteger(1);
        // 期望值是1，更新值为2，返回true代表更新成功
        System.out.println(atomicInteger.compareAndSet(1, 2));
        // 期望值是1，更新值为3，返回false代表更新失败（最新值已经是2）
        System.out.println(atomicInteger.compareAndSet(1, 3));
    }

}
```

#### 有什么作用

可以不加锁达到保证数据的原子性，相对于synchronized关键字来说的话，它是轻量级的。

从一个简单的示例来进行说明：

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicInteger;

public class CASDemo {

    static AtomicInteger atomicInteger = new AtomicInteger(0);

    static volatile int count = 0;

    /**
     * 不需要加锁，就能保证原子性
     */
    public static void addPlusPlusAtomic() {
        atomicInteger.getAndIncrement();
    }

    /**
     * 需要加synchronized+volatile关键字才能保证原子性
     */
    public static synchronized void addPlusPlus() {
        count++;
    }

    public static void main(String[] args) throws InterruptedException {
        int num = 200;
        ExecutorService executor = Executors.newFixedThreadPool(num);

        // 定义200个任务，每个任务执行1000次
        for (int i = 0; i < num; i++) {
            executor.submit(() -> {
                System.out.println(Thread.currentThread().getName());
                for (int j = 0; j < 1000; j++) {
                    addPlusPlus();
                    addPlusPlusAtomic();
                }
            });
        }

        executor.shutdown();
        // 等待子线程执行完成
        executor.awaitTermination(Long.MAX_VALUE, TimeUnit.HOURS);
        // 输出计算结果
        System.out.println("atomicInteger="+atomicInteger.get());
        System.out.println("count="+count);
    }

}
```

#### 底层原理

通过JDK自带的Unsafe类进行实现的，调用的是native方法，从而达到原子性。

这个类可以直接操作物理内存的数据，相当于C语言的一个指针。

CAS是一条CPU的原子指令（cmpxchg指令），不会造成所谓的数据不一致问题，Unsafe提供的CAS方法（如compareAndSwapXXX）底层实现即为CPU指令cmpxchg。

以AtomicInteger类的getAndIncrement()方法进行说明：

``` java
	private static final Unsafe unsafe = Unsafe.getUnsafe();
    /**
     * value属性的内存偏移量（内存地址）
     */
    private static final long valueOffset;

    static {
        try {
            // 拿到value属性的内存地址
            valueOffset = unsafe.objectFieldOffset
                    (AtomicInteger.class.getDeclaredField("value"));
        } catch (Exception ex) { throw new Error(ex); }
    }

    /**
     * 当前值
     */
    private volatile int value;

    public final int getAndIncrement() {
        return unsafe.getAndAddInt(this, valueOffset, 1);
    }

    /**
     * CAS自旋
     * @param var1 当前对象
     * @param var2 value属性的内存偏移量（内存地址）
     * @param var4 1
     * @return
     */
    public final int getAndAddInt(Object var1, long var2, int var4) {
        // 当前value属性的真实值
        int var5;
        do {
            var5 = this.getIntVolatile(var1, var2);
        } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4)/* 如果期望值与真实值一样，就加一，否则继续循环判断，直到更新成功为止 */);

        return var5;
    }
```

PS：里面的注释是我自己加的，便于理解。