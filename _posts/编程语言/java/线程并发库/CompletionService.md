    package org.walkerljl.practice.asyncparallel;
    
    import org.walkerljl.commons.datetime.DateUtils;
    import org.walkerljl.practice.Task;
    
    import java.util.concurrent.*;
    
    /**
     * 使用CompletionService自带的阻塞队列获取执行结果。
     * Task执行成功之后，才会将Future扔到BlockingQueue
     * @author lijunlin
     */
    public class CompletionServiceTest {
    
        public static void main(String[] args) {
            ExecutorService executorService = Executors.newCachedThreadPool();
            CompletionService<Integer> completionService = new ExecutorCompletionService<Integer>(executorService);
            int taskAmount = 10;
            for (int i = taskAmount; i > 0; i--) {
                completionService.submit(new Task((long)i, i * 5));
            }
    
            for (int i = taskAmount; i > 0; i--) {
                try {
                    System.out.println(DateUtils.getCurrentDateTime() + "->" + completionService.take().get());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (ExecutionException e) {
                    e.printStackTrace();
                }
            }
        }
    }
