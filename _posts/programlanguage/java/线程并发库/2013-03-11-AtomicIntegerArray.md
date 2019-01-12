---
layout : post
title : Java并发库-AtomicIntegerArray
date : 2014-01-01
author : walkerljl
categories : programlanguage
tag : Java并发库
---    
```
package org.walkerljl.practice.concurrent;
    
import java.util.concurrent.atomic.AtomicIntegerArray;
    
/**
 * AtomicIntegerArrayTest
 *
 * @author walkerljl
 */
public class AtomicIntegerArrayTest {
    
    private static final AtomicIntegerArray ATOMIC_INTEGER_ARRAY = new AtomicIntegerArray(10);
    
    public static void main(String[] args) throws InterruptedException {
        Thread[] threads = new Thread[100];
        for (int i = 0; i < 100; i++) {
            final int index = i % 10;
            final int threadNum = i;
            threads[i] = new Thread() {
                @Override
                public void run() {
                    int result = ATOMIC_INTEGER_ARRAY.addAndGet(index, index + 1);
                    System.out.println(String.format("线程编号:%s, 对应的原始值为:%s, 添加后的结果为:%s",
                            new Object[]{threadNum, (index + 1), result}));
                }
            };
            threads[i].start();
        }
    
        for (Thread thread : threads) {
            thread.join();
        }
        System.out.println("执行完成，结果列表如下:");
    
        for (int i = 0; i < ATOMIC_INTEGER_ARRAY.length(); i++) {
            System.out.println(ATOMIC_INTEGER_ARRAY.get(i));
        }
    }
}
```