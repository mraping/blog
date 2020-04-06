## AOP

AOP：面向切面编程（底层为动态代理）
注意需要打开这个注解：```@EnableAspectJAutoProxy```

AnnotationAwareAspectJAutoProxyCreator贯穿了AOP整个的实现，以下为调用链

AnnotationAwareAspectJAutoProxyCreator
​	AspectJAwareAdvisorAutoProxyCreator
​		AbstractAdvisorAutoProxyCreator
​			AbstractAutoProxyCreator
​				extends ProxyProcessorSupport implements SmartInstantiationAwareBeanPostProcessor, 				BeanFactoryAware

​				至此重点关注后置处理器（在bean的初始化前后做一些处理），自动装配BeanFactory，						     				SmartInstantiationAwareBeanPostProcessor：bean的后置处理器



AOP bean的装配：

```java
// Register bean processors that intercept bean creation.
registerBeanPostProcessors(beanFactory);
```

普通bean的装配：

```java
// Instantiate all remaining (non-lazy-init) singletons.
finishBeanFactoryInitialization(beanFactory);
```



1、AnnotationAwareAspectJAutoProxyCreator --> InstantiationAwareBeanPostProcessor --> 前后置处理

2、Calculator类--> finishBeanFactoryInitialization --> div方法 Give BeanPostProcessors a chance to return a proxy instead of the target bean instance

（AOP）单实例Calculator bean先实例化

```java
try {
	// Give BeanPostProcessors a chance to return a proxy instead of the target bean instance.
	Object bean = resolveBeforeInstantiation(beanName, mbdToUse);
	if (bean != null) {
		return bean;
	}
}
```



所有的bean都会跟一个processor



BeanWrapper：包装bean，提供统一的get/set



后置处理器注入到容器是有顺序的，优先实现PriorityOrdered，其次Ordered



AOP bean：

创建 --> 初始化 --> init  --> AnnotationAwareAspectJAutoProxyCreator

前置处理器：postProcessBeforeInitialization（return null）

后置处理器：postProcessAfterInitialization（拿到通知方法，创建增强的动态代理对象）



bean的创建流程:

refresh() --> registerBeanPostProcessors(beanFactory) -->  getBean() --> doGetBean() --> createBean() --> bean创建 --> bean初始化 --> 后置处理器工作 --> 完成


AnnotationAwareAspectJAutoProxyCreator创建完成之后，在所有bean创建之前会有一个拦截

比如：getBeanPostProcessors()就会包含AnnotationAwareAspectJAutoProxyCreator

```java
public Object applyBeanPostProcessorsBeforeInitialization(Object existingBean, String beanName)
      throws BeansException {

   Object result = existingBean;
   for (BeanPostProcessor processor : getBeanPostProcessors()) {
      Object current = processor.postProcessBeforeInitialization(result, beanName);
      if (current == null) {
         return result;
      }
      result = current;
   }
   return result;
}
```



```java
public AopProxy createAopProxy(AdvisedSupport config) throws AopConfigException {
		if (config.isOptimize() || config.isProxyTargetClass() || hasNoUserSuppliedProxyInterfaces(config)) {
			Class<?> targetClass = config.getTargetClass();
			if (targetClass == null) {
				throw new AopConfigException("TargetSource cannot determine target class: " +
						"Either an interface or a target is required for proxy creation.");
			}
			if (targetClass.isInterface() || Proxy.isProxyClass(targetClass)) {
				return new JdkDynamicAopProxy(config);
			}
			return new ObjenesisCglibAopProxy(config);
		}
		else {
			return new JdkDynamicAopProxy(config);
		}
	}

```

当调用AOP增强的类时：

调用getAdvicesAndadvisorsForBean()获取bean的通知方法 --> 得到由CGLIB增强后的对象 --> 当执行目标方法时，CGLIB对象就会调用之前保存好的切面通知



何时使用JDK还是CGLIB？
1）如果目标对象实现了接口（InvocationHanlder），默认情况下会采用JDK的动态代理实现AOP。

2）如果目标对象实现了接口，可以强制使用CGLIB实现AOP。

3）如果目标对象没有实现了接口，必须采用CGLIB库，Spring会自动在JDK动态代理和CGLIB之间转换。



注解可以理解为增强