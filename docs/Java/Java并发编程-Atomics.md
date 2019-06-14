## Java中的13个原子操作类

当程序更新一个变量时，如果多线程同时更新这个变量，可能得到期望之外的值，比如变量i=1, A线程更新i+1, B线程也更新i+1, 经过两个线程操作之后可能i不等于3,而是等于2。因为A和B线程在更新变量i的时候拿到的都是1,这就是线程不安全的更新操作，通常我们会使用synchronized来解决这个问题，synchronized会保 证多线程不会同时更新变量i。

而Java从JDK 1.5开始提供了java.uti.concurrent.atomic包(以下简称Atomic包)，这个包中的原子操作类提供了一种用法简单、性能高效、线程安全地更新一个变量的方式。
因为变量的类型有很多种，所以在Atomic包里一共提供了13个类，属于4种类型的原子更新方式，分别是原子更新基本类型、原子更新数组、原子更新引用和原子更新属性(字段)。
Atomic包里的类基本都是使用Unsafe实现的包装类。

### 1.原子更新基本类型类

使用原子的方式更新基本类型，Atomic包提供了以下3个类。

- AtomicBoolean:原子更新布尔类型。

- Atomiclnteger:原子更新整型。

- AtomicLong:原子更新长整型。

以上3个类提供的方法几乎一模-样，所以本节仅以AtomicInteger为例进行讲解，AtomicInteger的常用方法如下。
- int addAndGet (int delta) :以原子方式将输入的数值与实例中的值(AtomicInteger里的value) 相加，并返回结果。
- boolean compareAndSet (int expect, int update) :如果输入的数值等于预期值，则以原子方式将该值设置为输入的值。
- int getAndIncrement():以原子方式将当前值加1，注意，这里返回的是自增前的值。
- void lazySet (int newValue) : 最终会设置成newValue,使用lazySet设置值后， 可能导致其他线程在之后的一小段时间内还是可以读到旧的值。
- int getAndSet (int newValue) :以原子方式设置为newValue的值，并返回旧值。



### 2.原子更新数组
通过原子的方式更新数组里的某个元素，Atomic包提供了以下4个类。

- AtomiclntegerArray:原子更新整型数组里的元素。
- AtomicLongArray:原子更新长整型数组里的元素。
- AtomicReferenceArray:原子更新引用类型数组里的元素。
- AtomiclntegerArray类主要是提供原子的方式更新数组里的整型，其常用方法如下。
  - int addAndGet (inti, int delta) :以原子方式将输入值与数组中索引的元素相加。
  - boolean compareAndSet (int i, int expect, int update) :如果当前值等于预期值，则以原子方式将数组位置i的元素设置成update值。



### 3.原子更新引用类型

原子更新基本类型的Atomiclnteger,只能更新一个変量，如果要原子更新多个変量，就需要使用这个原子更新引用类型提供的类。Atomic包提供 了以下3个类。

- AtomicReference:原子更新引用类型。
- AtomicReferenceFieldUpdater:原子更新引用类型里的字段。
- AtomicMarkableReference:原子更新带有标记位的引用类型。可以原子更新一个布尔类型的标记位和引用类型。枸造方法是AtomicMarkableReference (V initialRef, boolean initialMark),



### 4.原子更新字段类

如果需原子地更新某个类里的某个字段时，就需要使用原子更新字段类，Atomic包提供了以下3个类进行原子字段更新。

- AtomicIntegerFieldUpdater:原子更新整型的字段的更新器。

- AtomicLongFieldUpdater:原子更新长整型字段的更新器。

- AtomicStampedReference:原子更新带有版本号的引用类型。该类将整数值与引用关联起来，可用于原子的更新数据和数据的版本号，可以解决使用CAS进行原子更新时可能出现的ABA问题。


要想原子地更新字段类需要两步。第一步， 因为原子更新字段类都是抽象类，每次使用的时候必须使用静态方法newUpdater()创建一个更新器， 并且需要设置想要更新的类和属性。第二步，更新类的字段(属性)必须使用public volatile修饰符。