## �鿴�͵���oracle���ݿ�����������

### ��¼

    sqlplus / as sysdba

#### �л��û�

*(֮�����л��û�����Ϊ�����û�ʹ��sqlplus����ʱ������ȱ�ٻ���������ִ��Ȩ��)*

    su oracle

#### ���û�������

��shell��ִ�����������. **ע����ֵҪ��ʵ�ʰ�װ�����ݿ�Ϊ׼**

    export ORACLE_HOME=/u01/app/oracle/12c
    export ORACLE_SID=orcl


### �鿴��ǰ��ʹ�õ�������

    SQL> select count(*) from v$process; 

### �鿴����������ͻỰ��

    SQL> show parameter processes;
    SQL> show parameter sessions;

### �޸�������

    SQL> alter system set processes = 2000 scope = spfile;
    SQL> alter system set sessions=2205 scope=spfile;
    SQL> shutdown immediate;
    SQL> startup;

### ִ��ʱ�쳣�Ĵ���

#### �ڴ治��

һ�����ʾ����:

    ORA-00821: Specified value of sga_target  **M is too small, needs to be at least **M

����ԭ���ǵ�����������������Ӧ�޸��ڴ��С��

    SQL> create pfile='/u01/app/oracle/12c/dbs/initGSP.ora' from spfile;
    SQL> quit

Ȼ����shell��, ʹ��oracle�û�ִ�У��޸��ڴ��С

    vi /u01/app/oracle/12c/dbs/initGSP.ora

�ҵ�

    *.sga_target=768m

�޸ĳɣ����汨���ֵ  needs to be at least **M)

    *.sga_target=1768m

Ȼ�������������ݿ�

    SQL> startup pfile='/u01/app/oracle/12c/dbs/initGSP.ora';

**�������һ������������ؽ���������ִ����������**

    SQL> create spfile from pfile='/u01/app/oracle/12c/dbs/initGSP.ora';
    SQL> shutdown immediate;
    SQL> startup;
