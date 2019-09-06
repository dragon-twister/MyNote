# Mysql

[toc]

### 数据库事务的四个特性

原子性:整个事务中的所有操作，要么全部完成，要么全部不完成，不可能停滞在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。

一致性:在事务开始之前和事务结束以后，数据库的完整性约束没有被破坏。

隔离性:隔离状态执行事务，使它们好像是系统在给定时间内执行的唯一操作。如果有两个事务，运行在相同的时间内，执行 相同的功能，事务的隔离性将确保每一事务在系统中认为只有该事务在使用系统。这种属性有时称为串行化，为了防止事务操作间的混淆，必须串行化或序列化请 求，使得在同一时间仅有一个请求用于同一数据。

持久性:在事务完成以后，该事务所对数据库所作的更改便持久的保存在数据库之中，并不会被回滚。

### jdbc操作数据库的步骤？

    - 加载数据库驱动程序
    
        Class.forName("com.mysql.jdbc.Driver")
    
    - 连接数据库

        Connection con = DriverManager.getConnection(url,username, password);
    
    - 操作数据库
    
        PreparedStatement stat = con.prepareStatement(sql);
        stat.executeQuery();
        
    - 关闭数据库，释放连接 
    
        con.close();

### @Transational和public

@Transactional 
注解应该只被应用到 public 方法上，这是由 Spring AOP 的本质决定的。

如果你在 protected、private 或者默认可见性的方法上使用 @Transactional 注解，这将被忽略，也不会抛出任何异常。


### 怎么防止sql注入

1、使用预编译：在mybatis中使用#号，hibernate使用query.setParameter来设置参数

mybatis的会默认加上引号，而$是原样输出，

什么时候使用$:

        1 作为in条件时，

        2 参数为int类型并且数据库中字段的类型是number，

        3 表名

        4 order by ${}，排序字段

2、验证字符串

### 数据库三范式

1 所有字段不可再分

2 满足第一范式的情况下，所有字段都依赖主键

3 满足第二范式的情况下，非主键的所有字段都互不依赖

### MySQL的复制原理以及流程

基本原理流程，3个线程以及之间的关联；

1\. 主：binlog线程——记录下所有改变了数据库数据的语句，放进master上的binlog中；

2\. 从：io线程——在使用start slave 之后，负责从master上拉取 binlog 内容，放进 自己的relay log中；

3\. 从：sql执行线程——执行relay log中的语句；

    
### 如果发现CPU，或者IO压力很大，怎么定位问题？

   - 首先我会用top命令和iostat命令，定位是什么进程在占用cpu和磁盘io； 
   - 如果是mysql的问题，我会登录到数据库，通过show full - processlist命令，看现在数据库在执行什么sql语句，是否有语句长时间执行使数据库卡住；
   - 执行show innodb engine status命令，查看数据库是否有锁资源争用；
   - 查看mysql慢查询日志，看是否有慢sql；
   - 找到引起数据库占用资源高的语句，进行优化，该建索引的建索引，索引不合适的删索引，或者根据情况kill掉耗费资源的sql语句等
   

### MySQL中myisam与innodb的区别

(1)、问5点不同；

1>.InnoDB支持事物，而MyISAM不支持事物

2>.InnoDB支持行级锁，而MyISAM支持表级锁

3>.InnoDB支持MVCC, 而MyISAM不支持

4>.InnoDB支持外键，而MyISAM不支持

5>.InnoDB不支持全文索引，而MyISAM支持。

补充：

    - 应用场景的区别：<br>
        MyISAM适合：(1)做很多count的计算；(2)插入不频繁，查询非常频繁；(3)没有事务。<br>
        InnoDB适合：(1)可靠性要求比较高，或者要求事务；(2)表更新和查询都相当的频繁，并且行锁定的机会比较大的情况。

(2)、innodb引擎的4大特性

插入缓冲（insert buffer),二次写(double write),自适应哈希索引(ahi),预读(read ahead)

(3)、2者selectcount(*)哪个更快，为什么

myisam更快，因为myisam内部维护了一个计数器，可以直接调取。

  
### MySQL binlog的几种日志录入格式以及区别  

(1)、binlog的日志格式的种类和分别  
(2)、适用场景；  
(3)、结合第一个问题，每一种日志格式在复制中的优劣。  
Statement：每一条会修改数据的sql都会记录在binlog中。  
优点：不需要记录每一行的变化，减少了binlog日志量，节约了IO，提高性能。(相比row能节约多少性能 与日志量，这个取决于应用的SQL情况，正常同一条记录修改或者插入row格式所产生的日志量还小于Statement产生的日志量，但是考虑到如果带条 件的update操作，以及整表删除，alter表等操作，ROW格式会产生大量日志，因此在考虑是否使用ROW格式日志时应该跟据应用的实际情况，其所 产生的日志量会增加多少，以及带来的IO性能问题。)  
缺点：由于记录的只是执行语句，为了这些语句能在slave上正确运行，因此还必须记录每条语句在执行的时候的 一些相关信息，以保证所有语句能在slave得到和在master端执行时候相同 的结果。另外mysql 的复制,像一些特定函数功能，slave可与master上要保持一致会有很多相关问题(如sleep()函数， last\_insert\_id()，以及user-defined functions(udf)会出现问题).  
使用以下函数的语句也无法被复制：  
\* LOAD_FILE()  
\* UUID()  
\* USER()  
\* FOUND_ROWS()  
\* SYSDATE() (除非启动时启用了 --sysdate-is-now 选项)  
同时在INSERT ...SELECT 会产生比 RBR 更多的行级锁  
2.Row:不记录sql语句上下文相关信息，仅保存哪条记录被修改。  
优点： binlog中可以不记录执行的sql语句的上下文相关的信息，仅需要记录那一条记录被修改成什么了。所以rowlevel的日志内容会非常清楚的记录下 每一行数据修改的细节。而且不会出现某些特定情况下的存储过程，或function，以及trigger的调用和触发无法被正确复制的问题  
缺点:所有的执行的语句当记录到日志中的时候，都将以每行记录的修改来记录，这样可能会产生大量的日志内容,比 如一条update语句，修改多条记录，则binlog中每一条修改都会有记录，这样造成binlog日志量会很大，特别是当执行alter table之类的语句的时候，由于表结构修改，每条记录都发生改变，那么该表每一条记录都会记录到日志中。  
3.Mixedlevel: 是以上两种level的混合使用，一般的语句修改使用statment格式保存binlog，如一些函数，statement无法完成主从复制的操作，则 采用row格式保存binlog,MySQL会根据执行的每一条具体的sql语句来区分对待记录的日志形式，也就是在Statement和Row之间选择 一种.新版本的MySQL中队row level模式也被做了优化，并不是所有的修改都会以row level来记录，像遇到表结构变更的时候就会以statement模式来记录。至于update或者delete等修改数据的语句，还是会记录所有行的 变更。  
  
### MySQL数据库cpu飙升到500%的话他怎么处理？  
列出所有进程 show processlist 观察所有进程 多秒没有状态变化的(干掉)  
查看超时日志或者错误日志 (做了几年开发,一般会是查询以及大批量的插入会导致cpu与i/o上涨,,,,当然不排除网络状态突然断了,,导致一个请求服务器只接受到一半，比如where子句或分页子句没有发送,,当然的一次被坑经历)  
  
### MySQL中InnoDB引擎的行锁是基于什么实现的？为什么这样设计？

InnoDB是基于索引来完成行锁  
例: select * from tab\_with\_index where id = 1 for update;  
for update 可以根据条件来完成行锁锁定,并且 id 是有索引键的列,  
如果 id 不是索引键那么InnoDB将完成表锁,并发将无从谈起

19、如何从mysqldump产生的全库备份中只恢复某一个库、某一张表？  

答案见：[http://suifu.blog.51cto.com/9167728/1830651](http://suifu.blog.51cto.com/9167728/1830651)  
  
### 一个6亿的表a，一个3亿的表b，通过外间tid关联，你如何最快的查询出满足条件的第50000到第50200中的这200条数据记录。

1、如果A表TID是自增长,并且是连续的,B表的ID为索引  
select * from a,b where a.tid = b.id and a.tid>500000 limit 200;  
  
2、如果A表的TID不是连续的,那么就需要使用覆盖索引.TID要么是主键,要么是辅助索引,B表ID也需要有索引。  
select * from b , (select tid from a limit 50000,200) a where b.id = a .tid;


### sql语句应该考虑哪些安全性？ 

（1）防止sql注入，对特殊字符进行转义，过滤或者使用预编译的sql语句绑定变量。 

（2）最小权限原则，特别是不要用root账户，为不同的类型的动作或者组建...


### mysql主库从库的实现原理


### 数据库备份计划

8、备份计划，mysqldump以及xtranbackup的实现原理  
(1)、备份计划；  
这里每个公司都不一样，您别说那种1小时1全备什么的就行  
(2)、备份恢复时间；  
这里跟机器，尤其是硬盘的速率有关系，以下列举几个仅供参考  
20G的2分钟（mysqldump）  
80G的30分钟(mysqldump)  
111G的30分钟（mysqldump)  
288G的3小时（xtra)  
3T的4小时（xtra)  
逻辑导入时间一般是备份时间的5倍以上  
  
### 事务的实现原理

在InnoDB内部会维护一个redo日志文件，我们也可以叫做事务日志文件。事务日志会存储每一个InnoDB表数据的记录修改。当InnoDB启动时，InnoDB会检查数据文件和事务日志，并执行两个步骤：它应用（前滚）已经提交的事务日志到数据文件，并将修改过但没有提交的数据进行回滚操作。  

### 500台db，在最快时间之内重启  
puppet，dsh  
 
### 你是如何监控你们的数据库的？你们的慢日志都是怎么查询的？  
监控的工具有很多，例如zabbix，lepus，我这里用的是lepus  

### 你是否做过主从一致性校验，如果有，怎么做的，如果没有，你打算怎么做？  
主从一致性校验有多种工具 例如checksum、mysqldiff、pt-table-checksum等  
  



