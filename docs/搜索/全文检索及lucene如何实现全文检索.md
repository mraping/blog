# 全文检索及lucene如何实现全文检索

# 全文检索
全文检索是一种将文件中所有文本与检索项匹配的检索方法。它可以根据需要获得全文中有关章、节、段、句、词等信息。计算机程序通过扫描文章中的每一个词，对每一个词建立一个索引，指明该词在文章中出现的次数和位置，当用户查询时根据建立的索引查找，类似于通过字典的检索字表查字的过程。

# 为什么要有全文索引？
like满足不了要求，而且效率较低。

## 主要应用领域：
搜索引擎（百度，搜狗）、站内搜索（微博搜索）、电商网站（京东，淘宝）

## 全文索引工具的三个组成部分

1、索引部分（I am a boy） <br/>
2、分词部分 <br/>
3、搜索部分 <br/>

## 倒排索引
倒排索引，是索引技术中的一种，是基于单词-文档矩阵的一种存储形式，它描述了一个term词项集合和文档集合之间具有映射关系的数据结构。

反向索引的所保存的信息一般如下：

假设我的文档集合里面有100篇文档，为了方便表示，我们为文档编号从1到100，得到下面的结构

![ss](http://39.96.200.64/upload/2019/03/svqii2j9feimhpcplau32c3l3l.jpg)

左边保存的是一系列字符串，称为词典。

每个字符串都指向包含此字符串的文档(Document)链表，此文档链表称为倒排表(Posting List)。

有了索引，便使保存的信息和要搜索的信息一致，可以大大加快搜索的速度。

## 当接受到用户查询请求时，倒排索引中发生了什么？

一般地，当接受到用户查询请求时，进入到倒排索引进行检索时，在返回结果的过程中，主要有以下几个步骤：

比如说，我们要寻找既包含字符串“lucene”又包含字符串“solr”的文档，我们只需要以下几步：

1. 取出包含字符串“lucene”的文档链表。
2. 取出包含字符串“solr”的文档链表。
3. 通过合并链表，找出既包含“lucene”又包含“solr”的文件,对结果列表数据进行微运算，如：计算文档静态分，文档相关性等。
4. 基于上述运算得分对文档进行综合排序，最后返回结果给用户。

![ss](http://39.96.200.64/upload/2019/03/0lq3ukoa8aii1pj8fs9ffa785n.jpg)


## 分词

 在创建索引的时候需要用到分词器，在使用字符串搜索的时候也会用到分词器，并且这两个地方要使用同一个分词器，否则可能会搜索不出来结果。
 
### 中文分词器的原理

对于中文分词，通常有三种方式：单字分词、二分法分词、词典分词。

- a.1 单字分词：就是按照中文一个字一个字的进行分词，比如:"我们是中国人"，分词的效果就是"我"，"们"，"是"，"中"，"国"，"人"，StandardAnalyzer分词法就是单字分词。

- a.2 二分法分词：按照两个字进行切分，比如："我们是中国人"，分词的效果就是："我们"，"们是"，"是中"，"中国"，"国人"，CJKAnalyzer分词法就是二分法分词

- a.3 词库分词：按照某种算法构造词，然后去匹配已建好的词库集合，如果匹配到就切分出来成为词语，通常词库分词被认为是最好的中文分词算法，如："我们是中国人"，分词的效果就是:"我们"，"中国人"，极易分词

# lucene实现全文检索
Lucene是apache下的一个开放源代码的全文检索引擎工具包。提供了完整的查询引擎和索引引擎，部分文本分析引擎。Lucene的目的是为软件开发人员提供一个简单易用的工具包，以方便的在目标系统中实现全文检索的功能。

## Lucene与搜索引擎的区别
Lucene和搜索引擎不同，Lucene是一套用java写的全文检索的工具包，为应用程序提供了很多个api接口去调用，可以简单理解为是一套实现全文检索的类库，搜索引擎是一个全文检索系统，它是一个单独运行的软件。

## 搜索过程
搜索就是用户输入关键字，从索引（index）中进行搜索的过程。

- 根据关键字搜索索引，
- 根据索引找到对应的文档，从而找到要搜索的内容（这里指磁盘上的文件）。[可参考项目代码]


## 索引和搜索流程图
![](http://39.96.200.64/upload/2019/03/3i743aactsjddqqfae5tmpkeus.png)

### 建立索引
绿色表示索引过程，对要搜索的原始内容进行索引构建一个索引库，索引过程包括：

确定原始内容即要搜索的内容 --> 采集文档 --> 创建文档 --> 分析文档 --> 索引文档

具体步骤如下：

1、创建Directory（内存、硬盘） <br/>
2、创建IndexWriter（指定分词器） <br/>
3、创建Document对象（如word文档） <br/>
4、为Document添加Field（标题、内容、作者） <br/>
5、通过IndexWriter添加文档到索引中 <br/>

### 搜索
红色表示搜索过程，从索引库中搜索内容，搜索过程包括：

用户通过搜索界面 --> 创建查询 --> 执行搜索，从索引库搜索 --> 渲染搜索结果

具体步骤如下：

1、创建Directory <br/>
2、创建IndexReader（用来读取所有的索引）<br/>
3、根据IndexReader创建IndexSeacher <br/>
4、创建搜索的Query <br/>
5、创建seacher搜索并返回TopDocs <br/>
6、根据TopDocs获取ScoreDoc对象 <br/>
7、根据seacher和ScoreDoc对象获取具体的Document对象 <br/>
8、根据Document对象获取需要的值


# 扩展:Lucene，Solr，ElasticSearch ？
现在主流的搜索引擎大概就是：Lucene，Solr，ElasticSearch。

由于 Lucene 的复杂性，一般很少会考虑它作为搜索的第一选择，排除一些公司需要自研搜索框架，底层需要依赖 Lucene。

选择Solr，ElasticSearch搜索，参考以下文章
[Lucene，Solr，ElasticSearch ？](https://mp.weixin.qq.com/s/fSJvaGE3wtDM2DMaRKckaA)

# 其它

![招聘要求](http://39.96.200.64/upload/2019/03/p0h0a7f4siiaqqijpu9v6k1856.png)

## 索引过程的核心类
#### ①IndexWriter 
索引过程的核心组件。这个类负责创建新索引或者打开已有索引，以及向索引中添加、删除或更新被索引文档的信息。可以把IndexWriter看作这样一个对象：它为你提供针对索引文件的写入操作，但不能用于读取或搜索索引。IndexWriter需要开辟一定空间来存储索引，该功能可以由Directory完成。
#### ②Directory 
该类描述了Lucene索引的存放位置。它是一个抽象类，它的子类负责具体指定索引的存储路径。用FSDirectory.open方法来获取真实文件在文件系统的存储路径，然后将它们一次传递给IndexWriter类构造方法。IndexWriter不能直接索引文本，这需要先由Analyzer将文本分割成独立的单词才行。
#### ③Analyzer 
文本文件在被索引之前，需要经过Analyzer（分析器）处理。Analyzer是由IndexWriter的构造方法来指定的，它负责从被索引文本文件中提取语汇单元，并提出剩下的无用信息。如果被索引内容不是纯文本文件，那就需要先将其转换为文本文档。对于要将Lucene集成到应用程序的开发人员来说，选择什么样Analyzer是程序设计中非常关键的一步。分析器的分析对象为文档，该文档包含一些分离的能被索引的域。
#### ④Document 
Document对象代表一些域（Field）的集合。文档的域代表文档或者文档相关的一些元数据。元数据（如作者、标题、主题和修改日期等）都作为文档的不同域单独存储并被索引。Document对象的结构比较简单，为一个包含多个Filed对象容器；Field是指包含能被索引的文本内容的类。
#### ⑤Field 
索引中的每个文档都包含一个或多个不同命名的域，这些域包含在Field类中。每个域都有一个域名和对应的域值，以及一组选项来精确控制Lucene索引操作各个域值。

Field是文档中的域，包括Field名和Field值两部分，一个文档可以包括多个Field，Document只是Field的一个承载体，Field值即为要索引的内容，也是要搜索的内容。


## 参考文章
[lucene教程--全文检索技术详解](https://blog.csdn.net/sihai12345/article/details/70550362)
[Solr or Lucene全文检索实现原理](https://blog.csdn.net/vtopqx/article/details/73459439)

