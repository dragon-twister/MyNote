# Lucene和Solr

### 实现步骤

- 创建一个FullTextSession
- 通过Hibernate Search query DSL(推荐的)或Lucene 
- query API创建一个Lucene query。
- 使用org.hibernate.Query包装Lucene query
- 通过调用list() 或scroll()执行search

http://blog.csdn.net/dm_vincent/article/details/40707857

http://sin90lzc.iteye.com/blog/1106714