[toc]
# 常用SQL
## Oracle
### 修改字段长度和类型

    alter table test modify(name varchar(255));

### 添加注释

    comment on column xmtax_qz_log.satus is '0失败1失败2税号不存在3税号和身份证不匹配，4入库成功';


### 解决死锁
 
    select object_id,session_id,locked_mode from v$locked_object;
     
    alter system  kill session  '56, 29891'; 
    
    select t2.username,t2.sid,t2.serial#,t2.logon_time from v$locked_object t1,v$session t2 where t1.session_id=t2.sid order by t2.logon_time;

### 删除表

drop table credit_details;

### 添加字段

    alter table dl_fankui add(yhmc varchar2(40) not null)

### 创建用户

    create user tjgs identified by tjgs;

### 并赋予权限

     GRANT CREATE SESSION,
             CREATE ANY TABLE,
             CREATE ANY VIEW ,
             CREATE ANY INDEX,
             CREATE ANY PROCEDURE,
             ALTER ANY TABLE,
             ALTER ANY PROCEDURE,
             DROP ANY TABLE,
             DROP ANY VIEW,
             DROP ANY INDEX,
             DROP ANY PROCEDURE,
            SELECT ANY TABLE,
             INSERT ANY TABLE,
            UPDATE ANY TABLE,
             DELETE ANY TABLE TO tjgs; 

### 表空间配额

    alter user tjgs quota 4M on users;       
    或者
    GRANT UNLIMITED TABLESPACE TO tjgs;