## Java线程的六种状态

**新建状态(New)**

当用new操作符创建一个线程后， 例如new Thread(r)，此时线程处在新建状态。 当一个线程处于新建状态时，线程中的任务代码还没开始运行。

**就绪状态(Runnable)**

也被称为“可执行状态”。一个新创建的线程并不自动开始运行，要执行线程，必须调用线程的start()方法。当调用了线程对象的start()方法即启动了线程，此时线程就处于就绪状态。

处于就绪状态的线程并不一定立即运行run()方法，线程还必须同其他就绪线程竞争CPU，只有获得CPU使用权才可以运行线程。比如在单核心CPU的计算机系统中，不可能同时运行多个线程，一个时刻只能有一个线程处于运行状态。对与多个处于就绪状态的线程是由Java运行时系统的线程调度程序(thread scheduler)来调度执行。

除了调用start()方法后让线程变成就绪状态，一个线程阻塞状态结束后也可以变成就绪状态，或者从运行状态变化到就绪状态。

**运行状态(Running)**

线程获取到CPU使用权进行执行。需要注意的是，线程只能从就绪状态进入到运行状态。真正开始执行run()方法的内容。

**阻塞状态(Blocked)**

线程在获取锁失败时(因为锁被其它线程抢占)，它会被加入锁的同步阻塞队列，然后线程进入阻塞状态(Blocked)。处于阻塞状态(Blocked)的线程放弃CPU使用权，暂时停止运行。待其它线程释放锁之后，阻塞状态(Blocked)的线程将在次参与锁的竞争，如果竞争锁成功，线程将进入就绪状态(Runnable) 。

**等待状态(WAITING)**

或者叫条件等待状态，当线程的运行条件不满足时，通过锁的条件等待机制(调用锁对象的wait()或显示锁条件对象的await()方法)让线程进入等待状态(WAITING)。处于等待状态的线程将不会被cpu执行，除非线程的运行条件得到满足后，其可被其他线程唤醒，进入阻塞状态(Blocked)。调用不带超时的Thread.join()方法也会进入等待状态。

**限时等待状态(TIMED_WAITING)**

限时等待是等待状态的一种特例，线程在等待时我们将设定等待超时时间，如超过了我们设定的等待时间，等待线程将自动唤醒进入阻塞状态(Blocked)或就绪状态(Runnable) 。在调用Thread.sleep()方法，带有超时设定的Object.wait()方法，带有超时设定的Thread.join()方法等，线程会进入限时等待状态(TIMED_WAITING)。

**死亡状态(TERMINATED)**

线程执行完了或者因异常退出了run()方法，该线程结束生命周期。



查看jdk源码是6种状态,没有上面的Running

```java
/**
     * A thread state.  A thread can be in one of the following states:
     * <ul>
     * <li>{@link #NEW}<br>
     *     A thread that has not yet started is in this state.
     *     </li>
     * <li>{@link #RUNNABLE}<br>
     *     A thread executing in the Java virtual machine is in this state.
     *     </li>
     * <li>{@link #BLOCKED}<br>
     *     A thread that is blocked waiting for a monitor lock
     *     is in this state.
     *     </li>
     * <li>{@link #WAITING}<br>
     *     A thread that is waiting indefinitely for another thread to
     *     perform a particular action is in this state.
     *     </li>
     * <li>{@link #TIMED_WAITING}<br>
     *     A thread that is waiting for another thread to perform an action
     *     for up to a specified waiting time is in this state.
     *     </li>
     * <li>{@link #TERMINATED}<br>
     *     A thread that has exited is in this state.
     *     </li>
     * </ul>
     *
     * <p>
     * A thread can be in only one state at a given point in time.
     * These states are virtual machine states which do not reflect
     * any operating system thread states.
     *
     * @since   1.5
     * @see #getState
     */
    public enum State {
        /**
         * Thread state for a thread which has not yet started.
         */
        NEW,

        /**
         * Thread state for a runnable thread.  A thread in the runnable
         * state is executing in the Java virtual machine but it may
         * be waiting for other resources from the operating system
         * such as processor.
         */
        RUNNABLE,

        /**
         * Thread state for a thread blocked waiting for a monitor lock.
         * A thread in the blocked state is waiting for a monitor lock
         * to enter a synchronized block/method or
         * reenter a synchronized block/method after calling
         * {@link Object#wait() Object.wait}.
         */
        BLOCKED,

        /**
         * Thread state for a waiting thread.
         * A thread is in the waiting state due to calling one of the
         * following methods:
         * <ul>
         *   <li>{@link Object#wait() Object.wait} with no timeout</li>
         *   <li>{@link #join() Thread.join} with no timeout</li>
         *   <li>{@link LockSupport#park() LockSupport.park}</li>
         * </ul>
         *
         * <p>A thread in the waiting state is waiting for another thread to
         * perform a particular action.
         *
         * For example, a thread that has called <tt>Object.wait()</tt>
         * on an object is waiting for another thread to call
         * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
         * that object. A thread that has called <tt>Thread.join()</tt>
         * is waiting for a specified thread to terminate.
         */
        WAITING,

        /**
         * Thread state for a waiting thread with a specified waiting time.
         * A thread is in the timed waiting state due to calling one of
         * the following methods with a specified positive waiting time:
         * <ul>
         *   <li>{@link #sleep Thread.sleep}</li>
         *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
         *   <li>{@link #join(long) Thread.join} with timeout</li>
         *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
         *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
         * </ul>
         */
        TIMED_WAITING,

        /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */
        TERMINATED;
    }
```

