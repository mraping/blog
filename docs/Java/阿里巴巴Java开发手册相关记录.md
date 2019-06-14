【强制】SimpleDateFormat 是线程不安全的类，一般不要定义为 static 变量，如果定义为static，必须加锁，或者使用 DateUtils 工具类。

正例：注意线程安全，使用 DateUtils。亦推荐如下处理：
```
private static final ThreadLocal<DateFormat> df = new ThreadLocal<DateFormat>() {
    @Override
    protected DateFormat initialValue() {
        return new SimpleDateFormat("yyyy-MM-dd");    
    }
};
```

说明：如果是 JDK8 的应用，可以使用 Instant 代替 Date，LocalDateTime 代替 Calendar，DateTimeFormatter 代替 SimpleDateFormat，官方给出的解释：simple beautiful strong immutable thread-safe。



【强制】所有的 POJO 类属性必须使用包装数据类型。

【强制】RPC 方法的返回值和参数必须使用包装数据类型。

【推荐】所有的局部变量使用基本数据类型。

【强制】定义 DO/DTO/VO 等 POJO 类时，不要设定任何属性默认值。
反例：POJO 类的 gmtCreate 默认值为 new Date();但是这个属性在数据提取时并没有置入具体值，在更新其它字段时又附带更新了此字段，导致创建时间被修改成当前时间。

【强制】构造方法里面禁止加入任何业务逻辑，如果有初始化逻辑，请放在 init 方法中。

【推荐】使用索引访问用 String 的 split 方法得到的数组时，需做最后一个分隔符后有无内容的检查，否则会有抛 IndexOutOfBoundsException 的风险。
说明：
String str = "a,b,c,,";
String[] ary = str.split(",");
// 预期大于 3，结果是 3
System.out.println(ary.length); 

【推荐】setter 方法中，参数名称与类成员变量名称一致，this.成员名 = 参数名。在
getter/setter 方法中，不要增加业务逻辑，增加排查问题的难度。

【强制】ArrayList的subList结果不可强转成ArrayList，否则会抛出ClassCastException异常，即 java.util.RandomAccessSubList cannot be cast to java.util.ArrayList.
说明：subList 返回的是 ArrayList 的内部类 SubList，并不是 ArrayList ，而是ArrayList 的一个视图，对于 SubList 子列表的所有操作最终会反映到原列表上。

【强制】使用工具类 Arrays.asList()把数组转换成集合时，不能使用其修改集合相关的方
法，它的 add/remove/clear 方法会抛出 UnsupportedOperationException 异常。
说明：asList 的返回对象是一个 Arrays 内部类，并没有实现集合的修改方法。Arrays.asList体现的是适配器模式，只是转换接口，后台的数据仍是数组。
String[] str = new String[] { "you", "wu" };
List list = Arrays.asList(str);
第一种情况：list.add("yangguanbao"); 运行时异常。
第二种情况：str[0] = "gujin"; 那么 list.get(0)也会随之修改。

【强制】表达是与否概念的字段，必须使用 is_xxx 的方式命名，数据类型是 unsigned tinyint（ 1 表示是，0 表示否）。
说明：任何字段如果为非负数，必须是 unsigned。
正例：表达逻辑删除的字段名 is_deleted，1 表示删除，0 表示未删除。

【强制】小数类型为 decimal，禁止使用 float 和 double。
说明：float 和 double 在存储的时候，存在精度损失的问题，很可能在值的比较时，得到不正确的结果。如果存储的数据范围超过 decimal 的范围，建议将数据拆成整数和小数分开存储。

【推荐】单表行数超过 500 万行或者单表容量超过 2GB，才推荐进行分库分表。
说明：如果预计三年后的数据量根本达不到这个级别，请不要在创建表时就分库分表。

【强制】业务上具有唯一特性的字段，即使是多个字段的组合，也必须建成唯一索引。
说明：不要以为唯一索引影响了 insert 速度，这个速度损耗可以忽略，但提高查找速度是明显的；另外，即使在应用层做了非常完善的校验控制，只要没有唯一索引，根据墨菲定律，必然有脏数据产生。

【强制】超过三个表禁止 join。需要 join 的字段，数据类型必须绝对一致；多表关联查询时，保证被关联的字段需要有索引。
说明：即使双表 join 也要注意表索引、SQL 性能。

【强制】不要使用 count(列名)或 count(常量)来替代 count(*)，count(*)是 SQL92 定义的标准统计行数的语法，跟数据库无关，跟 NULL 和非 NULL 无关。
说明：count(*)会统计值为 NULL 的行，而 count(列名)不会统计此列为 NULL 值的行。

【强制】当某一列的值全是 NULL 时，count(col)的返回结果为 0，但 sum(col)的返回结果为NULL，因此使用 sum()时需注意 NPE 问题。
正例：可以使用如下方式来避免 sum 的 NPE 问题：SELECT IF(ISNULL(SUM(g)),0,SUM(g)) FROM table;

【强制】在代码中写分页查询逻辑时，若 count 为 0 应直接返回，避免执行后面的分页语句

【推荐】in 操作能避免则避免，若实在避免不了，需要仔细评估 in 后边的集合元素数量，控制在 1000 个之内。

【强制】POJO 类的布尔属性不能加 is，而数据库字段必须加 is_，要求在 resultMap 中进行
字段与属性之间的映射。
说明：参见定义 POJO 类以及数据库字段定义规定，在中增加映射，是必须的。在 MyBatis Generator 生成的代码中，需要进行对应的修改。