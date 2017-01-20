---
layout : post
title : Java并发库-CountDownLatch
date : 2014-01-01
author : walkerljl
categories : blog
tag : Java并发库
---    
```
package org.walkerljl.practice.concurrent;
    
import java.util.concurrent.CountDownLatch;
    
/**
 * CountDownLatch(线程同步辅助类)
 *
 * @author lijunlin
 */
public class CountDownLatchTest {
    
    public void test() {
        int counter = 5;
        CountDownLatch countDownLatch = new CountDownLatch(counter);
    
        for (int index = 0; index < counter; index++) {
            new Thread(new Worker(index, countDownLatch)).start();
        }
    
        System.out.println("正在等待所有子线程执行完毕");
        try {
            countDownLatch.await();
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        System.out.println("所有子线程执行完毕, 主线程结束");
    }
}
    
class Worker implements Runnable {
    private int index;
    private CountDownLatch countDownLatch;
    
    public Worker(int index, CountDownLatch countDownLatch) {
        this.index = index;
        this.countDownLatch = countDownLatch;
    }
    
    @Override
    public void run() {
        try {
            Thread.currentThread().setName("线程-" + index);
            Thread.sleep(index * 1000);
            System.out.println(Thread.currentThread().getName() + "执行完成");
        } catch (Throwable e) {
            e.printStackTrace();
        } finally {
            countDownLatch.countDown();
        }
    }
}
```