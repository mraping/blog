## 1、可见性介绍
### 1.1、 可见性：
一个线程对共享变量的修改，能够及时的被其它变量看到。
### 1.2、 共享变量：
如果一个变量在过个线程的工作内存中都存在副本，那么这个变量就是这几个线程的共享变量。
### 1.3、Java内存模型：
Java内存模型（Java Memory Model）描述了Java程序中各个变量（线程共享变量）的访问规则，以及在JVM中将变量存储到内存和从内存中读取朱变量这样的底层细节。

 - 所有的变量都储存在主内存中
 - 每个线程都有自己独立的工作内存，里面保存该线程使用到的变量的副本（主内存该变量的一份拷贝）
#### 1.3.1、两条规定
 - 线程对共享变量的所有操作都必须在自己的工作内存中进行，不能直接从主内存中读写
 - 不同线程之间无法直接访问其它线程工作内存中的变量，线程间变量值的传递需要通过主内存来完成。
### 1.4、共享变量可见性实现的原理
线程一对共享变量的修改要想被线程二及时看到，必须经过以下两个步骤：

 - 把工作内存1中更新过的变量刷新到主内存中
 - 把主内存中最新的共享变量值更新到工作内存2中
### 1.5、可见性的实现方式（Java语言层面支持的）

 - synchronized
 - volatile

## 2、synchronized 实现可见性
### 2.1、JMM关于synchronized 的两条规定

 - 线程解锁前，必须把共享变量的最新值刷新到主内存中
 - 线程加锁时，将清空工作内存中的值，从而使用共享变量时需要从主内存中重新读取最新的值（注意：加锁与解锁需要是同一把锁）
### 2.2、线程执行互斥代码的过程
 - a、获得互斥锁 
 - b、清空工作内存
 - c、从主内存拷贝变量的最新副本到工作内存
 - d、执行代码
 - e、将更改后的共享变量的值刷新到主内存
 - f、释放互斥锁
### 2.3、重排序
代码写的顺序与实际执行的顺序不同，指令重排序是编译器或处理器为了提高程序性能而做的优化。
 - a、编译器优化的重排序（编译器优化）
 - b、指令级并行的重排序（处理器优化）
 - c、内存系统的重排序（处理器优化）
### 2.4、As-If-Serial语义
as-if-serial语义的意思指：不管怎么重排序（编译器和处理器为了提高并行度），（单线程）程序的执行结果不能被改变。编译器，runtime 和处理器都必须遵守as-if-serial语义。
为了遵守as-if-serial语义，编译器和处理器不会对存在数据依赖关系的操作做重排序，因为这种重排序会改变执行结果。但是，如果操作之间不存在数据依赖关系，这些操作可能被编译器和处理器重排序。
### 2.5、代码分析
#### 2.5.1、不安全的代码
```java
public class SynchronizedDemo {
	//共享变量
    private boolean ready = false;
    private int result = 0;
    private int number = 1;   
    //写操作
    public void write(){
    	ready = true;	      				 //1.1				
    	number = 2;		                    //1.2			    
    }
    //读操作
    public void read(){			   	 
    	if(ready){						     //2.1
    		result = number*3;	 	//2.2
    	}   	
    	System.out.println("result的值为：" + result);
    }

    //内部线程类
    private class ReadWriteThread extends Thread {
    	//根据构造方法中传入的flag参数，确定线程执行读操作还是写操作
    	private boolean flag;
    	public ReadWriteThread(boolean flag){
    		this.flag = flag;
    	}
        @Override                                                                    
        public void run() {
        	if(flag){
        		//构造方法中传入true，执行写操作
        		write();
        	}else{
        		//构造方法中传入false，执行读操作
        		read();
        	}
        }
    }

    public static void main(String[] args)  {
    	SynchronizedDemo synDemo = new SynchronizedDemo();
    	//启动线程执行写操作
    	synDemo .new ReadWriteThread(true).start();
    	try {
			Thread.sleep(1000);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
    	//启动线程执行读操作
    	synDemo.new ReadWriteThread(false).start();
    }
}
```
执行以上的main方法，会发现最终的值会发生差异，因为有多种的执行顺序等原因：

 - 1.1 -> 2.1 -> 2.2 -> 1.2     —— result：3
 - 1.2 -> 2.1 -> 2.2 -> 1.1     —— result：0

也就是共享变量在线程间不可见，原因：

 - 线程的交叉执行
 - 重排序结合交叉执行
 - 共享变量更新后的值没有在工作内存与主内存之间及时更新
 
#### 2.5.1、安全的代码
 加上synchronized关键字
```java
	//写操作
    public synchronized void write(){
    	ready = true;	      								
    	number = 2;		                  	    
    }
    //读操作
    public synchronized void read(){			   	 
    	if(ready){						    
    		result = number*3;	 
    	}   	
    	System.out.println("result的值为：" + result);
    }
```
### 2.6、synchronized解决方案：
 - 原子性，不会交叉执行
 - 可见性
### 2.7、tip
 - 当一个线程访问object的一个synchronized(this)同步代码块时，其他线程对object中所有其它synchronized(this)同步代码块的访问将会被阻塞。
 - 当一个线程访问object的一个synchronized(this)同步代码块时，另一个线程仍然可以访问该object中的非synchronized(this)同步代码块。
 - 当一个线程访问object的一个synchronized(this)同步代码块时，它就获得了这个**object**的对象锁。结果，其它线程对该object对象所有同步代码部分的访问都被暂时阻塞。

## 3、volatile 实现可见性
### 3.1、volatile关键字：

 - 能够保证volatile变量的可见性
 - 不能保证volatile变量复合操作的原子性
### 3.2、volatile如何实现内存可见性：
通俗的讲：volatile变量每次被线程访问时，都强迫从主内存中重读该变量的值，而当变量改变时，又会强迫线程将最新的值刷新到主内存中去。这样，任何时刻，不同的线程总能看到该变量的值。
### 3.3、代码分析volatile不能保证原子性的原因
```java
public class VolatileDemo {
	private Lock lock = new ReentrantLock();
	private int number = 0;
	public int getNumber(){
		return this.number;
	}
	public void increase(){
		try {
			Thread.sleep(100);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		this.number++;
	}
	
	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		final VolatileDemo volDemo = new VolatileDemo();
		for(int i = 0 ; i < 500 ; i++){
			new Thread(new Runnable() {
				@Override
				public void run() {
					volDemo.increase();
				}
			}).start();
		}
		
		//如果还有子线程在运行，主线程就让出CPU资源，
		//直到所有的子线程都运行完了，主线程再继续往下执行
		while(Thread.activeCount() > 1){
			Thread.yield();
		}
		System.out.println("number : " + volDemo.getNumber());
	}
}
```
运行上面的程序代码，理论上最终的值应该是500，但是多运行几次会出现小于500的值。
原因分析：
number=5

 -  线程A读取number的值
 - 线程B读取number的值
 - 线程B执行+1操作
 - 线程B写入最新的值
此时线程B与主内存的值为6，而线程A的值仍为5。
俩个线程都对number进行了+1操作，但是只增加了1，这样就会出现小于500的结果。
### 3.4、解决方案
 - 使用synchronized关键字
```java
	public synchronized void increase(){
		try {
			Thread.sleep(100);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		this.number++;
	}
```
 或者
```java
	public void increase(){
		try {
			Thread.sleep(100);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		//降低锁的粒度
		synchronized(this){
			this.number++;
		}
	}
```
 - 使用ReentrantLock
```java
	public void increase(){
		try {
			Thread.sleep(100);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		lock.lock();
		try {
			this.number++;
		} finally {
			lock.unlock();
		}
	}
```
 - 使用AtomicInteger
### 3.5、使用场景
 - 对变量的写入操作不依赖于当前值（不满足的：num++；满足：boolean变量、记录温度变化的变量）
 - 该变量没有包含在具有其他变量的不变式中（比如存在俩个volatile变量low,up;不满足的：不变式 low < up）

## 4、总结

 - volatile使用场景没有synchronized广泛，但是比synchronized更轻量级，不会阻塞线程
 - synchronized既能保证可见性也能保证原子性，而volatile只能保证可见性
 - 在大多数情况下即使没有保证可见性的措施，共享变量依然可以及时的更新，原因是：一般只有在短时间内高并发的情况下才会出现变量没有及时更新的情况，因为cpu会在执行时快速的刷新缓存。
 
## 5、本文全部内容来源于慕课网课程：[细说Java多线程之内存可见性](https://www.imooc.com/learn/352 "细说Java多线程之内存可见性")的学习记录。