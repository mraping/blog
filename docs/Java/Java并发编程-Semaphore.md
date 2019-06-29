## 控制并发线程数的Semaphore

Semaphore (信号量) 是用来控制同时访问特定资源的线程数量，它通过协调各个线程，以保证合理的使用公共资源。
多年以来，我都觉得从字面上很难理解Semaphore所表达的含义，只能把它比作是控制流量的红绿灯。比如xx马路要限制流量，只允许同时有五辆车在这条路上行使，其他的都必须在路口等待，所以前五辆车会看到绿灯，可以开进这条马路，后面的车会看到红灯，不能驶入xx马路，但是如果前五辆中有1辆车已经离开了xx马路，那么后面就允许有1辆车驶入马路，这个例子里说的车就是线程，驶入马路就表示线程在执行，离开马路就表示线程执行完成，看见红灯就表示线程被阻塞，不能执行。

### Semaphore的构造方法
Semaphore (int permits)接受一个整型的数字，表示可用的许可证数量。Semaphore (10) 表示允许10个线程获取许可证，也就是最大并发数是10。Semaphore的用法也很简单，首先线程使用Semaphore的acquire()方法获取一个许可证，使用完之后调用release()方法归还许可证。还可以用tryAcquire()方法尝试获取许可证。

### 其他方法
Semaphore还提供一些其他方法， 具体如下。

- intavailablePermits():返回此信号量中当前可用的许可证数。
- intgetQueueLength():返回正在等待获取许可证的线程数。
- booleanhasQueuedThreads():是否有线程正在等待获取许可证。
- void reducePermits (int reduction) :减少reduction个许可证，是个protected方法。
- Collection getQueuedThreads():返回所有等待获取许可证的线程集合，是个protected方法。

### 代码示例

```java
public class TestSemaphore {

    private static class Car extends Thread{
        private Semaphore semaphore;
        private int num;

        private Car(Semaphore semaphore,int num){
            this.semaphore = semaphore;
            this.num = num;
        }

        @Override
        public void run() {
            try {
                System.out.println("申请通过许可的车辆编号：" + num);
                semaphore.acquire();
                Thread.sleep(5000);
                System.out.println("通过车辆编号：" + num);
                // 释放许可
                semaphore.release();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        // 同时通过的车辆数为 5
        Semaphore semaphore = new Semaphore(5);
        // 8辆车需要通过
        for (int i = 0; i < 7; i++) {
            new Thread(new Car(semaphore,i)).start();
        }
    }
}
```

