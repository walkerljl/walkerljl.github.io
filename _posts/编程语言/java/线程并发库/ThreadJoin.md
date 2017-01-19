
    package org.walkerljl.practice.concurrent;
    
    import java.util.Random;
    
    /**
     * ThreadJoinTest.java
     *
     * @author lijunlin
     */
    public class ThreadJoinTest {
    
        private static final int COUNTER = 1000000;
    
        public static void main(String[] args) throws InterruptedException {
            int[] array = new int[COUNTER];
            Random random = new Random();
            for (int i = 0; i < COUNTER; i++) {
                array[i] = Math.abs(random.nextInt());
            }
            long startTime = System.currentTimeMillis();
            Computer c1 = new Computer(array, 0, COUNTER / 2);
            Computer c2 = new Computer(array, (COUNTER / 2 + 1), COUNTER);
            c1.start();
            c2.start();
    
            //begin join
            c1.join();
            c2.join();
            System.out.println("Total time->" + (System.currentTimeMillis() - startTime));
            System.out.println((c1.getResult() + c2.getResult())); //& Integer.MAX_VALUE);
    
            int result = 0;
            for (int i = 1; i <= COUNTER + 1; i++) {
                result += i;
            }
            System.out.println("Expected result->" + result);
        }
    
        static class Computer extends Thread {
            private int start;
            private int end;
            private int result;
            private int[] array;
    
            public Computer(int[] array, int start, int end) {
                this.array = array;
                this.start = start;
                this.end = end;
            }
    
            @Override
            public void run() {
                for (int i = start; i < end; i++) {
                    result += array[i];
                    //debug
                    System.out.println("Thread name->" + Thread.currentThread().getName());
    //				if (result < 0) {
    //					result &= Integer.MAX_VALUE;
    //				}
                }
            }
    
            public int getResult() {
                return result;
            }
        }
    }