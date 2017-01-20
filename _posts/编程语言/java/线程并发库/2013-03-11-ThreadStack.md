---
layout : post
title : Java并发库-ThreadStack
date : 2014-01-01
author : walkerljl
categories : blog
tag : Java并发库
---
``` 
package org.walkerljl.practice.concurrent;
    
/**
 * ThreadStackTest.java
 *
 * @author lijunlin
 */
public class ThreadStackTest {
    
    public static void main(String[] args) {
        printStack(getStackByThread());
        printStack(getStackByException());
    }
    
    private static void printStack(StackTraceElement[] stacks) {
        for (StackTraceElement stack : stacks) {
            System.out.println(stack);
        }
        System.out.println("\n");
    }
    
    private static StackTraceElement[] getStackByThread() {
        return Thread.currentThread().getStackTrace();
    }
    
    private static StackTraceElement[] getStackByException() {
        return new Exception().getStackTrace();
    }
}
```
