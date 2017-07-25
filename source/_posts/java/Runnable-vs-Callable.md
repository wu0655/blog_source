---
title: 说说Runnable与Callable(转载)
date: 2017-07-25 10:00:52
categories: java
tags:
  - reproductiond
  - concurrent
---

本文基于下文的修改：
[说说Runnable与Callable](http://www.cnblogs.com/frinder6/p/5507082.html)

Callable接口：
```java
public interface Callable {
  V call() throws Exception;
}
```
Runnable接口：
```java
public interface Runnable {
    public abstract void run();
}
```

- 相同点：
  1. 两者都是接口；（废话）
  2. 两者都可用来编写多线程程序；
  3. 两者都需要调用Thread.start()启动线程；
- 不同点：
  1. 两者最大的不同点是：实现Callable接口的任务线程能返回执行结果；而实现Runnable接口的任务线程不能返回结果；
  2. Callable接口的call()方法允许抛出异常；而Runnable接口的run()方法的异常只能在内部消化，不能继续上抛；
- 注意点：
  - Callable接口支持返回执行结果，此时需要调用FutureTask.get()方法实现，此方法会阻塞主线程直到获取‘将来’结果；
  - 当不调用此方法时，主线程不会阻塞！

Callable工作的Demo：
```java
package com.callable.runnable;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

/**
 * Created on 2016/5/18.
 */
public class CallableImpl implements Callable<String> {

    public CallableImpl(String acceptStr) {
        this.acceptStr = acceptStr;
    }

    private String acceptStr;

    @Override
    public String call() throws Exception {
        // 任务阻塞 1 秒
        Thread.sleep(1000);
        return this.acceptStr + " append some chars and return it!";
    }


    public static void main(String[] args) throws ExecutionException, InterruptedException {
        Callable<String> callable = new CallableImpl("my callable test!");
        FutureTask<String> task = new FutureTask<>(callable);
        long beginTime = System.currentTimeMillis();
        // 创建线程
        new Thread(task).start();
        // 调用get()阻塞主线程，反之，线程不会阻塞
        String result = task.get();
        long endTime = System.currentTimeMillis();
        System.out.println("hello : " + result);
        System.out.println("cast : " + (endTime - beginTime) / 1000 + " second!");
    }
}
```


测试结果：
```
hello : my callable test! append some chars and return it!
cast : 1 second!

Process finished with exit code 0
```

Runnable工作的Demo：
```java
package com.callable.runnable;

/**
 * Created on 2016/5/18.
 */
public class RunnableImpl implements Runnable {

    public RunnableImpl(String acceptStr) {
        this.acceptStr = acceptStr;
    }

    private String acceptStr;

    @Override
    public void run() {
        try {
            // 线程阻塞 1 秒，此时有异常产生，只能在方法内部消化，无法上抛
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        // 最终处理结果无法返回
        System.out.println("hello : " + this.acceptStr);
    }


    public static void main(String[] args) {
        Runnable runnable = new RunnableImpl("my runable test!");
        long beginTime = System.currentTimeMillis();
        new Thread(runnable).start();
        long endTime = System.currentTimeMillis();
        System.out.println("cast : " + (endTime - beginTime) / 1000 + " second!");
    }
}
```

测试结果：
```
cast : 0 second!
hello : my runable test!

Process finished with exit code 0
```

写此篇的原因是一次面试中问到Callable与Runnable的区别，当时用的多的是Runnable，而Callable使用很少！
比较了两者后（网上查了不少），发现Callable在很多特殊的场景下还是很有用的！最后留点抄的代码，加深对Callable的认识！
