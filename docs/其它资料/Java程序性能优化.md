1. 避免在循环条件中使用复杂表达式
2. 为'vectors' 和 'hashtables'定义初始大小 
> jvm为vector扩充大小的时候需要重新创建一个更大的数组，将原原先数组中的内容复制过来，最后，原先的数组再被回收。可见vector容量的扩大是一个颇费时间的事。 
通常，默认的10个元素大小是不够的。你最好能准确的估计你所需要的最佳大小。
3. 在finally块中关闭stream 
>程序中使用到的资源应当被释放，以避免资源泄漏。这最好在finally块中去做。不管程序执行的结果如何，finally块总是会执行的，以确保资源的正确关闭。
4. 使用'system.arraycopy ()'代替通过来循环复制数组
5. 避免不需要的instanceof操作
6. 如果只是查找单个字符的话，用charat()代替startswith() 
7. 使用移位操作来代替'a / b'操作 
8. 在字符串相加的时候，使用 ' ' 代替 " "，如果该字符串只有一个字符的话 
9. 不要在循环中调用synchronized(同步)方法 
10. 将try/catch块移出循环 
11. 对于boolean值，避免不必要的等式判断 
12. 对于常量字符串，用'string' 代替 'stringbuffer' 
13. 使用条件操作符代替"if (cond) a = b; else a = c;" 结构 
14. 不要在循环体中实例化变量 
15. 确定 StringBuffer的容量
```
StringBuffer 的构造器会创建一个默认大小(通常是16)的字符数组。在使用中，如果超出这个大小，就会重新分配内存，创建一个更大的数组，并将原先的数组复制过来，再 丢弃旧的数组。在大多数情况下，你可以在创建 StringBuffer的时候指定大小，这样就避免了在容量不够的时候自动增长，以提高性能。
```
16. 尽可能的使用栈变量
```
如果一个变量需要经常访问，那么你就需要考虑这个变量的作用域了。static? local?还是实例变量？访问静态变量和实例变量将会比访问局部变量多耗费2-3个时钟周期。
```
```java
private int _sum;
void getSum (int[] values) {
    int sum = _sum;  // temporary local variable.
    for (int i=0; i < value.length; i++) {
        sum += value[i];
    }
    _sum = sum;
}
```
17. 嵌套循环应该遵循“外小内大”的原则