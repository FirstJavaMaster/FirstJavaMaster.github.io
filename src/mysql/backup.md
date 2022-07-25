## 数据库备份

### 备份数据库

```shell
mysqldump -ucloud -p index_cloud > index_cloud.sql;
```

mysqldump的默认参数即可导出导入, 因为其默认开启了 `--opt` 参数, 
它是 `--add-drop-table --addlocks --create-options --disable-keys --extended-insert --lock-tables --quick --set-charset` 的缩写.

### 导出成csv文件

```
select *
from bm_whcd
into outfile '/tmp/test.csv'
fields terminated by ','
  optionally enclosed by '"'
  escaped by '"'
lines terminated by '\r\n';
```

### 免密备份

login-path是MySQL5.6开始支持的新特性。通过借助mysql_config_editor工具将登陆MySQL服务的认证信息加密保存在.mylogin.cnf文件（默认位于用户主目录） 。之后，MySQL客户端工具可通过读取该加密文件连接MySQL，避免重复输入登录信息，避免敏感信息暴露。

添加配置:

```
mysql_config_editor set --login-path=slkj -uslkj -p
```

其中可配置项

    -h,–host=name 添加host到登陆文件中
    -G,–login-path=name 在登录文件中为login path添加名字（默认为client）
    -p,–password 在登陆文件中添加密码（该密码会被自动加密）
    -u,–user 添加用户名到登陆文件中
    -S,–socket=name 添加sock文件路径到登陆文件中
    -P,–port=name 添加登陆端口到登陆文件中


显示配置:
```
mysql_config_editor print --login-path=slkj #显示执行的login-path配置
mysql_config_editor print --all             #显示所有的login-path信息
```

删除配置:
```
mysql_config_editor remove --login-path=slkj
```
此命令也可使用"添加"命令的配置项

重置配置:
```
mysql_config_editor reset --login-path=slkj
```

使用login-path登录:
```shell
mysql --login-path=slkj
```
