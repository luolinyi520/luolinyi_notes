## ArrayList线程安全问题解决

#### 问题现象

多个线程操作同一个list会出现并发修改异常（java.util.ConcurrentModificationException）

测试代码如下：

```java
public static void main(String[] args) {
    List<String> list = new ArrayList<>();
    for (int i = 0; i < 30; i++) {
        new Thread(() -> {
            list.add(UUID.randomUUID().toString());
            System.out.println(list);
        }).start();
    }
}
```

输出结果：

Exception in thread "Thread-28" java.util.ConcurrentModificationException

#### 解决方案

1. Vector

   用Vector类来实现，它的add()方法加了synchronized关键字修饰，所以能保证线程安全。

   ```java
   public static void main(String[] args) {
       List<String> list = new Vector<>();
       for (int i = 0; i < 30; i++) {
           new Thread(() -> {
               list.add(UUID.randomUUID().toString());
               System.out.println(list);
           }).start();
       }
   }
   ```

2. Collections.synchronizedList()方法

   利用集合辅助类来创建一个线程安全的集合，这个就是相当于在外面套了一层，使其保证线程安全。

   ```java
   public static void main(String[] args) {
       List<String> list = Collections.synchronizedList(new ArrayList<>());
       for (int i = 0; i < 30; i++) {
           new Thread(() -> {
               list.add(UUID.randomUUID().toString());
               System.out.println(list);
           }).start();
       }
   }
   ```

3. CopyOnWriteArrayList（推荐使用这个类）

   这个类是JUC包下面的一个类，叫写时复制，能够做到读写分离，保证写的线程安全且支持并发读。

   ```java
   public static void main(String[] args) {
       List<String> list = new CopyOnWriteArrayList<>();
       for (int i = 0; i < 30; i++) {
           new Thread(() -> {
               list.add(UUID.randomUUID().toString());
               System.out.println(list);
           }).start();
       }
   }
   ```

   CopyOnWriteArrayList.add()方法源码讲解：

   ```java
   public boolean add(E e) {
       // 通过定义一个重入锁进行控制
       final ReentrantLock lock = this.lock;
       // 加锁
       lock.lock();
       try {
           // 当前list
           Object[] elements = getArray();
           // 当前list的长度
           int len = elements.length;
           // 复制一个新的list且在原来的长度+1
           Object[] newElements = Arrays.copyOf(elements, len + 1);
           // 把新加的元素添加到新的list上
           newElements[len] = e;
           // 写完之后重新设置回当前list
           setArray(newElements);
           return true;
       } finally {
           // 解锁
           lock.unlock();
       }
   }
   ```

   

   



