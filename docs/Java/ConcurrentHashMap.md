## 参考资料:https://github.com/Snailclimb/JavaGuide/blob/master/Java%E7%9B%B8%E5%85%B3/%E8%BF%99%E5%87%A0%E9%81%93Java%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6%E9%9D%A2%E8%AF%95%E9%A2%98%E5%87%A0%E4%B9%8E%E5%BF%85%E9%97%AE.md
## JDK1.7
- 关键是Segment
- ConcurrentHashMap是一个二级哈希表。在一个总的哈希表下面，有若干个子哈希表。
- ConcurrentHashMap优势就是采用了锁分段技术，每一个 Segment 就好比一个自治区，Segment之间互不影响。
- Segment的写入是需要上锁的，因此对同一Segment的并发写入会被阻塞。
- ConcurrentHashMap当中每个Segment各自持有一把锁。在保证线程安全的同时**降低了锁的粒度**，让并发操作效率更高。
- ConcurrentHashMap在对Key求Hash值的时候，为了实现Segment均匀分布，进行了两次Hash。

## JDK1.8
ConcurrentHashMap取消了Segment分段锁，采用CAS和synchronized来保证并发安全。数据结构跟HashMap1.8的结构类似，数组+链表/红黑二叉树。

synchronized只锁定当前链表或红黑二叉树的首节点，这样只要hash不冲突，就不会产生并发，效率又提升N倍。
