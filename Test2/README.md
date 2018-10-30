# 实验2：用户及权限管理
## 一.
> 以system登录到pdborcl，创建角色csy_view和用户csy_user，并授权和分配空间：
```
$ sqlplus system/123@pdborcl
SQL> CREATE ROLE csy_view;
Role created.
SQL> GRANT connect,resource,CREATE VIEW TO csy_view;
Grant succeeded.
SQL> CREATE USER csy_user IDENTIFIED BY 123 DEFAULT TABLESPACE users TEMPORARY TABLESPACE temp;
User created.
SQL> ALTER USER csy_user QUOTA 50M ON users;
User altered.
SQL> GRANT csy_view TO csy_user;
Grant succeeded.
SQL> exit
```
### 语句“ALTER USER new_user QUOTA 50M ON users;”是指授权new_user用户访问users表空间，空间限额是50M。

## 二.
> 新用户csy_user连接到pdborcl，创建表mytable和视图myview，插入数据，最后将myview的SELECT对象权限授予hr用户。
```
$ sqlplus csy_user/123@pdborcl
SQL> show user;
  USER is "CSY_USER"
SQL> CREATE TABLE mytable (id number,name varchar(50));
Table created.
SQL> INSERT INTO mytable(id,name)VALUES(1,'zhang');
1 row created.
SQL> INSERT INTO mytable(id,name)VALUES (2,'wang');
1 row created.
SQL> CREATE VIEW myview AS SELECT name FROM mytable;
View created.
SQL> SELECT * FROM myview;
NAME
--------------------------------------------------
zhang
wang
SQL> GRANT SELECT ON myview TO hr;
Grant succeeded.
SQL>exit
```
## 三.
> 用户hr连接到pdborcl，查询csy_user授予它的视图myview
```
$ sqlplus hr/123@pdborcl
SQL> SELECT * FROM csy_user.myview;
NAME
--------------------------------------------------
zhang
wang
SQL> exit
```
## 四.
> 数据库和表空间占用分析
>> 当全班同学的实验都做完之后，数据库pdborcl中包含了每个同学的角色和用户。 所有同学的用户都使用表空间users存储表的数据。 表空间中存储了很多相同名称的表mytable和视图myview，但分别属性于不同的用户，不会引起混淆。 随着用户往表中插入数据，表空间的磁盘使用量会增加。

## 五.
> 查看数据库的使用情况
>> 以下样例查看表空间的数据库文件，以及每个文件的磁盘占用情况。

```
$ sqlplus system/123@pdborcl

SQL>SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files  WHERE  tablespace_name='USERS';

SQL>SELECT a.tablespace_name "表空间名",Total/1024/1024 "大小MB",
 free/1024/1024 "剩余MB",( total - free )/1024/1024 "使用MB",
 Round(( total - free )/ total,4)* 100 "使用率%"
 from (SELECT tablespace_name,Sum(bytes)free
        FROM   dba_free_space group  BY tablespace_name)a,
       (SELECT tablespace_name,Sum(bytes)total FROM dba_data_files
        group  BY tablespace_name)b
 where  a.tablespace_name = b.tablespace_name;
 ```
>> autoextensible是显示表空间中的数据文件是否自动增加。
>> MAX_MB是指数据文件的最大容量。
## 六.
> 实验结论
>> 通过本次的实验基本掌握了Oracle中系统权限和对象权限的概念，能熟练进行用户权限的授予与回收；理解角色的基本概念，能熟练使用角色进行权限的授予与回收
