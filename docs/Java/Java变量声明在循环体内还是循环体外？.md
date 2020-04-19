阿里巴巴开发手册中描述：

【推荐】循环体中的语句要考量性能，以下操作尽量移至循环体外处理，如定义对象、变量、获取数据库连接，进行不必要的 try-catch 操作(这个 try-catch 是否可以移至循环体外)。

一些文章在介绍Java代码的优化细节中也描述：

循环中不要不断的创建对象引用，理由是：循环中new object()会导致内存中有count个引用存在，会导致消耗内存过大。



但是真的这样合理？

- 从局部变量作用域最小化原则来说，在循环外定义变量会使得代码的可读性下降
- 真的会性能下降？查阅了一些资料，有资料表明并没有表面性能一定会下降，Java的JIT（即时编译技术）可能会将你在这些层面的所谓优化给重新组织˙。



从一些源码中，也发现都是在循环中new object()，如HashMap的putMapEntries方法

```java
/**
     * Implements Map.putAll and Map constructor.
     *
     * @param m the map
     * @param evict false when initially constructing this map, else
     * true (relayed to method afterNodeInsertion).
     */
    final void putMapEntries(Map<? extends K, ? extends V> m, boolean evict) {
        int s = m.size();
        if (s > 0) {
           	.... 
            for (Map.Entry<? extends K, ? extends V> e : m.entrySet()) {
                K key = e.getKey(); // 注意此处
                V value = e.getValue();
                putVal(hash(key), key, value, false, evict);
            }
        }
    }
```

MyBatis源码,BatchExecutor.doFlushStatements()

```java
public List<BatchResult> doFlushStatements(boolean isRollback) throws SQLException {
    try {
      List<BatchResult> results = new ArrayList<>();
      if (isRollback) {
        return Collections.emptyList();
      }
      for (int i = 0, n = statementList.size(); i < n; i++) {
        Statement stmt = statementList.get(i);// 注意此处
        applyTransactionTimeout(stmt);
        BatchResult batchResult = batchResultList.get(i);// 注意此处
        try {
          batchResult.setUpdateCounts(stmt.executeBatch());
          MappedStatement ms = batchResult.getMappedStatement();// 注意此处
          List<Object> parameterObjects = batchResult.getParameterObjects();
          KeyGenerator keyGenerator = ms.getKeyGenerator();
          if (Jdbc3KeyGenerator.class.equals(keyGenerator.getClass())) {
            Jdbc3KeyGenerator jdbc3KeyGenerator = (Jdbc3KeyGenerator) keyGenerator;
            jdbc3KeyGenerator.processBatch(ms, stmt, parameterObjects);
          } else if (!NoKeyGenerator.class.equals(keyGenerator.getClass())) { //issue #141
            for (Object parameter : parameterObjects) {
              keyGenerator.processAfter(this, ms, stmt, parameter);
            }
          }
          // Close statement to close cursor #1109
          closeStatement(stmt);
        } catch (BatchUpdateException e) {
          StringBuilder message = new StringBuilder();
          message.append(batchResult.getMappedStatement().getId())
              .append(" (batch index #")
              .append(i + 1)
              .append(")")
              .append(" failed.");
          if (i > 0) {
            message.append(" ")
                .append(i)
                .append(" prior sub executor(s) completed successfully, but will be rolled back.");
          }
          throw new BatchExecutorException(message.toString(), e, results, batchResult);
        }
        results.add(batchResult);
      }
      return results;
    } finally {
      for (Statement stmt : statementList) {
        closeStatement(stmt);
      }
      currentSql = null;
      statementList.clear();
      batchResultList.clear();
    }
  }
```

综上：推介在循环中声明变量。