MYSQL 开发常用命令集锦
=================================

表结构修改
---------------------------------

> 新增字段
>
> > ALTER TABLE table_name ADD new_column column_type  DEFAULT default_value NOT NULL COMMENT '注释'
>
> 修改字段信息
>
> > ALTER TABLE table_name MODIFY column column_type  COMMENT '注释'
>
> 增加索引
> 
> > 普通索引：ALTER TABLE table_name ADD INDEX index_name (column_list)   
> > 唯一索引：ALTER TABLE table_name ADD UNIQUE (column_list)
>
> 删除索引
> > ALTER TABLE table_name DROP INDEX index_name

****

表信息查询
-----------------------------
* 查看表结构 :  desc table_name 
* 查看表创建语句  :  SHOW CREATE TABLE  table_name
* 已存在表->查看索引  :   show index from table_name;

****

常用函数
------------------------------
* 计数    :  count(1)
* 求某一列的和   :  sum(colunm_name)
* 将 LONG 型时间转换为 DATE_TIME :    FROM_UNIXTIME( long_time / 1000 )
* 将时间转换为指定格式   : DATE_FORMAT(date,format)

>
> > Exp:DATE_FORMAT(date,'%Y-%m-%d %H:%i:%s')   2016-12-01 17:55:55
> > 其他配置参考:http://www.w3school.com.cn/sql/func_date_format.asp
>
> 在命令行查询数据时各列分行显示   :  SQL查询语句 + \G           
>
> > Exp: select * from test \G  


连接数据库
-----------------------
* 直连

>
> > 1. mysql -h{host} -P{port} -u{user_name} -p{password} -D{db_name}
> > 2. mysql -h{host} -P{port} -u{user_name} -p  按 enter 后 输入密码 -> use {db_name} 
> > 第二种方法的好处是可以防止密码泄漏

导入/导出数据
---------------------
* 未连接数据库的情况下从外部 SQL 脚本导入

>
> > mysql -h {host} -u {user_name} -p {password} < {absolute_filepath}
>

* 已连接数据的情况下导入数据,此时命令提示符为 mysql>

>
> > source {absolute_filepath}
>

* 根据 SQL 语句将查询结果导出到指定路径

>
> > mysql -u {user_name} -p {password} -h {host} -P {port}  {db_name} -N -e {sql_str} > {absolute_filepath}
>

* case when 语句的使用

Exp:
<pre>
    select 
       ( case sex when 1 then '男生' when 2 then '女生' else '人妖' end ) as sex 
    from user
</pre>
<pre>
    select 
       sum( case when sex = 1 then 1 else 0 end ) as '男生人数',
       sum( case when sex = 0 then 1 else 0 end ) as '女生人数'
    from user
</pre>
