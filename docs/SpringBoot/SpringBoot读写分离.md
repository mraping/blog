## 思路
配置主从DataSource，通过Context结合AOP进行数据主从库的切换

## 开源项目
[SpringBoot-DynamicDataSource](https://github.com/suxiongwei/SpringBoot-DynamicDataSource)
- 通过切面根据不同的条件在执行数据库操作前切换数据源
- 该项目使用了一个可写数据源和多个只读数据源，为了减少数据库压力，使用轮循的方式选择只读数据源；考虑到在一个 Service 中同时会有读和写的操作，所以本应用使用 AOP 切面通过 DAO 层的方法名切换只读数据源；但这种方式要求数据源主从一致，并且应当避免在同一个 Service 方法中写入后立即查询，==如果必须在执行写入操作后立即读取，应当在 Service 方法上添加 @Transactional 注解以保证使用主数据源==

需要注意的是，使用 DAO 层切面后不应该在 Service 类层面上加 @Transactional 注解，而应该添加在方法上，这也是 Spring 推荐的做法

## 其它
也可以通过以下方式进行AOP切入
```
@Before("execution(* com.ganinfo.*.mapper..*.select*(..)) || execution(* com.ganinfo.*.mapper..*.get*(..))|| execution(* com.ganinfo.*.mapper..*.query*(..))")

@Before("execution(* com.ganinfo.*.mapper..*.insert*(..)) || execution(* com.ganinfo.*.mapper..*.update*(..))")
```
