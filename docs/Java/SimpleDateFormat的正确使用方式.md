### 1、只在需要的时候创建新实例，不用static修饰

```java
public static String formatDate(Date date) throws ParseException{
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    return sdf.format(date);
}

public static Date parse(String strDate) throws ParseException{
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    return sdf.parse(strDate);
}

public static String formatDate(Date date) throws ParseException{    
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
 	  return	sdf.format(date);
}

public static Date parse(String strDate) throws ParseException{
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    return sdf.parse(strDate);
}
```

如上代码，仅在需要用到的地方创建一个新的实例，就没有线程安全问题，不过也加重了创建对象的负担，会频繁地创建和销毁对象，效率较低。

### 2、synchronized大法好

```java
private static final SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

public static String formatDate(Date date) throws ParseException{
    synchronized(sdf){
        return sdf.format(date);
    }
}

public static Date parse(String strDate) throws ParseException{
    synchronized(sdf){
        return sdf.parse(strDate);
    }
}
```

简单粗暴，synchronized往上一套也可以解决线程安全问题，缺点自然就是并发量大的时候会对性能有影响，线程阻塞。



### 3、ThreadLocal

```java
private static ThreadLocal<DateFormat> threadLocal = new ThreadLocal<DateFormat>(){
    @Override
    protected DateFormat initialValue(){
        return new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    }
};

public static Date parse(String dateStr) throws ParseException{
    return threadLocal.get().parse(dateStr);
}

public static String format(Date date){
    return threadLocal.get().format(date);
}
```

ThreadLocal可以确保每个线程都可以得到单独的一个SimpleDateFormat的对象，那么自然也就不存在竞争问题了。



### 4、基于JDK1.8的DateTimeFormatter

也是《阿里巴巴开发手册》给我们的解决方案，对之前的代码进行改造：

```java
private static final DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");

public static StringformatDate2(LocalDateTime date){
   return formatter.format(date);
}

public static LocalDateTime parse2(String dateNow){
   return LocalDateTime.parse(dateNow,formatter);  
}
```



### 参考文章：[还在使用 SimpleDateFormat？你的项目崩没？](https://mp.weixin.qq.com/s/CkCZIGUQB1h6auHh9mK4Ig)