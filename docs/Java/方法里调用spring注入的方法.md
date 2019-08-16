在静态方法里直接调用非静态方法是不行的。 
那如何调用spring注入的方法呢？ 

```
@Component 
public class AutoLoginUtil { 
    @Autowired 
    private IUserService userService; 

    private static AutoLoginUtil autoLoginUtil; 

    @PostConstruct 
    public void init() { 
        autoLoginUtil = this; 
        autoLoginUtil.userService = this.userService; 
    } 

    public static void autoLogin() { 
        autoLoginUtil.userService.queryUserAutoLogin(); 
    } 

} 
```


IUserService userService是spring注入的方法。 
主要是init()和@PostConstruct注解。



从Java EE5规范开始，Servlet中增加了两个影响Servlet生命周期的注解，@PostConstruct和@PreDestroy，这两个注解被用来修饰一个非静态的void（）方法。写法有如下两种方式：

@PostConstruct

public void someMethod(){}

或者

public @PostConstruct void someMethod(){}

被@PostConstruct修饰的方法会在服务器加载Servlet的时候运行，并且只会被服务器执行一次。PostConstruct在构造函数之后执行，init（）方法之前执行。PreDestroy（）方法在destroy（）方法知性之后执行