## SpringMVC与Servlet3.0

Web的三大组件

- Servlet
- Filter
- Listener

这些组件经常时在web.xml中

SpringMVC核心类：DispatcherServlet

### Servlet3.0

要求tomcat7以上

@WebServlet("/xxx")



Servlet3.0 - ServletContainerInitializer注册JAVA组件:http://www.voidcn.com/article/p-spnncpgu-brp.html

需要在META-INF中配置初始化时指定的组件

META-INF/services/javax.servlet.ServletContextInitializer



SpringMVC可以看成Struts的简化版

### SpringMVC

引入mvc的jar包会相应的引入beans、aop、context、core等jar包

Spring-web-xxx.jar的META-INF/services/下面就有对应的javax.servlet.ServletContextInitializer

```java
org.springframework.web.SpringServletContainerInitializer
```

核心类：WebApplicationInitializer

DispatcherServlet:

​	Servlet WebApplicationContext:Controller、HandlerMapping、ViewResolver

​	Root WebApplicationContext:Service、Repositories