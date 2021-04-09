## BlockingQueue学习

#### 什么是阻塞队列？

![image-20210203154502793](C:\Users\Administrator\Desktop\aaa.png)

线程1往阻塞队列中添加元素，而线程2从阻塞队列中移除元素。

当阻塞队列是空时，从队列中获取元素的操作将会被阻塞。

当阻塞队列是满时，往队列里添加元素的操作将会被阻塞。

#### 为什么用？有什么好处？

在多线程领域：所谓阻塞，在某些情况下会挂起线程（即阻塞），一旦条件满足，被挂起的线程又会自动唤醒。

为什么需要BlockingQueue

好处是我们不需要关心什么时候需要阻塞线程，什么时候需要唤醒线程，因为这一切BlockingQueue都给你一手包办了。

在concurrent包发布以前，在多线程环境下，我们每个程序员都必须去自己控制这些细节，尤其还要兼顾效率和线程安全，而这会给我们的程序带来不小的复杂度。

#### 常用类型

| 名称                  | 简述                                                      |
| --------------------- | --------------------------------------------------------- |
| ArrayBlockingQueue    | 由数组结构组成的有界阻塞队列                              |
| LinkedBlockingQueue   | 由链表结构组成的有界(大小默认为Integer.MAX_VALUE)阻塞队列 |
| PriorityBlockingQueue | 支持优先级排序的无界阻塞队列                              |
| DelayQueue            | 使用优先级队列实现的延迟无界阻塞队列                      |
| SynchronousQueue      | 不存储元素的阻塞队列，也即单个元素的队列                  |
| LinkedTransferQueue   | 由链表结构组成的无界阻塞队列                              |
| LinkedBlockingDeque   | 由链表结构组成的双向阻塞队列                              |

#### 核心方法

| 方法类型 | 抛出异常  | 特殊值   | 阻塞   | 超时               |
| -------- | --------- | -------- | ------ | ------------------ |
| 插入     | add(e)    | offer(e) | put(e) | offer(e,time,unit) |
| 移除     | remove()  | poll()   | take() | poll(time,unit)    |
| 检查     | element() | peek()   | 不可用 | 不可用             |

#### 七大类型的Demo用法

是我自己在学习的时候写的笔记，仅供参考。

```java
package com.lly.test;

import org.junit.jupiter.api.Test;
import java.util.concurrent.*;

public class BlockingQueueDemo {

    @Test
    public void test_ArrayBlockingQueue() {
        // 由数组结构组成的有界阻塞队列
        BlockingQueue<String> blockingQueue = new ArrayBlockingQueue<>(2);
        blockingQueue.add("11");
        blockingQueue.add("22");
        System.out.println("begin\t"+blockingQueue.toString());
        System.out.println(blockingQueue.remove());
        System.out.println("end\t"+blockingQueue.toString());
        System.out.println(blockingQueue.remove("11"));
        System.out.println(blockingQueue.element());
    }

    @Test
    public void test_LinkedBlockingQueue() {
        // 由链表结构组成的有界(大小默认为Integer.MAX_VALUE)阻塞队列
        BlockingQueue<String> blockingQueue = new LinkedBlockingQueue<>(2);
        blockingQueue.offer("11");
//        System.out.println(blockingQueue.offer("22"));
//        blockingQueue.poll();
//        System.out.println(blockingQueue.toString());
//        blockingQueue.poll();
        System.out.println(blockingQueue.peek());
    }

    @Test
    public void test_SynchronousQueue() {
        // 不存储元素的阻塞队列，也即单个元素的队列
        BlockingQueue<String> blockingQueue = new SynchronousQueue<>();
        new Thread(() -> {
            try {
                System.out.println(Thread.currentThread().getName());
                blockingQueue.put("11");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "A").start();

        new Thread(() -> {
            try {
                System.out.println(Thread.currentThread().getName()+"\t"+blockingQueue.take());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "B").start();
    }

    static class User implements Comparable<User> {
        String name;
        int age;

        public User(int age, String name) {
            this.age = age;
            this.name = name;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public int getAge() {
            return age;
        }

        public void setAge(int age) {
            this.age = age;
        }

        @Override
        public int compareTo(User o) {
            if (this.age > o.getAge()) {
                return 1;
            } else if (this.age < o.getAge()) {
                return -1;
            } else {
                return 0;
            }
        }

        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    ", age=" + age +
                    '}';
        }
    }

    @Test
    public void test_PriorityBlockingQueue() {
        // 支持优先级排序的无界阻塞队列
        PriorityBlockingQueue<User> blockingQueue = new PriorityBlockingQueue<>();
        blockingQueue.put(new User(20, "BB"));
        blockingQueue.put(new User(17, "CC"));
        blockingQueue.put(new User(25, "AA"));
        // 输出结果：[User{name='CC', age=17}, User{name='BB', age=20}, User{name='AA', age=25}]
        System.out.println(blockingQueue.toString());
    }

    @Test
    public void test_LinkedTransferQueue() {
        // 由链表结构组成的无界阻塞队列
        LinkedTransferQueue<String> linkedTransferQueue = new LinkedTransferQueue();

        Thread t2 = new Thread(() -> {
            try {
                System.out.println(linkedTransferQueue.take());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"t2");
        t2.start();

        Thread t1 = new Thread(() -> {
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(linkedTransferQueue.tryTransfer("aa"));
        },"t1");
        t1.start();

//        Thread t1 = new Thread(() -> {
//            try {
//                System.out.println(linkedTransferQueue.tryTransfer("aa",5,TimeUnit.SECONDS));
//            } catch (InterruptedException e) {
//                e.printStackTrace();
//            }
//        },"t1");
//        t1.start();
//
//        Thread t2 = new Thread(() -> {
//            try {
//                System.out.println(linkedTransferQueue.take());
//            } catch (InterruptedException e) {
//                e.printStackTrace();
//            }
//        },"t2");
//        t2.start();
    }

    @Test
    public void test_LinkedBlockingDeque() {
        // 由链表结构组成的双向阻塞队列
        LinkedBlockingDeque<String> blockingDeque = new LinkedBlockingDeque();
        blockingDeque.offer("11");
        blockingDeque.offer("22");
        System.out.println(blockingDeque.toString());
//
//        System.out.println(blockingDeque.getLast());
//        System.out.println();
//
//        System.out.println(blockingDeque.poll());
//
//        System.out.println();
//        System.out.println(blockingDeque.toString());
        blockingDeque.addLast("33");
        blockingDeque.addFirst("00");
        System.out.println(blockingDeque.toString());
        blockingDeque.removeLast();
        System.out.println(blockingDeque.toString());
    }

    static class DelayedElement implements Delayed {

        private final long delay; //这个就是等待时间 延迟时间
        private final long expire;  // 到期时间
        private final String msg;   //数据
        private final long now; // 创建时间

        public DelayedElement(long delay, String msg) {
            this.delay = delay;
            this.msg = msg;
            expire = System.currentTimeMillis() + delay;    //到期时间 = 当前时间+延迟时间
            now = System.currentTimeMillis();
        }

        @Override
        public String toString() {
            final StringBuilder sb = new StringBuilder("DelayedElement{");
            sb.append("delay=").append(delay);
            sb.append(", expire=").append(expire);
            sb.append(", msg='").append(msg).append('\'');
            sb.append(", now=").append(now);
            sb.append('}');
            return sb.toString();
        }

        /**
         * 需要实现的接口，获得延迟时间   用过期时间-当前时间
         * @param unit
         * @return
         */
        @Override
        public long getDelay(TimeUnit unit) {
//            System.out.println(System.currentTimeMillis());
            return unit.convert(this.expire - System.currentTimeMillis() , TimeUnit.MILLISECONDS);
        }

        /**
         * 用于延迟队列内部比较排序   当前时间的延迟时间 - 比较对象的延迟时间
         * @param o
         * @return
         */
        @Override
        public int compareTo(Delayed o) {
            return (int) (this.getDelay(TimeUnit.MILLISECONDS) -o.getDelay(TimeUnit.MILLISECONDS));
        }
    }

    @Test
    public void test_DelayQueue() throws InterruptedException {
        // 使用优先级队列实现的延迟无界阻塞队列
        DelayQueue<DelayedElement> delayQueue = new DelayQueue<>();
//        delayQueue.offer(new DelayedElement(5000, "11"));
//        delayQueue.offer(new DelayedElement(3000, "22"));
        delayQueue.offer(new DelayedElement(3000, "33"));
//        delayQueue.offer(new DelayedElement(10000, "00"));

        TimeUnit.SECONDS.sleep(2);
        System.out.println(delayQueue.toString());
//        System.out.println(delayQueue.poll());
        System.out.println(delayQueue.take());

    }

}

```

