## ThreadLocal

ThreadLocal即线程变量，是一个以ThreadLocal对象为键、任意对象为值的存储结构。这个结构被附带在线程上，也就是说一个线程 可以根据一个ThreadLocal对象查询到绑定在这个线程上的一个值。可以通过set(T)方法来设置一个值， 在当前线程下再通过get()方法获取到原先设置的值。

> 作者： 零度冰炫 匠心零度
>
> 链接：https://mp.weixin.qq.com/s/8Ql-5kaUtxiCWyHR6uPPBw

### ThreadLocal用在什么地方？

讨论ThreadLocal用在什么地方前，我们先明确下，如果仅仅就一个线程，那么都不用谈ThreadLocal的，**ThreadLocal是用在多线程的场景的！！！**

ThreadLocal归纳下来就2类用途：

- **保存线程上下文信息，在任意需要的地方可以获取！！！**
- **线程安全的，避免某些情况需要考虑线程安全必须同步带来的性能损失！！！**

#### 保存线程上下文信息，在任意需要的地方可以获取！！！

由于ThreadLocal的特性，同一线程在某地方进行设置，在随后的任意地方都可以获取到。从而可以用来保存线程上下文信息。

常用的比如每个请求怎么把一串后续关联起来，就可以用ThreadLocal进行set，在后续的任意需要记录日志的方法里面进行get获取到请求id，从而把整个请求串起来。

还有比如Spring的事务管理，用ThreadLocal存储Connection，从而各个DAO可以获取同一Connection，可以进行事务回滚，提交等操作。

> 备注：ThreadLocal的这种用处，很多时候是用在一些优秀的框架里面的，一般我们很少接触，反而下面的场景我们接触的更多一些！

#### 线程安全的，避免某些情况需要考虑线程安全必须同步带来的性能损失！！！

ThreadLocal为解决多线程程序的并发问题提供了一种新的思路。但是ThreadLocal也有局限性，我们来看看阿里规范：

> 【参考】ThreadLocal 无法解决共享对象的更新问题，ThreadLocal 对象建议使用 static修饰。这个变量是针对一个线程内所有操作共享的，所以设置为静态变量，所有此类实例共享此静态变量 ，也就是说在类第一被使用时装载，只分配一块存储空间，所有此类的对象(只要是这个线程内定义的)都可以操控这个变量。

每个线程往ThreadLocal中读写数据是线程隔离，互相之间不会影响的，所以**ThreadLocal无法解决共享对象的更新问题！**

> 由于不需要共享信息，自然就不存在竞争问题了，从而保证了某些情况下线程的安全，以及避免了某些情况需要考虑线程安全必须同步带来的性能损失！！！

**这类场景阿里规范里面也提到了：**

> 【强制】SimpleDateFormat 是线程不安全的类，一般不要定义为static变量，如果定义为 static，必须加锁，或者使用 DateUtils 工具类。 
>
> 正例:注意线程安全，使用 DateUtils。亦推荐如下处理: 
>
> ```java
> private static final ThreadLocal<DateFormat> df = new ThreadLocal<DateFormat>() {
>  	@Override 
> 	protected DateFormat initialValue() {
>  		return new SimpleDateFormat("yyyy-MM-dd"); 
> 	} 
> }; 
> ```
>
> 说明:如果是 JDK8 的应用，可以使用 Instant 代替 Date，LocalDateTime 代替 Calendar，DateTimeFormatter 代替 SimpleDateFormat，官方给出的解释:simple beautiful strong immutable thread-safe。 



### ThreadLocal一些细节！

ThreaLocal使用示例代码：

```java
public class ThreadLocalTest {
    private static ThreadLocal<Integer> threadLocal = new ThreadLocal<>();

    public static void main(String[] args) {

        new Thread(() -> {
            try {
                for (int i = 0; i < 100; i++) {
                    threadLocal.set(i);
                    System.out.println(Thread.currentThread().getName() + "====" + threadLocal.get());
                    try {
                        Thread.sleep(200);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            } finally {
                threadLocal.remove();
            }
        }, "threadLocal1").start();


        new Thread(() -> {
            try {
                for (int i = 0; i < 100; i++) {
                    System.out.println(Thread.currentThread().getName() + "====" + threadLocal.get());
                    try {
                        Thread.sleep(200);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            } finally {
                threadLocal.remove();
            }
        }, "threadLocal2").start();
    }
}
```

代码运行结果：

```
threadLocal1====0
threadLocal2====null
threadLocal2====null
threadLocal1====1
threadLocal2====null
threadLocal1====2
threadLocal2====null
threadLocal1====3
threadLocal2====null
```

从运行的结果我们可以看到threadLocal1进行set值对threadLocal2并没有任何影响！

### Thread、ThreadLocalMap、ThreadLocal总览图：

![总览图](https://github.com/suxiongwei/blog/blob/master/img/20190619165221.png)

Thread类有属性变量threadLocals （类型是ThreadLocal.ThreadLocalMap），也就是说每个线程有一个自己的ThreadLocalMap ，所以每个线程往这个ThreadLocal中读写隔离的，并且是互相不会影响的。

**一个ThreadLocal只能存储一个Object对象，如果需要存储多个Object对象那么就需要多个ThreadLocal！！！**

如图：

![流程](https://github.com/suxiongwei/blog/blob/master/img/20190619165308.png)

看到上面的几个图，大概思路应该都清晰了，我们Entry的key指向ThreadLocal用**虚线**表示弱引用 

java对象的引用包括 ： 强引用，软引用，弱引用，虚引用 。

因为这里涉及到弱引用，简单说明下：

弱引用也是用来描述非必需对象的，当JVM进行垃圾回收时，无论内存是否充足，**该对象仅仅被弱引用关联**，那么就会被回收。

**当仅仅只有ThreadLocalMap中的Entry的key指向ThreadLocal的时候，ThreadLocal会进行回收的！！！**

ThreadLocal被垃圾回收后，在ThreadLocalMap里对应的Entry的键值会变成null，但是Entry是强引用，那么Entry里面存储的Object，并没有办法进行回收，所以ThreadLocalMap 做了一些额外的回收工作。

### ThreadLocal最佳实践

```
try {
    // 其它业务逻辑
} finally {
    threadLocal对象.remove();
}
```