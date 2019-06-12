## 等待多线程完成的CountDownLatch
CountDownLatch是java.util.concurrent包中的一个类。CountDownLatch主要提供的机制是当多个（具体数量等同于初始化CountDownLatch时count的值）线程都达到了预期状态或完成预期工作时触发事件，其它线程可以等待这个事件来触发自己后续的工作。

CountDownLatch允许一个或多个线程等待其他线程完成操作。

需要注意的是：

等待的线程可以是多个，即CountDownLatch可以唤醒多个等待的线程。到达自己预期状态的线程会调用CountDownLatch方法，而等待的线程会调用CountDownLatch的await方法。

计数器必须大于等于0，只是等于0时候，计数器就是零，调用await方法时不会阻塞当前线程。CountDownLatch不可能重新初始化或者修改CountDownl atch对象的内部计数器的值。一个线程调用countDown方法happen-before,另外一个线程调用await方法。

### 使用场景

比如对于马拉松比赛，进行排名计算，参赛者的排名，肯定是跑完比赛之后，进行计算得出的，翻译成Java识别的预发，就是N个线程执行操作，主线程等到N个子线程执行完毕之后，在继续往下执行。

### 典型用法

CountDownLatch典型用法1：某一线程在开始运行前等待n个线程执行完毕。将CountDownLatch的计数器初始化为n new CountDownLatch(n) ，每当一个任务线程执行完毕，就将计数器减1 countdownlatch.countDown()，当计数器的值变为0时，在CountDownLatch上 await() 的线程就会被唤醒。

### 相关文章

[CountDownLatch详解](https://yq.aliyun.com/articles/689069?spm=a2c4e.11157919.spm-cont-list.182.146c27ae5jqaF5)
[什么时候使用CountDownLatch](http://www.importnew.com/15731.html)



