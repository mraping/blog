## Spring 属性赋值

赋值方式

1. @Value
2. Spring EL表达式
3. properties



------

尽量用Autowired

@Qualifier 能保证优先加载

@Resource：JSR250



bean初始化过程：

容器启动加载配置类--> refresh()-->finishBeanFactoryInitialization-->getBean()-->doCreateBean()-->beanWrapper(对象的创建)-->populated(属性的赋值)-->initializate()-->processors



@Autowired Spring 支持required = false;

@Resource  JSR250

@Inject 



@Qualifier、@Primary 区别

@Primary 代表所有要注入xxx的bean