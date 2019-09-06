- 创建dblinx

    create public database link TESTLINK2 connect to WANGYONG identified by "123456" USING 'ORCL21'
    
- 查看dblink

    select owner,object_name from dba_objects where object_type='DATABASE LINK';
    
    select * from dba_db_links;
    
- 查看当前表属于哪个数据库