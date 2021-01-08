## 查看和调整oracle数据库的最大连接数

### 登录

    sqlplus / as sysdba

#### 切换用户

*(之所以切换用户是因为其他用户使用sqlplus命令时经常会缺少环境变量或执行权限)*

    su oracle

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
