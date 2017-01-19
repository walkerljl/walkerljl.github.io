    package org.walkerljl.practice.concurrent;
    
    import java.util.Random;
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.atomic.AtomicReference;
    
    /**
     * AtomicReferenceABATest
     *
     * @author lijunlin
     */
    public class AtomicReferenceABATest {
    
        private static final AtomicReference<String> ATOMIC_REFERENCE = new AtomicReference<String>("abc");
        private static final Random RANDOM_OBJECT = new Random();
    
        public static void main(String[] args) throws InterruptedException {
            final CountDownLatch startCountDownLatch = new CountDownLatch(1);
            Thread[] threads = new Thread[20];
            for (int i = 0; i < 20; i++) {
                final int num = i;
                threads[i] = new Thread() {
                    @Override
                    public void run() {
                        String oldValue = ATOMIC_REFERENCE.get();
                        try {
                            startCountDownLatch.await();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                        try {
                            Thread.sleep(RANDOM_OBJECT.nextInt() & 500);
                        } catch (Exception e) {
                            e.printStackTrace();
                        }
                        if (ATOMIC_REFERENCE.compareAndSet(oldValue, oldValue + num)) {
                            System.out.println("线程:" + num + "，进行了对象修改");
                        }
                    }
                };
                threads[i].start();
            }
            Thread.sleep(200);
            startCountDownLatch.countDown();
            new Thread() {
                @Override
                public void run() {
                    try {
                        Thread.sleep(RANDOM_OBJECT.nextInt() & 200);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
    
                    while (!ATOMIC_REFERENCE.compareAndSet(ATOMIC_REFERENCE.get(), "abc")) {
                    }
                    ;
    
                    System.out.println("已经修改为原始值");
                }
            }.start();
        }
    }