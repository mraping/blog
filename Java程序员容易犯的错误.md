## 1.Array转ArrayList

当需要把Array转成ArrayList的时候，开发人员经常这样做：

```
List<String> list = Arrays.asList(arr);
```

Arrays.asList()会返回一个ArrayList，但是要特别注意，这个ArrayList是Arrays类的静态内部类，并不是java.util.ArrayList类。java.util.Arrays.ArrayList类实现了set()， get()，contains()方法，但是并没有实现增加元素的方法（事实上是可以调用add方法，但是没有具体实现，仅仅抛出UnsupportedOperationException异常），因此它的大小也是固定不变的。为了创建一个真正的java.util.ArrayList，你应该这样做：

```
ArrayList<String> arrayList = new ArrayList<String>(Arrays.asList(arr));
```

ArrayList的构造方法可以接收一个Collection类型，而java.util.Arrays.ArrayList已经实现了该接口。
## 2.判断一个数组是否包含某个值

开发人员经常这样做：

```
Set<String> set = new HashSet<String>(Arrays.asList(arr));
return set.contains(targetValue);
```

以上代码可以正常工作，但是没有必要将其转换成set集合，将一个List转成Set需要额外的时间，其实我们可以简单的使用如下方法即可：

```
Arrays.asList(arr).contains(targetValue);
或者
for(String s: arr){
if(s.equals(targetValue))
return true;
}
return false;
```
第一种方法可读性更强。

## 3.访问级别

开发人员经常使用public修饰类字段，虽然这很容易让别人直接通过引用获取该字段的值，但这是一个不好的设计。根据经验，应该尽可能的降低成员属性的访问级别。

