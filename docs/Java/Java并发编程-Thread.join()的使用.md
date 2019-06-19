## Thread.join()的使用

如果一个线程A执行了thread.join()语句，其含义是:当前线程A等待thread线程终止之后才从thread.join()返回。线程Thread除了提供join()方法之外，还提供了join(long mllis)和join(long mllis, int nanos)两个具备超时特性的方法。这两个超时方法表示，如果线程thread在给定的超时时间里没有终止，那么将会从该超时方法中返回。

### 代码示例

```java
public class Join {

    public static void main(String[] args) throws Exception{
        Thread previous =  Thread.currentThread();
        for (int i = 0; i < 10; i++) {
            // 每个线程拥有前一个线程的引用，需要等待前一个线程终止，才能从等待中返回￼ 
            Thread thread = new Thread(new Domino(previous), String.valueOf(i));
            thread.start();
            previous = thread;
        }
        TimeUnit.SECONDS.sleep(5);
        System.out.println(Thread.currentThread().getName() + " terminate. ");
    }

    static class Domino implements Runnable {
        private Thread thread;

        public Domino(Thread thread) {
            this.thread = thread;
        }

        public void run() {
            try {
                thread.join();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + " terminate.");
        }
    }
}
```

在以上代码中，创建了10个线程，编号0~9,每个线程调用前一个线程的join()方法，也就是线程0结束了，线程才能从join()方法中返回，而线程0需 要等待main线程结束。

输出如下：

```
main terminate.
0 terminate. 
1 terminate.
2 terminate. 
3 terminate. 
4 terminate. 
5 terminate. 
6 terminate. 
7 terminate. 
8 terminate. 
9 terminate.
```


从上述输出可以看到，每个线程终止的前提是前驱线程的终止,每个线程等待前驱线程终止后，才从join()方法返回，这里涉及了等待通知机制(等待前驱线程结束，接收前驱线程结束通知)。