# mybatis

### hibernate和mybatis的共同点

无论是用过的 hibernate,Mybatis,你都可以法相他们有一个共同点：

- 从配置文件(通常是 XML配置文件中)得到sessionfactory.
- 由 sessionfactory 产生 session
- 在 session 中完成对数据的增删改查和事务提交等.
在用完之后关闭 session 。
- 在 Java 对象和 数据库之间有做 mapping 的配置文件，也通常是 xml 文件。


### 命名空间的作用

<mapper namespace="">：

映射文件中的namespace是用于绑定Dao接口的，当你的namespace绑定接口后，你可以不用写接口实现类，mybatis会通过该绑定自动帮你找到对应要执行的SQL语句，如下：


### 分页sql

SELECT * FROM test LIMIT 10, 1 

结果 11

SELECT * FROM test LIMIT 10, 3

结果 11 12 13

### 注意

要实现动态传入表名、列名，需要做如下修改 
添加属性statementType=”STATEMENT” 
同时sql里的属有变量取值都改成${xxxx}，而不是#{xxx}

statementType="STATEMENT"  #{xxx}  的#就不能用了  需要换成${xxx}

statementType：STATEMENT（非预编译），PREPARED（预编译）或CALLABLE中的任意一个，这就告诉 MyBatis 分别使用Statement，PreparedStatement或者CallableStatement。默认：PREPARED。这里显然不能使用预编译，要改成非预编译。