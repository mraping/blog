# JVM 调优

- -XX:AutoBoxCacheMax
- -XX:+AlwaysPreTouch
- -Xmx, -Xms

## 性能调优参数
### -XX:AutoBoxCacheMax

JAVA进程启动的时候,会加载rt.jar这个核心包的,rt.jar包里的Integer自然也是被加载到JVM中,Integer里面有一个IntegerCache缓存,如下:

```
private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];

        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                int i = parseInt(integerCacheHighPropValue);
                i = Math.max(i, 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
            }
            high = h;

            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);
        }

        private IntegerCache() {}
}
```


IntegerCache有一个静态代码块,JVM在加载Integer这个类时,会优先加载静态的代码。当JVM进程启动完毕后, -128 ~ +127 范围的数字会被缓存起来,调用valueOf方法的时候,如果是这个范围内的数字,则直接从缓存取出。
超过这个范围的,就只能构造新的Integer对象了。

```
public static Integer valueOf(int i) {
        assert IntegerCache.high >= 127;
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
}
```
因此可以根据实际情况把AutoBoxCacheMax的值设置的大写,推荐的

-XX:AutoBoxCacheMax=20000

### -XX:+AlwaysPreTouch

JAVA进程启动的时候,虽然我们可以为JVM指定合适的内存大小,但是这些内存操作系统并没有真正的分配给JVM,而是等JVM访问这些内存的时候,才真正分配,这样会造成以下问题。

1. GC的时候,新生代的对象要晋升到老年代的时候,需要内存,这个时候操作系统才真正分配内存,这样就会加大young gc的停顿时间;
2. 可能存在内存碎片的问题。

可以在JVM启动的时候,配置

> -XX:+AlwaysPreTouch

参数,这样JVM就会先访问所有分配给它的内存,让操作系统把内存真正的分配给JVM.后续JVM就可以顺畅的访问内存了。


## 内存参数
-Xmx, -Xms

这两个一般都是设置4个g
