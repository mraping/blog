### 一.特性：

1. 一般在**service**里加@Transactional注解，不建议在接口上添加，加了此注解后此类会纳入spring事务管理中，每个业务方法执行时，都会开启一个事务，不过都是按照相同的管理机制。

2.  @Transactional注解只能应用到public修饰符上，其它修饰符不起作用，但不报错。

3.  默认情况下此注解会对unchecked异常进行回滚，对checked异常不回滚。

   > ##### 那什么是unchecked,什么是checked呢？
   >
   > 通俗的说，编译器能检测到的是checked，检测不到的就是unchecked。派生于Error或者RuntimeException（比如空指针，1/0）的异常称为unchecked异常。
   > 继承自Exception得异常统称为checked异常，如IOException、TimeoutException等。

4. 只读事务： @Transactional(propagation=Propagation.NOT_SUPPORTED,readOnly=true)
    只读标志只在事务启动时应用，否则即使配置也会被忽略。
    启动事务会增加线程开销，数据库因共享读取而锁定(具体跟数据库类型和事务隔离级别有关)。通常情况下，仅是读取数据时，不必设置只读事务而增加额外的系统开销。

### 二.事务传播模式

Propagation枚举了多种事务传播模式，部分列举如下：

1、REQUIRED(默认模式)：业务方法需要在一个容器里运行。如果方法运行时，已经处在一个事务中，那么加入到这个事务，否则自己新建一个新的事务。

2、NOT_SUPPORTED：声明方法不需要事务。如果方法没有关联到一个事务，容器不会为他开启事务，如果方法在一个事务中被调用，该事务会被挂起，调用结束后，原先的事务会恢复执行。

3、REQUIRESNEW：不管是否存在事务，该方法总汇为自己发起一个新的事务。如果方法已经运行在一个事务中，则原有事务挂起，新的事务被创建。

4、 MANDATORY：该方法只能在一个已经存在的事务中执行，业务方法不能发起自己的事务。如果在没有事务的环境下被调用，容器抛出例外。

5、SUPPORTS：该方法在某个事务范围内被调用，则方法成为该事务的一部分。如果方法在该事务范围外被调用，该方法就在没有事务的环境下执行。

6、NEVER：该方法绝对不能在事务范围内执行。如果在就抛例外。只有该方法没有关联到任何事务，才正常执行。

7、NESTED：如果一个活动的事务存在，则运行在一个嵌套的事务中。如果没有活动事务，则按REQUIRED属性执行。它使用了一个单独的事务，这个事务拥有多个可以回滚的保存点。内部事务的回滚不会对外部事务造成影响。它只对DataSourceTransactionManager事务管理器起效。

### 三.解决注解不回滚问题

1.  检查方法是不是public
2.  检查异常是不是unchecked异常
3.  如果是checked异常也想回滚的话，注解上写明异常类型即可
4.  @Transactional(rollbackFor=Exception.class)

### 四.记一次在开发过程中事务不生效的问题

将@Transactional配置在了Controller中，没有生效

applicationContext.xml配置如下：

```xml
<context:annotation-config/>
    <context:component-scan base-package="com.ksxing">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller" />
    </context:component-scan>
```

分析：应该是没有扫描到事务注解

实际开发中很可能Spring与Spring mvc配置文件解析的是分开的，你在applicationContext.xml里面开启了@transanction解释器，就会在applicationContext.xml配置的扫描包的时候把扫描到的@transanction这样的注解开启事务，然后Spring-mvc.xml也有扫描而且没有开启解释器就会把有事务功能的controller替换为没有事务功能的controller，@Transactional 就不起作用