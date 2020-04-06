Spring-->IOC-->DI

@Configuration相当于一个配置文件，例如bean.xml

AnnotationConfigApplication

bean都是先定义，然后到Wrapper中进行实例化



@Configuration

@ComponentScan(value="com.xxx", excludeFilters={@Filter(type=FilterType.ANNOTATION, classes={Controller.class})}, useDefaultFilters=false)

指定扫描范围，并且可以做一些过滤

当type=FilterType.CUSTOM时可以自定义过滤规则，具体实现：实现TypeFilter 重写match方法



@Scope扫描规则：

1、单实例bean

2、多实例bean

3、

单实例bean与多实例bean的区别：

对IOC容器来说

多实例：仅当bean被使用的时候才创建

单实例：创建IOC容器的时候bean就被创建



看@Scope的源码

@Lazy





@Service也是引用了@Component，相当于service是Component的子类

IOC就是对bean进行管理：bean注册、实例化、管理



条件注册：实现 Condition （MyCondition），在需要进行条件注册的bean上加注解@Conditional(MyCondition.class)即可实现条件注册

ConditionContext:判断条件可以使用的上下文



BeanFactory和FactoryBean的区别：

FactoryBean：注册机制，可以把Java实例bean通过FactoryBean注入到容器中

BeanFactory：从容器中获取到实例化后的bean



给容器注册组件的方式：

1. @Bean:导入第三方的类或包的组件

2. 包扫描+组件的注解（@ComponentScan：@Controller,@Service,@Responsitory,@Component）

3. @Import:快速给容器导入一个组件 注意：@Bean有点简单，构造的,

   3.1. 具体的使用：在类上注解@Import(value = {XXX.class})

   容器会自动注册这个组件，bean的id为全类名：如com.xx.xx

   3.2. ImportSelector:是一个接口，返回需要导入到容器组件的全类名数组

   具体的使用：XXXSelector implement ImportSelector --> @Import(value = {XXX.class，XXXSelector.class})

   3.3. ImportBeanDefintionRegister:可以手动添加组件到IOC容器，所有bean的注册可以使用BeanDefinitionRegistry

   具体的使用：XXXBeanDefintionRegister implement ImportBeanDefintionRegister --> @Import(value = {XXX.class，XXXSelector.class,XXXBeanDefintionRegister.class})

   3.4. FactoryBean：使用Spring的FactoryBean（工厂bean）进行注册

   具体的使用：实现FactoryBean接口，重写三个方法：

bean最终放在一个map里面：beanDefinitionMap

```java
private final Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap(256);
```



FactoryBean接口实现



bean的生命周期

bean的生命周期是指：创建-->初始化-->销毁 的过程

我们可以自定义bean初始化和销毁方法

容器在bean进行到当前生命周期的时候，来调用自定义的初始化和销毁方法

```xml
<bean id = "xxx" class = "" Init-method = "xxx" destory-method= "">
```

对于单实例的bean，可以正常调用初始化和销毁方法

对于多实例的bean，容器只负责初始化，但不会管理bean，容器关闭时不会调用销毁方法

缓存中的单例全部销毁

```java
// Destroy all cached singletons in the context's BeanFactory.
destroyBeans();
```

-->

```java
this.containedBeanMap.clear();
this.dependentBeanMap.clear();
this.dependenciesForBeanMap.clear();

```

初始化源码跟踪

```java
// Instantiate all remaining (non-lazy-init) singletons.
finishBeanFactoryInitialization(beanFactory);
```

```java
// Create bean instance.
if (mbd.isSingleton()) {
  sharedInstance = getSingleton(beanName, () -> {
    try {
      return createBean(beanName, mbd, args);
    }
    catch (BeansException ex) {
      // Explicitly remove instance from singleton cache: It might have been put there
      // eagerly by the creation process, to allow for circular reference resolution.
      // Also remove any beans that received a temporary reference to the bean.
      destroySingleton(beanName);
      throw ex;
    }
  });
  bean = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
}

```

自定义的初始化和销毁方法

1. ```<bean id = "xxx" class = "" Init-method = "xxx" destory-method= "">```

2. ```@Bean(initMethod = "init", destroyMethod = "destroy")```

3. ```implements InitializingBean, DisposableBean ```

4. 使用JSR250规则定义的（Java规范）两个注解来实现

   @PostConstruct：在bean创建完成，且属于赋值完成之后进行初始化，属于JDK规范的注解

   @PreDestroy：在bean将被移除之前进行通知，在容器销毁之前进行清理工作



BeanPostProcessor

bean的后置处理器，在bean初始化之前调用进行拦截，在bean初始化前后进行一些处理工作

使用BeanPostProcessors如何控制bean的生命周期？

> 重写BeanPostProcessor接口的两个方法即可
>
> 1. postProcessBeforeInitialization()
> 2. postProcessAfterInitialization()

Spring容器创建的时候：

创建并初始化相关的后置处理器-->创建bean

## 容器对比及总结

ApplicationContextAware接口获取IOC容器

AnnotationConfigApplicationContext加载配置文件获取的容器

总结：

把Spring底层的组件可以注入到自定义的bean中，ApplicationContextAware是利用ApplicationContextAwareProcessor来处理的，其它XXXAware也类似，都有相关的Processor来处理

比如ApplicationContextAware是使用ApplicationContextProcessor后置处理器来处理的



@Value赋值计算的原理：

实现EmbeddedValueResolverAware接口```implements EmbeddedValueResolverAware```

重写 setmbeddedValueResolver方法，参数为StringValueResolver

```java
@Override
public void setEmbeddedValueResolver(StringValueResolver resolver) {
  String osName = resolver.resolveStringValue("${os.name}");
  System.out.println("StringValueResolver osName:" + osName);
}
```

## Spring扩展组件

BeanFactoryPostProcessor:beanFactory的后置处理器

BeanDifinitionPostProcessor:bean定义的后置处理器



