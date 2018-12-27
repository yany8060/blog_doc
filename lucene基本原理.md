<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

### 基本概念
> Lucene是一个高效的，基于Java的全文检索库

**结构化数据**：指固定格式或有限长度的数据，如数据库，元数据等

**非结构化数据**：指不定长或无固定格式的数据，如邮件，word文档等

**对结构化数据的搜索**:如对数据库的搜索，用 SQL 语句。再如对元数据的搜索，如利用windows 搜索对文件名，类型，修改时间进行搜索等。

**对非结构化数据的搜索**:如利用 windows 的搜索也可以搜索文件内容，Linux 下的 grep命令，再如用 Google 和百度可以搜索大量内容数据。


**全文检索**：指计算机索引程序通过扫描文章中的每一个词，对每一个词建立一个索引，指明该词在文章中出现的次数和位置，当用户查询时，检索程序就根据事先建立的索引进行查找，并将查找的结果反馈给用户的检索方式。这个过程类似于通过字典中的检索字表查字的过程。**这种先建立索引，再对索引进行搜索的过程就叫全文检索(Full-text Search)。**


#### 创建索引：

1. 准备一些要索引的原文档（Document）
2. 将原文档传给分词组件（Tokenizer），经过分词后得到的结果称为词元（Token）
3. 将得到的词元（Token）传给语言处理组件（Linguistic Processor），语言处理组件的结果称为词(Term)。
	* 变为小写
	* 将单词缩减为词根形式
	* 将单词转变为词根形式
4. 将得到的词（Term）传给索引组件（Indexer）
	* 利用得到的词（Term）创建一个字典
	* 对字典按字母顺序进行排序
	* 合并相同的词(Term)成为文档倒排(PostingList)链表

	
#### 索引的检索过程：

1. 用户输入查询语句
2. 对查询语句进行词法分析，语法分析，即语言处理
3. 搜索索引，得到符合语法树的文档
4. 根据得到的文档和查询语句的相关性，对结果进行排序
	* 计算权重（Term weight）的过程：此Term在此文档中出现的次数（Term Frequency）；有多少文档包􏰅此Term（Document Frequency）;权重计算公式简单经典实现如下 $$ w_{t,d} = tf_{t,d} * log(\frac{n}{df_t}) $$
	* 判断Term之间的关系从而得到文档相关性的过程，也即向量空间模型的算法(VSM)。

### 架构：
Lucene是有索引和搜索的两个过程，包含索引创建，索引，搜索三个要点。
![avatar](http://wx2.sinaimg.cn/mw690/007h1WTYly1fwzc3ta8r6j30fo0dc0ub.jpg)
如上图所示：一是从文本内容切分词后索引入库；二是根据查询条件返回结果，即建立索引和进行查询两部分。

#### 索引创建API
Lucene中要索引的文档、数据记录以document表示，应用程序通过IndexWirter将Document加入到索引中。Lucene的索引是应用反向索引。当用户有请求时，Query代表用户的查询语句，IndexSearcher通过函数search搜索Lucene Index。IndexSearcher计算term weight和score并且将结果返回给用户，返回给用户的文档集合用TopDocsCollector表示。

![avatar](http://wx1.sinaimg.cn/mw690/007h1WTYly1fwzc3ob5ldj30hs0bgq3q.jpg)



#### Lucene实现的包结构
![avatar](http://wx2.sinaimg.cn/mw690/007h1WTYly1fwzc41qa1mj30g00aewfo.jpg)

* analysis模块主要负责词法分析及语言处理而形成term。
* index模块主要负责索引的创建，里面有IndexWriter。
* store模块主要负责索引的读写。
* QueryParser模块主要负责语法的分析。
* search模块主要负责对索引的搜索。
* similarity模块主要负责相关性打分的实现。
