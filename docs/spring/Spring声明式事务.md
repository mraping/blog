## 声明式事务

@Transactional

以方法为单位，进行事务控制；抛出异常，事务回滚

 @EnableTransactionManagement:使用此注解开启事务管理器

核心组件：InfrastructureAdvisorAutoProxyCreator
	1、注册

​	2、利用后置处理器机制在创建之后，包装对象，返回一个代理对象（增强），代理对象执行方法时，利用拦截器进行调用

​	

AnnotationTransactionAttributeSource:事务增强器用事务注解的信息，使用这个类解析事务注解

TransactionInterceptor:保存了事务属性信息，事务管理器 MethodInterceptor

当执行目标方法时：

​	执行拦截器链

​	事务拦截器：

​		1、获取事务相关属性

​		2、获取PlatformTransactionManager事务管理器

​	执行目标方法：

​		如果异常:completeTransactionAfterThrowing,利用事务管理器回滚操作

​		如果正常:利用事务管理器，提交事务

