## 1、先说说Lucene与elasticsearch关系？
Lucene是一个开源的全文检索引擎工具包（类似于Java api），而Elasticsearch底层是基于这些包，对其进行了扩展，提供了比Lucene更为丰富的查询语言，可以非常方便的通过Elasticsearch的HTTP接口与底层Lucene交互。
如果在应用程序中直接使用Lucene，你需要覆盖大量的集成框架工作，而使用ElasticSearch就省下了这些集成工作。
一句话概括: Elasticsearch是Lucene面向企业搜索应用的扩展，极大的缩短研发周期。
## 2、有没有必要先去学习Lucene？
刚刚入门Elasticsearch，只需稍微了解下Lucene，无需去真正学习它，就可以很好的完成全文索引的工作，很好的进行开发。
等自己熟练使用之后，可以反过头来学习Lucene里面底层的原理，也是一种提升。

因为Lucene是一个编程库,您可以按原始接口来调用。但是Elasticsearch是在它基础上扩展的应用程序,就可以直接拿来使用了。
---例子一定要看！！如下：---
例如：你直接拿汽车（Elasticsearch）来开，开好车就行，无需了解里面的发动机、各个组件（Lucene library）。后面你在去了解一些原理，对于修车等等会有帮助。