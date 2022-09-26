## 查看和调整oracle数据库的最大连接数

### 登录三部曲

```shell
# 切换用户
su - oracle

# 直接登录
sqlplus / as sysdba

# 或者使用下面的命令
sqlplus /nolog
connect /as sysdba
```

### 监听
```shell
# 监听状态
lsnrctl status
# 关闭监听
lsnrctl stop
# 启动监听
lsnrctl start
```

### 数据库的启动与停止
```shell
# 停止
shutdown immediate
# 启动
startup
```


#### 设置环境变量

在shell中执行下面的命令. **注意其值要以实际安装的数据库为准**

    export ORACLE_HOME=/u01/app/oracle/12c
    export ORACLE_SID=orcl


### 查看当前已使用的连接数

    SQL> select count(*) from v$process; 

### 查看最大连接数和会话数

    SQL> show parameter processes;
    SQL> show parameter sessions;

### 修改连接数

    SQL> alter system set processes = 2000 scope = spfile;
    SQL> alter system set sessions=2205 scope=spfile;
    SQL> shutdown immediate;
    SQL> startup;

### 执行时异常的处理

#### 内存不足

一般会提示如下:

    ORA-00821: Specified value of sga_target  **M is too small, needs to be at least **M

报错原因是调整了连接数，需相应修改内存大小：

    SQL> create pfile='/u01/app/oracle/12c/dbs/initGSP.ora' from spfile;
    SQL> quit

然后在shell中, 使用oracle用户执行，修改内存大小

    vi /u01/app/oracle/12c/dbs/initGSP.ora

找到

    *.sga_target=768m

修改成（上面报错的值  needs to be at least **M)

    *.sga_target=1768m

然后重新启动数据库

    SQL> startup pfile='/u01/app/oracle/12c/dbs/initGSP.ora';

**如果上面一句启动报错，务必解决错误后再执行下面的语句**

    SQL> create spfile from pfile='/u01/app/oracle/12c/dbs/initGSP.ora';
    SQL> shutdown immediate;
    SQL> startup;


## 数据泵导出报错 ORA-31634: job already exists

需清理dba_datapump_jobs表的记录，首先查询现有记录：

```oracle
select 'drop table ' || owner_name || '.' || job_name || ';'
from dba_datapump_jobs
where state = 'NOT RUNNING';
```

拷贝出查询结果执行即可。


## 查询表空间使用情况

```oracle
SELECT a.tablespace_name,
       total / (1024 * 1024 * 1024),
       free / (1024 * 1024 * 1024),
       (total - free) / (1024 * 1024 * 1024)
FROM (SELECT tablespace_name, SUM(bytes) free
      FROM dba_free_space
      GROUP BY tablespace_name) a,
     (SELECT tablespace_name, SUM(bytes) total
      FROM dba_data_files
      GROUP BY tablespace_name) b
WHERE a.tablespace_name = b.tablespace_name
order by a.tablespace_name;
```

## session相关

+ 查询session

```oracle
select *
from v$session;
```

+ 查询被锁定的语句
```oracle
select *
from v$locked_object;
```

+ 查看数据库引起锁表的SQL语句
```oracle
-- 查看数据库引起锁表的SQL语句
SELECT A.USERNAME,
       A.MACHINE,
       A.PROGRAM,
       A.SID,
       A.SERIAL#,
       A.STATUS,
       A.PORT,
       C.PIECE,
       C.SQL_TEXT
FROM V$SESSION A,
     V$SQLTEXT C
WHERE A.SID IN (SELECT DISTINCT T2.SID
                FROM V$LOCKED_OBJECT T1,
                     V$SESSION T2
                WHERE T1.SESSION_ID = T2.SID)
  AND A.SQL_ADDRESS = C.ADDRESS(+)
ORDER BY A.SERIAL#, C.PIECE;
```

+ 解锁语句
```oracle
SELECT 'ALTER SYSTEM KILL SESSION ''' || A.SID || ',' || A.SERIAL# || ''';'
FROM V$SESSION A
WHERE A.SID IN (SELECT DISTINCT T2.SID
                FROM V$LOCKED_OBJECT T1,
                     V$SESSION T2
                WHERE T1.SESSION_ID = T2.SID);
```

+ 查询cpu占用的语句(pid需替换成实际值)
```oracle
SELECT sql_text
FROM v$sqltext a
WHERE (a.hash_value, a.address) IN
      (SELECT DECODE(sql_hash_value, 0, prev_hash_value, sql_hash_value),
              DECODE(sql_hash_value, 0, prev_sql_addr, sql_address)
       FROM v$session b
       WHERE b.paddr =
             (SELECT addr FROM v$process c WHERE c.spid = 'pid'))
ORDER BY piece;
```