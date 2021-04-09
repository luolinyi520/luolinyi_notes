## BlockingQueue之常用类型Demo

#### 七大类型的Demo用法

是我自己在学习的时候写的笔记，仅供参考。

代码如下：

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

