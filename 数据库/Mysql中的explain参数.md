# Mysql中的explain参数

[TOC]

## 前言

- MySQL EXPLAIN命令主要用来优化查询性能本文文主要讲解explain相关参数说明。
- EXPALIN只能解释SELECT操作，其他操作要重写为SELECT后查看执行计划。


    mysql> explain select * from (select * from ( select * from t1 where id=2602) a) b;
    +----+-------------+------------+--------+-------------------+---------+---------+------+------+-------+
    | id | select_type | table      | type   | possible_keys     | key     | key_len | ref  | rows | Extra |
    +----+-------------+------------+--------+-------------------+---------+---------+------+------+-------+
    |  1 | PRIMARY     | <derived2> | system | NULL              | NULL    | NULL    | NULL |    1 |       |
    |  2 | DERIVED     | <derived3> | system | NULL              | NULL    | NULL    | NULL |    1 |       |
    |  3 | DERIVED     | t1         | const  | PRIMARY,idx_t1_id | PRIMARY | 4       |      |    1 |       |
    +----+-------------+------------+--------+-------------------+---------+---------+------+------+-------+

## select_type

表示查询中每个select子句的类型



1. SIMPLE：简单的SELECT，不实用UNION或者子查询。

2. PRIMARY：最外层SELECT。

3. UNION：第二层，在SELECT之后使用了UNION。

4. DEPENDENT UNION：UNION语句中的第二个SELECT，依赖于外部子查询。

5. UNION RESULT：UNION的结果。

6. SUBQUERY：子查询中的第一个SELECT。

7. DEPENDENT SUBQUERY：子查询中的第一个SELECT，取决于外面的查询。

8. DERIVED：导出表的SELECT（FROM子句的子查询.

## table

显示这一行的数据是关于哪张表的，有时不是真实的表名字,看到的是derivedx(x是个数字,我的理解是第几步执行的结果)

## type

这是重要的列，显示连接使用了何种类型。
从最好到最差的连接类型：const、eq_reg、ref、range、index和ALL

Type：告诉我们对表使用的访问方式，主要包含如下集中类型。

1. all：全表扫描。

2. const：读常量，最多只会有一条记录匹配，由于是常量，实际上只须要读一次。

3. eq_ref：最多只会有一条匹配结果，一般是通过主键或唯一键索引来访问。

4. fulltext：进行全文索引检索。

5. index：全索引扫描。

6. index_merge：查询中同时使用两个（或更多.索引，然后对索引结果进行合并（merge.，再读取表数据。

7. index_subquery：子查询中的返回结果字段组合是一个索引（或索引组合.，但不是一个主键或唯一索引。

8. rang：索引范围扫描。

9. ref：Join语句中被驱动表索引引用的查询。

10. ref_or_null：与ref的唯一区别就是在使用索引引用的查询之外再增加一个空值的查询。

11. system：系统表，表中只有一行数据；

12. unique_subquery：子查询中的返回结果字段组合是主键或唯一约束。

## possible_keys

指出MySQL能使用哪个索引在表中找到记录，查询涉及到的字段上若存在索引，则该索引将被列出，但查询时不一定被使用


## key

实际使用的索引。如果为NULL，则没有使用索引。很少的情况下，MYSQL会选择优化不足的索引。这种情况下，可以在SELECT语句中使用USE INDEX（indexname.来强制使用一个索引或者用IGNORE INDEX（indexname.来强制MYSQL忽略索引

## key_len

使用的索引的长度。在不损失精确性的情况下，长度越短越好

## ref

显示索引的哪一列被使用了，如果可能的话，是一个常数

## rows

rows列显示MySQL认为它执行查询时必须检查的行数。注意这是一个预估值。

## Extra

执行查询的过程中对查询计划的重要补充信息

1. Distinct：查找distinct 值，当mysql找到了第一条匹配的结果时，将停止该值的查询，转为后面其他值查询。

2. Full scan on NULL key：子查询中的一种优化方式，主要在遇到无法通过索引访问null值的使用。

3. Range checked for each record (index map: N)：通过 MySQL 官方手册的描述，当 MySQL Query Optimizer 没有发现好的可以使用的索引时，如果发现前面表的列值已知，部分索引可以使用。对前面表的每个行组合，MySQL检查是否可以使用range或 index_merge访问方法来索取行。

4. Select tables optimized away：当我们使用某些聚合函数来访问存在索引的某个字段时，MySQL Query Optimizer 会通过索引直接一次定位到所需的数据行完成整个查询。当然，前提是在 Query 中不能有 GROUP BY 操作。如使用MIN()或MAX()的时候。

5. Using filesort：当Query 中包含 ORDER BY 操作，而且无法利用索引完成排序操作的时候，MySQL Query Optimizer 不得不选择相应的排序算法来实现。

6. Using index：所需数据只需在 Index 即可全部获得，不须要再到表中取数据。

7. Using index for group-by：数据访问和 Using index 一样，所需数据只须要读取索引，当Query 中使用GROUP BY或DISTINCT 子句时，如果分组字段也在索引中，Extra中的信息就会是 Using index for group-by。

8. Using temporary：当 MySQL 在某些操作中必须使用临时表时，在 Extra 信息中就会出现Using temporary 。主要常见于 GROUP BY 和 ORDER BY 等操作中。

9. Using where：如果不读取表的所有数据，或不是仅仅通过索引就可以获取所有需要的数据，则会出现 Using where 信息。

10. No tables：Query 语句中使用 FROM DUAL或不包含任何 FROM子句。

11. Not exists：MYSQL优化了LEFT JOIN，一旦它找到了匹配LEFT JOIN标准的行， 就不再搜索了。
