Spring容器中的Bean是否线程安全，容器本身并没有提供Bean的线程安全策略，因此可以说Spring容器中的Bean本身不具备线程安全的特性，但是具体还是要结合具体scope的Bean去研究。

Spring 的 bean 作用域（scope）类型

- singleton:单例，默认作用域。
- prototype:原型，每次创建一个新对象。
- request:请求，每次Http请求创建一个新对象，适用于WebApplicationContext环境下。
- session:会话，同一个会话共享一个实例，不同会话使用不用的实例。
- global-session:全局会话，所有会话共享一个实例。

线程安全这个问题，要从单例与原型Bean分别进行说明。

- 原型Bean
  对于原型Bean,每次创建一个新对象，也就是**线程之间并不存在Bean共享**，自然是不会有线程安全的问题。

- 单例Bean
  对于单例Bean,所有线程**都共享一个单例实例Bean,因此是存在资源的竞争。**

如果单例Bean,是一个**无状态Bean**，也就是线程中的操作不会对Bean的成员执行查询以外的操作，那么这个单例Bean是线程安全的。比如Spring mvc 的 Controller、Service、Dao等，这些Bean大多是无状态的，只关注于方法本身。

对于有状态的bean，Spring官方提供的bean，一般提供了通过**ThreadLocal**去解决线程安全的方法，比如RequestContextHolder、TransactionSynchronizationManager、LocaleContextHolder等。

- 使用ThreadLocal的好处
  使得多线程场景下，多个线程对这个单例Bean的成员变量并不存在资源的竞争，因为ThreadLocal为每个线程保存线程私有的数据。这是一种以空间换时间的方式。

当然也可以通过加锁的方法来解决线程安全，这种以时间换空间的场景在高并发场景下显然是不实际的。
