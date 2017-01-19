    package org.walkerljl.practice.concurrent;
    
    import java.util.Random;
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.atomic.AtomicStampedReference;
    
    /**
     * 同一个业务重复提交两次,对于非重复提交类型的并发还是会有问题,
     * 使用AtomicMarkableReference解决此问题
     *
     * @author lijunlin
     */
    public class AtomicStampedReferenceTest {
    
        private static final AtomicStampedReference<String> ATOMIC_REFERENCE = new AtomicStampedReference<String>("abc", 0);
        private static final Random RANDOM_OBJECT = new Random();
    
        public static void main(String[] args) throws InterruptedException {
            final CountDownLatch startCountDownLatch = new CountDownLatch(1);
            Thread[] threads = new Thread[20];
            for (int i = 0; i < 20; i++) {
                final int num = i;
                threads[i] = new Thread() {
                    @Override
                    public void run() {
                        String oldValue = ATOMIC_REFERENCE.getReference();
                        int stamp = ATOMIC_REFERENCE.getStamp();
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
                        if (ATOMIC_REFERENCE.compareAndSet(oldValue, oldValue + num, stamp, stamp + 1)) {
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
                    int stamp = ATOMIC_REFERENCE.getStamp();
                    while (!ATOMIC_REFERENCE.compareAndSet(ATOMIC_REFERENCE.getReference(), "abc", stamp, stamp + 1)) {
                        stamp = ATOMIC_REFERENCE.getStamp();
                    }
                    ;
                    System.out.println("已经修改为原始值");
                }
            }.start();
        }
    }