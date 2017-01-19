
    package org.walkerljl.practice.concurrent;
    
    import java.lang.Thread.UncaughtExceptionHandler;
    
    /**
     * 线程异常终止处理器
     *
     * @author lijunlin
     */
    public class ThreadExceptionHandlerTest {
    
        public static void main(String[] args) {
            Thread thread = new Thread() {
                @Override
                public void run() {
                    Integer.parseInt("ABC");
                }
            };
            thread.setUncaughtExceptionHandler(new ThreadExceptionHandler());
            thread.start();
        }
    }
    
    /**
     * 线程异常处理器
     *
     * @author lijunlin<walkerljl@qq.com>
     */
    class ThreadExceptionHandler implements UncaughtExceptionHandler {
    
        @Override
        public void uncaughtException(Thread t, Throwable e) {
            System.out.println(String.format("线程Id->%s,Name->%s出现异常",
                    new Object[]{Thread.currentThread().getId(), Thread.currentThread().getName()}));
            e.printStackTrace();
        }
    }