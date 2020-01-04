Java中for each与正常for循环效率对比


循环ArrayList时，普通for循环比foreach循环花费的时间要少一点；循环LinkList时，普通for循环比foreach循环花费的时间要多很多。 
当我将循环次数提升到一百万次的时候，循环ArrayList，普通for循环还是比foreach要快一点；但是普通for循环在循环LinkList时，程序直接卡死。

结论：**需要循环数组结构的数据时，建议使用普通for循环，因为for循环采用下标访问，对于数组结构的数据来说，采用下标访问比较好。** 
**需要循环链表结构的数据**时，一定不要使用普通for循环，这种做法很糟糕，数据量大的时候有可能会导致系统崩溃。

原因：foreach使用的是迭代器

可以下标访问时，使用for，不能下标访问，需要指针访问时，使用for each。



实现RandomAccess接口的集合比如ArrayList，应当使用最普通的for循环而不是foreach循环来遍历

这是JDK推荐给用户的。JDK API对于RandomAccess接口的解释是：实现RandomAccess接口用来表明其支持快速随机访问，此接口的主要目的是允许一般的算法更改其行为，从而将其应用到随机或连续访问列表时能提供良好的性能。实际经验表明，**实现RandomAccess接口的类实例，假如是随机访问的，使用普通for循环效率将高于使用foreach循环；反过来，如果是顺序访问的，则使用Iterator会效率更高**。可以使用类似如下的代码作判断：

```java
if (list instanceof RandomAccess) {    
  for (int i = 0; i < list.size(); i++){}
} else {
    Iterator<?> iterator = list.iterable();    while (iterator.hasNext()) {        					iterator.next()    }
} 
```

foreach循环的底层实现原理就是迭代器Iterator，参见Java语法糖1：可变长度参数以及foreach循环原理。所以后半句”反过来，如果是顺序访问的，则使用Iterator会效率更高”的意思就是顺序访问的那些类实例，使用foreach循环去遍历。