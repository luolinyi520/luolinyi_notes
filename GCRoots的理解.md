## 通过Callable接口实现多线程

```java
import java.util.concurrent.Callable;
import java.util.concurrent.FutureTask;

class MyThread implements Callable<Integer> {

    @Override
    public Integer call() throws Exception {
        System.out.println(Thread.currentThread().getName()+"\t"+"come in");
        return 1024;
    }
}

public class CallableTest {

    public static void main(String[] args) throws Exception {
        FutureTask<Integer> futureTask = new FutureTask<>(new MyThread());
        Thread thread = new Thread(futureTask, "T1");
        thread.start();

        Integer s1 = 100;
        Integer s2 = futureTask.get();
        System.out.println("result="+(s1+s2));
    }

}
```

输出结果：

T1	come in
result=1124