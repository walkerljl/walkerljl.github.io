---
layout : post
title : Java并发库-Semaphore
date : 2014-01-01
author : walkerljl
categories : programlanguage
tag : Java并发库
---
```
package org.walkerljl.practice.concurrent;
    
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Semaphore;
    
/**
 * Semaphore(信号量)
 * 限制可以使用的资源
 *
 * @author walkerljl
 */
public class SemaphoreTest {
    
    public static void main(String[] args) {
        ExecutorService exec = Executors.newCachedThreadPool();
        final Semaphore semp = new Semaphore(5);
        // 模拟20个客户端访问
        for (int index = 0; index < 20; index++) {
            final int NO = index;
            Runnable run = new Runnable() {
                public void run() {
                    try {
                        semp.acquire();//获取资源
                        System.out.println("Accessing: " + NO);
                        Thread.sleep((long) (Math.random() * 10000));
                        semp.release(); //释放资源
    
                        System.out.println("-----------------" + semp.availablePermits());
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            };
            exec.execute(run);
        }
        exec.shutdown();//退出线程池
    }
}
```