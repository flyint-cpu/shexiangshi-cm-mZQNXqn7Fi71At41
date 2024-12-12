
# ScheduledExecutorService 简介


* `ScheduledExecutorService`是`ExecutorService`的一个子接口。它主要用于在给定的延迟之后或周期性地执行任务。这个接口提供了一种方便的方式来处理异步任务的调度，相比于传统的`Timer`和`TimerTask`，它具有更好的灵活性和可靠性，特别是在处理多线程环境下的任务调度时。
* 我们可以通过`ScheduledExecutorService`，更有效地管理和调度多个异步任务，这些任务可以是一次性执行的，也可以是周期性重复执行的。


# 常用方法


## schedule


`schedule(Runnable command, long delay, TimeUnit unit)`


* **功能描述**：该方法用于安排一个任务（`Runnable`）在指定的延迟（`delay`）之后执行一次。`TimeUnit`是一个枚举类型，用于指定延迟的时间单位，例如`TimeUnit.SECONDS`表示秒，`TimeUnit.MILLISECONDS`表示毫秒等。
* **示例代码**：



```


|  | ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1); |
| --- | --- |
|  | Runnable task = () -> System.out.println("任务在延迟后执行"); |
|  | scheduler.schedule(task, 5, TimeUnit.SECONDS); |
|  | scheduler.shutdown(); |


```

* **解释**：在这个示例中，首先通过`Executors.newScheduledThreadPool(1)`创建了一个`ScheduledExecutorService`对象，线程池大小为 1。然后定义了一个`Runnable`任务，该任务只是简单地打印一条消息。接着使用`schedule`方法安排这个任务在 5 秒（`TimeUnit.SECONDS`）后执行。最后调用`shutdown`方法来优雅地关闭`ScheduledExecutorService`，防止资源泄漏。


## scheduleAtFixedRate


`scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit)`


* **功能描述**：用于安排一个任务在初始延迟（`initialDelay`）之后开始执行，然后以固定的频率（`period`）重复执行。如果任务的执行时间超过了指定的周期（`period`），下一次执行会在当前任务执行完成后立即开始，不会等待一个完整的周期。
* **示例代码**：



```


|  | ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1); |
| --- | --- |
|  | Runnable task = () -> System.out.println("周期性任务执行"); |
|  | scheduler.scheduleAtFixedRate(task, 2, 3, TimeUnit.SECONDS); |
|  | try { |
|  | Thread.sleep(10000); |
|  | } catch (InterruptedException e) { |
|  | e.printStackTrace(); |
|  | } |
|  | scheduler.shutdown(); |


```

* **解释**：在这个例子中，任务会在 2 秒（`initialDelay`）后首次执行，然后每隔 3 秒（`period`）执行一次。通过`Thread.sleep(10000)`让主线程等待一段时间，以便观察任务的周期性执行。最后关闭`ScheduledExecutorService`。


## scheduleWithFixedDelay


`scheduleWithFixedDelay(Runnable command, long initialDelay, long delay, TimeUnit unit)`


* **功能描述**：安排一个任务在初始延迟（`initialDelay`）之后开始执行，并且在每次执行完成后，等待固定的延迟（`delay`）时间后再执行下一次任务。这与`scheduleAtFixedRate`不同，`scheduleAtFixedRate`是按照固定的频率执行，而这个方法是在上一次任务执行完成后等待固定的延迟时间再执行下一次。
* **示例代码**：



```


|  | ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1); |
| --- | --- |
|  | Runnable task = () -> { |
|  | System.out.println("任务开始执行"); |
|  | try { |
|  | Thread.sleep(2000);  // 模拟任务执行时间 |
|  | } catch (InterruptedException e) { |
|  | e.printStackTrace(); |
|  | } |
|  | System.out.println("任务执行完成"); |
|  | }; |
|  | scheduler.scheduleWithFixedDelay(task, 2, 3, TimeUnit.SECONDS); |
|  | try { |
|  | Thread.sleep(15000); |
|  | } catch (InterruptedException e) { |
|  | e.printStackTrace(); |
|  | } |
|  | scheduler.shutdown(); |


```

* **解释**：在这个示例中，任务首先在 2 秒（`initialDelay`）后开始执行。任务本身会模拟执行 2 秒（通过`Thread.sleep(2000)`），然后在任务执行完成后，等待 3 秒（`delay`）再执行下一次任务。通过`Thread.sleep(15000)`让主线程等待足够长的时间来观察任务的执行情况，最后关闭`ScheduledExecutorService`。


# 关于shutdown


* **关闭释放资源：** 虽然JVM会自动回收资源，但是为防止内存泄漏，还是建议每次执行完成后都添加`shutdown()`方法，我们可以根据不同场景选择不同的关闭方法



```


|  | // 等待所有任务完成才去执行关闭 |
| --- | --- |
|  | executor.shutdown(); |
|  | // 如果需要立即关闭并尝试中断任务 |
|  | executor.shutdownNow(); |
|  | // 指定等待时间来等待执行完成 |
|  | executor.awaitTermination(long timeout, TimeUnit unit) // 如果在等待时间内执行器成功关闭，该方法将返回true；否则返回false |


```

 本博客参考[PodHub豆荚加速器官方网站](https://rikeduke.com)。转载请注明出处！
