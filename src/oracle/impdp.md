# oracle数据库impdp命令的使用方法

impdp命令直接在命令行(cmd/bash)下直接用,不必登录oracle.只能导入expdp命令导出的dmp文件.

但是导出时数据所在的表空间以及用户会记录在文件中,因此导出时是什么`表空间/用户`,导入时也必须是什么`表空间/用户`.

下面假设从A服务器的数据库中导出DMP文件,其用户名为`topicis`.要将其导入B服务器,而B服务器没有此用户.示例中,

表空间(`tablespace`)设置为`topicis_tablespace`

用户名(`username`/`schemas`)和密码均设置为`topicis`

目录名(`directory`)设置为`dmpdir`


## 操作流程开始:
+ 准备目录
```
cd /
mkdir db-data db-dir
chmod 777 -R db-data db-dir
```

+ 登录数据库
```
sqlplus / as sysdba
```

+ 创建表空间
```
create tablespace topicis_tablespace logging datafile '/db-data/topicis.dbf' size 1g autoextend on next 32m maxsize unlimited extent management local;
```

> 要保证`/db-data`目录存在,否则报错

+ 创建用户
```
create user topicis identified by topicis;
```

+ 指定表空间
```
alter user topicis default tablespace topicis_tablespace;
```

+ 赋予权限
```
grant create any directory, create session, create table, create view, unlimited tablespace to topicis;
```

+ 赋予导入权限(解决导入时偶尔发生的无DBA权限问题)
```
grant imp_full_database to topicis;
```

+ 创建directory

```
create or replace directory dmpdir as '/db-dir/topicis';
```

+ 退出sql操作窗口,导入数据

```
exit
impdp topicis/topicis directory=dmpdir dumpfile=test.dmp full=y;
```

或者

```
imp topicis/topicis file=/tmp_reg.dmp log=import2.log full=y ignore=y;
```


正常情况下,数据库表会自动创建,数据可以顺利导入.


## 其他命令

+ 删除用户
```
drop user topicis cascade;
```

+ 删除表空间
```
drop tablespace topicis_tablespace including contents and datafiles;
```



参考自[https://blog.csdn.net/zengmingen/article/details/60957942](https://blog.csdn.net/zengmingen/article/details/60957942)