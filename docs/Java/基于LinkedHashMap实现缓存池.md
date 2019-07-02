合理的利用缓存池可以降低内存的消耗，提升系统的性能。

但是如果放入太多的对象到缓存池中，反而会造成内存的严重消耗，同时由于缓存池中一直持有这些对象的引用，从而造成Full GC增多，对于这种情况应该合理控制缓存池的大小

控制缓存池大小的问题在于当到达缓存池的最大容量后，如果要加入新的对象该如何处理，有一些经典的缓存失效算法来清除缓存池中的对象，例如FIFO、LRU、LFU等。采用这些算法可控制缓存池中的对象数目，避免缓存池中的对象数量无限上涨。
在Java中，基于LinkedHashMap可简单实现支持FIFO和LRU策略 的CachePool，代码如下：

```java
public class ObjectCachePool<K, V> {
    private static final int FIFO_POLICY = 1;

    private static final int LRU_POLICY = 2;

    private static final int DEFAULT_SIZE = 10;

    private Map<K, V> cacheObjects;

    public ObjectCachePool() {
        this(DEFAULT_SIZE);
    }

    public ObjectCachePool(final int size) {
        this(size, FIFO_POLICY);
    }


    public ObjectCachePool(final int size, final int policy) {
        switch (policy) {
            case FIFO_POLICY:
                cacheObjects = new LinkedHashMap<K, V>(size) {
                    private static final long serialVersionUID = 1L;

                    @Override
                    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
                        return size() > size;
                    }
                };
                break;
            case LRU_POLICY:
                /**
                 * 设置accessOrder为true，会触发调用afterNodeAccess方法
                 * 每次调用get方法时都会调用afterNodeAccess方法，将我们访问的Node移动到最后，使之成为尾部节点，从而改变了数据在LinkedHashMap中的存储顺序。
                 */
                cacheObjects = new LinkedHashMap<K, V>(size, 0.75f, true) {
                    private static final long serialVersionUID = 1L;

                    @Override
                    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
                        return size() > size;
                    }
                };
                break;
            default:
                throw new IllegalArgumentException("Unknown policy:" + policy);
        }
    }

    public void put(K key,V value){
        cacheObjects.put(key,value);
    }

    public V get(K k){
        return cacheObjects.get(k);
    }

    public void remove(K key){
        cacheObjects.remove(key);
    }

    public void clear(){
        cacheObjects.clear();
    }
}

```

