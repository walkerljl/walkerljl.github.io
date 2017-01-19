    package org.walkerljl.practice.concurrent;
    
    import java.io.IOException;
    import java.util.Random;
    import java.util.concurrent.BrokenBarrierException;
    import java.util.concurrent.CyclicBarrier;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    
    /**
     * CyclicBarrierTest
     * 一个同步辅助类，它允许一组线程互相等待，直到到达某个公共屏障点 (common barrier point)
     * 可重复利用
     *
     * @author lijunlin
     */
    public class CyclicBarrierTest {
    
        public static void main(String[] args) throws IOException, InterruptedException {
            int count = 5;
            CyclicBarrier barrier = new CyclicBarrier(count);
            ExecutorService executor = Executors.newFixedThreadPool(count);
            String subject = "100米短跑";
            for (int index = 0; index < count; index++) {
                executor.submit(new Thread(new Runner(barrier, subject, ((index + 1) + "号选手"))));
            }
    
            subject = "400米短跑";
            for (int index = 0; index < count; index++) {
                executor.submit(new Thread(new Runner(barrier, subject, ((index + 1) + "号选手"))));
            }
            executor.shutdown();
        }
    }
    
    class Runner implements Runnable {
        private CyclicBarrier barrier;
        private String name;
        private String subject;
    
        public Runner(CyclicBarrier barrier, String subject, String name) {
            this.barrier = barrier;
            this.subject = subject;
            this.name = name;
        }
    
        @Override
        public void run() {
            String messagePrefix = subject + ":" + name;
            try {
                Thread.sleep(1000 * (new Random()).nextInt(8));
                System.out.println(messagePrefix + " 准备好了...");
                // barrier的await方法，在所有参与者都已经在此 barrier 上调用 await 方法之前，将一直等待。
                barrier.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (BrokenBarrierException e) {
                e.printStackTrace();
            }
            System.out.println(messagePrefix + " 起跑！");
        }
    }