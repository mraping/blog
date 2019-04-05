## 自定义权限注解
定义一个@interface类
```
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Access {

    String[] value() default {};

    String[] authorities() default {};

    String[] roles() default {};

}
```
@Target注解是标注这个类它可以标注的位置:</br>
常用的元素类型(ElementType):
```
public enum ElementType {
    /** Class, interface (including annotation type), or enum declaration */
    // TYPE类型可以声明在类上或枚举上或者是注解上
    TYPE,
    /** Field declaration (includes enum constants) */
    // FIELD声明在字段上
    FIELD,
    /** Method declaration */
    // 声明在方法上
    METHOD,
    /** Formal parameter declaration */
    // 声明在形参列表中
    PARAMETER,
    /** Constructor declaration */
    // 声明在构造方法上
    CONSTRUCTOR,
    /** Local variable declaration */
    // 声明在局部变量上
    LOCAL_VARIABLE,
    /** Annotation type declaration */
    ANNOTATION_TYPE,
    /** Package declaration */
    PACKAGE,
    /**
     * Type parameter declaration
     *
     * @since 1.8
     */
    TYPE_PARAMETER,
    /**
     * Use of a type
     *
     * @since 1.8
     */
    TYPE_USE
}
```
## 在方法上配置权限
```
@RestController
public class HelloController {

    @RequestMapping(value = "/admin", produces = MediaType.APPLICATION_JSON_UTF8_VALUE, method = RequestMethod.GET)
    // 配置注解权限, 允许身份为admin, 或者说允许权限为admin的人访问
    @Access(authorities = {"admin"})
    public String hello() {
        return "Hello, admin";
    }
}
```
## 自定义一个权限拦截器
自定义一个权限拦截器, 继承HandlerInterceptorAdapter类来编写权限逻辑
```
public class AuthenticationInterceptor extends HandlerInterceptorAdapter {

    // 在调用方法之前执行拦截
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 将handler强转为HandlerMethod, 前面已经证实这个handler就是HandlerMethod
        HandlerMethod handlerMethod = (HandlerMethod) handler;
        // 从方法处理器中获取出要调用的方法
        Method method = handlerMethod.getMethod();
        // 获取出方法上的Access注解
        Access access = method.getAnnotation(Access.class);
        if (access == null) {
        // 如果注解为null, 说明不需要拦截, 直接放过
            return true;
        }
        if (access.authorities().length > 0) {
            // 如果权限配置不为空, 则取出配置值
            String[] authorities = access.authorities();
            Set<String> authSet = new HashSet<>();
            for (String authority : authorities) {
            // 将权限加入一个set集合中
                authSet.add(authority);
            }
            // 这里我为了方便是直接参数传入权限, 在实际操作中应该是从参数中获取用户Id
            // 到数据库权限表中查询用户拥有的权限集合, 与set集合中的权限进行对比完成权限校验
            String role = request.getParameter("role");
            if (StringUtils.isNotBlank(role)) {
                if (authSet.contains(role)) {
                // 校验通过返回true, 否则拦截请求
                    return true;
                }
            }
        }
        // 拦截之后应该返回公共结果, 这里没做处理
        return false;
    }

}
```