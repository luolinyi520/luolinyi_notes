## Spring5之AOP学习

#### 概念

AOP是Aspect Oriented Programming的缩写，意为：面向切面编程。是spring框架的一个重要内容，通过它可以减少代码的耦合度。

#### 通知（增强）类型

总共有5种类型的通知，也有人叫增强，spring是通过动态代理在运行期间增强的，但是advice英文翻译为通知的意思，所以网上出现了2种概念。

具体看下表：

| 类型            | 描述       |
| --------------- | ---------- |
| @Before         | 前置通知   |
| @AfterReturning | 返回后通知 |
| @After          | 后置通知   |
| @Around         | 环绕通知   |
| @AfterThrowing  | 异常通知   |

#### 实操Demo

##### 1.定义一个service类

```java
package com.lly.demo.service;

import org.springframework.stereotype.Service;

@Service
public class AopService {

    public int div(int a, int b) {
        return a / b;
    }

}
```

##### 2.定义一个切面

通过@Aspect来定义一个切面，真正在项目中进行定义的话，请遵守单一原则，也就是一个类只定一个通知类型，这里是为了demo演示，就全部写了。

```java
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class MyAspect {

    /**
     * 前置通知
     */
    @Before("execution(* com.lly.demo.service.AopService.*(..))")
    public void before(JoinPoint joinPoint) {
//        System.out.println("方法执行前，打印入参：" + Arrays.toString(joinPoint.getArgs()));
        System.out.println("before...前置通知");
    }

    /**
     * 返回后通知
     */
    @AfterReturning("execution(* com.lly.demo.service.AopService.*(..))")
    public void afterReturning() {
        System.out.println("afterReturning...返回后通知");
    }

    /**
     * 后置通知
     */
    @After("execution(* com.lly.demo.service.AopService.*(..))")
    public void after() {
        System.out.println("after...后置通知");
    }

    /**
     * 环绕通知
     */
    @Around("execution(* com.lly.demo.service.AopService.*(..))")
    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("环绕通知之前...");
        Object retValue = proceedingJoinPoint.proceed();
        System.out.println("环绕通知之后...");
        return retValue;
    }

    /**
     * 异常通知
     */
    @AfterThrowing("execution(* com.lly.demo.service.AopService.*(..))")
    public void afterThrowing() {
        System.out.println("afterThrowing...异常通知");
    }

}
```

PS：

如果@Aspect提示不存在，请添加aspectj依赖，springboot默认是不会加这个依赖的。

```xml
<dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.6</version>
        </dependency>
```

##### 3.运行结果

正常运行打印结果：

调用开始
环绕通知之前...
before...前置通知
afterReturning...返回后通知
after...后置通知
环绕通知之后...
调用结束

![image-20210125101545883](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210125101545883.png)

异常运行打印结果：

调用开始
环绕通知之前...
before...前置通知
afterThrowing...异常通知
after...后置通知

![image-20210125101810253](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210125101810253.png)

#### 

