---
layout : post
title : Java并发库-AtomicInteger
date : 2014-01-01
author : walkerljl
categories : blog
tag : Java并发库
---

```
package org.walkerljl.practice.concurrent;
    
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.atomic.AtomicInteger;
    
/**
 * AtomicIntegerTest
 *
 * @author lijunlin
 */
public class AtomicIntegerTest {
    
    private static final AtomicInteger TEST_INTEGER = new AtomicInteger(0);
    private static int index = 1;
    
    public static void main(String[] args) throws InterruptedException {
        final CountDownLatch startCountDown = new CountDownLatch(1);
        final Thread[] threads = new Thread[10];
        for (int i = 0; i < 10; i++) {
            threads[i] = new Thread() {
                @Override
                public void run() {
                    try {
                        startCountDown.await();
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                    for (int j = 0; j < 100; j++) {
                        index++;
                        TEST_INTEGER.incrementAndGet();
                    }
                }
            };
            threads[i].start();
        }
    
        Thread.sleep(1000);
        startCountDown.countDown();
        for (Thread thread : threads) {
            thread.join();
        }
    
        System.out.println("AtomicInteger最终运行结果:" + TEST_INTEGER.get());
        System.out.println("valatile 最终运行结果:" + index);
    }
}
```
