### 1、Sql注入是怎么产生的？
 *  WEB开发人员无法保证所有的输入都已经过滤
 * 攻击者利用发送给SQL服务器的输入数据构造可执行的SQL代码
 * 数据库未做相应的安全配置
### 2、如何寻找Sql注入？
 * 识别web应用中的所有输入点
 * 了解哪些类型的请求会出发异常
 * 检测服务器响应中的异常
### 3、如何进行SQL注入攻击？
 *  数字注入
	 比如：用or 前一个条件不匹配后一个条件完全匹配。比如id = -1 or 1 = 1
 * 字符串注入
	 比如：用 **‘** 闭合查询，然后后面添加#，用来将后面的语句注释掉
### 4、如何预防SQL注入？
 * 严格检查输入变量的类型和格式
 * 过滤和转义特殊字符
 * 利用mysql的预编译机制
### 5、预编译的相关知识
  &emsp; sql注入只对sql语句的准备(编译)过程有破坏作用，而PreparedStatement已经准备(编译)好了,执行阶段只是把输入串作为数据处理,而不再对sql语句进行解析、准备(编译),因此也就避免了sql注入问题。
 
 * 什么是预编译语句
 预编译语句PreparedStatement 是java.sql中的一个接口，它是Statement的子接口。通过Statement对象执行SQL语句时，需要将SQL语句发送给DBMS，由DBMS首先进行编译后再执行。预编译语句和Statement不同，在创建PreparedStatement 对象时就指定了SQL语句，该语句立即发送给DBMS进行编译。当该编译语句被执行时，DBMS直接运行编译后的SQL语句，而不需要像其他SQL语句那样首先将其编译。
 * 什么时候使用预编译语句
 一般是在需要反复使用一个SQL语句时才使用预编译语句，预编译语句常常放在一个fo r或者while循环里面使用，通过反复设置参数从而多次使用该SQL语句。为了防止SQL注入漏洞，在某些数据操作中也使用预编译语句。
 * 为什么使用预编译语句
 预编译机制除了在开篇提到的可以防止SQL注入外，还有一下两方面的优点：
 a、**提高效率**：数据库处理一个SQL语句，需要完成解析SQL语句、检查语法和语义以及生成代码，一般说来，处理时间要比执行语句所需要的时间长。预编译语句在创建的时候已经是将指定的SQL语句发送给了DBMS，完成了解析、检查、编译等工作。因此，当一个SQL语句需要执行多次时，使用预编译语句可以减少处理时间，提高执行效率。
 b、**提高代码的可读性和可维护性**：将参数与SQL语句分离出来，这样就可以方便对程序的更改和扩展，同样，也可以减少不必要的错误。
### 6、防止SQL注入以及预编译机制在mybatis中的应用
#### '#' 与 '$'区别
&emsp;动态 SQL是 mybatis 的强大特性之一，也是它优于其他 ORM 框架的一个重要原因。mybatis 在对 sql 语句进行预编译之前，会对 sql 进行动态解析，解析为一个 BoundSql 对象，也是在此处对动态 SQL 进行处理的。
在动态 SQL 解析阶段，**#{ }** 和 **${ }** 会有不同的表现：
&emsp;**#{}** 解析为一个 JDBC 预编译语句（prepared statement）的参数标记符。
&emsp;例如，sqlMap 中如下的 sql 语句
```sql
select * from user where name = #{name};
```
&emsp;解析为：
```sql
select * from user where name = ?;
```
&emsp;一个 **#{ }** 被解析为一个参数占位符 **?** 。
&emsp;而，**${ }** 仅仅为一个纯碎的 **string 替换**，在动态 SQL 解析阶段将会进行**变量替换**
&emsp;例如，sqlMap 中如下的 sql
```sql
select * from user where name = ${name};
```
&emsp;当我们传递的参数为 "ruhua" 时，上述 sql 的解析为：
```sql
select * from user where name = "ruhua";
```
&emsp;预编译之前的 SQL 语句已经不包含变量 name 了。
&emsp;综上所得， **${ }** 的变量的替换阶段是在动态 SQL 解析阶段，而 **#{ }**的变量的替换是在 DBMS 中。
### 7、mybatis中‘#’ 与 ‘$’的用法
#### 能使用 #{ } 的地方就用 #{ }
首先这是为了性能考虑的，相同的预编译 sql 可以重复利用。
其次， **${ }** 在预编译之前已经被变量替换了，这会存在 *sql 注入问题* 。例如，如下的 sql，
```sql
select * from ${tableName} where name = #{name} 
```
假如，我们的参数 tableName 为 *user; delete user; --* ，那么 SQL 动态解析阶段之后，预编译之前的 sql 将变为
```sql
select * from user; delete user; -- where name = ?;
```
**--** 之后的语句将作为注释，不起作用，因此本来的一条查询语句偷偷的包含了一个删除表数据的 SQL！
2、表名作为变量时，必须使用 ${ }
这是因为，表名是字符串，使用 sql 占位符替换字符串时会带上单引号 '' ，这会导致 sql 语法错误，例如：
```sql
select * from #{tableName} where name = #{name};
```
预编译之后的sql 变为：
```sql
select * from ? where name = ?;
```
假设我们传入的参数为 tableName = "user" , name = "ruhua"，那么在占位符进行变量替换后，sql 语句变为
```sql
select * from 'user' where name='ruhua';
```
上述 sql 语句是存在语法错误的，表名不能加单引号 '' （注意，反引号 ``是可以的）。
### 8、mybatis sql 动态解析
mybatis 在调用 connection 进行 sql 预编译之前，会对sql语句进行动态解析，动态解析主要包含如下的功能：
* 占位符的处理
* 动态sql的处理
* 参数类型校验
### 9、以上内容由本人学习慕课网课程：[web安全之SQL注入](https://www.imooc.com/learn/883 )及其它互联网资源整理而成。如有侵权请及时联系我。
 