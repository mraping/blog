## IOC源码分析

IOC初始化之后是不可以在运行期注册组件的

IOC创建流程：

1. prepareRefresh()：刷新前的预处理

   1）initPropertySources()：初始化一些属性信息，子类自定义个性化的属性设置方法

   2）getEnvironment().validateRequiredProperties()：检验属性的合法性等

   3）this.earlyApplicationEvents = new LinkedHashSet<>()：保存容器中一些早期的事件

2. obtainFreshBeanFactory()：获取beanFactory

   refreshBeanFactory()刷新、创建beanFactory

   ```java
   /**
   	 * Create a new GenericApplicationContext.
   	 * @see #registerBeanDefinition
   	 * @see #refresh
   	 */
   	public GenericApplicationContext() {
   		this.beanFactory = new DefaultListableBeanFactory();
   	}
   ```

3. prepareBeanFactory(beanFactory)：beanFactory的预准备工作（以上创建了beanFactory，现在对beanFactory对象进行一些属性设置）

   1）设置beanFactory的类加载器，支持表达式解析器

   2）添加部分BeanPostProcessor（ApplicationContextAwareProcessor）

   3）设置忽略的自动装配的接口EnvironmentAware、EmbeddedValueResolverAware等

   4）注册可以解析的自动装配：能在任何组件中自动注入

   ​		BeanFactory、ResourceLoader...

   5）添加BeanPostProcessor（ApplicationListenerDetector）

   6）添加编译时的AspectJ

   7）给beanFactory中注册一些能用的组件：

   ​		environment

   ​		systemProperties

   ​		systemEnvironment

4. postProcessBeanFactory(beanFactory)：BeanFactory准备完成之后的后置处理工作

   至此BeanFactory的创建和初始化功能已经完成

5. invokeBeanFactoryPostProcessors(beanFactory)：执行BeanFactoryPostProcessors的方法

   两个接口：BeanFactoryPostProcessor,BeanDefinitionRegistryPostProcessor

   BeanFactoryPostProcessor的方法:

   ​	先执行BeanDefinitionRegistoryPostProcessor

   ​	1) 获取实现了PriorityOrdered的优先处理（First, register the BeanPostProcessors that implement PriorityOrdered.）

   ​	2)获取bean

   InstantiationAwareBeanPostProcessor

   SmartInstantiationAwareBeanPostProcessor  -->  beanPostProcessor

   拦截bean的创建，后置处理器里进行增强，动态代理

6. initMessageSource()：标签国际化资源

   初始化MessageSource组件（国际化功能：消息解析，消息绑定）messageSource：判断容器中是否有ID为messageSource,类型为MessageSource组件，如果没有的话，自己创建```new DelegatingMessageSource();```并注册到beanFactory：```beanFactory.registerSingleton(MESSAGE_SOURCE_BEAN_NAME``` this.messageSource);本质就是把bean放在一个singletonObject --> ```this.singletonObjects.put(beanName, singletonObject);```

7. initApplicationEventMulticaster()：初始化事件派发器

   获取beanFactory --> ```getBeanFactory()```

   判断当前beanFactory是否有bean名为applicationEventMulticaster

   通过SimpleApplicationEventMulticaster来创建bean

8. onRefresh()：留给子容器（子类），子类可以重写这个方法，在容器刷新的时候可以自定义逻辑

9. registerListeners()：将容器所有项目里的ApplicationListener注册进来，事件派发

10. finishBeanFactoryInitialization(beanFactory)：将容器中所有项目的单实例bean(非懒加载)进行初始化

    beanFactory.preInstantiateSingletons()：单实例bean(非懒加载)初始化

    ​	RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName)：获取bean的定义信息，依次进	行创建和初始化

    ​	doGetBean

    ​	Object sharedInstance = getSingleton(beanName)：先获取map中缓存的单例bean，如果第二次来拿，	就直接从缓存中（singletonObjects）来拿

    ​	markBeanAsCreated(beanName)：标记当前bean已经被创建

    ​		--> ```this.alreadyCreated.add(beanName);```

    ​	mbd.getDependsOn()：获取当前bean依赖的其它bean

    ​	Object bean = resolveBeforeInstantiation(beanName, mbdToUse)：让BeanPostProcessor尝试返回一	个代理对象

    ​	Object beanInstance = doCreateBean(beanName, mbdToUse, args)：创建bean实例

    ​	populateBean(beanName, mbd, instanceWrapper)：bean属性赋值

    ​	if (bp instanceof InstantiationAwareBeanPostProcessor)：后置处理器赋值

    ​		postProcessAfterInstantiation调用后置处理方法对bean进行定制处理

    ​		applyPropertyValues(beanName, mbd, bw, pvs)：设值

    initializeBean:bean前后置处理器执行的地方，AOP动态增强的入口