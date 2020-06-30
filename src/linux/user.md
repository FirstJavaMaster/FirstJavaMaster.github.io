## 用户账户

### 控制root用户的ssh登录

编辑文件 `sudo vi /etc/ssh/sshd_config`

`PermitRootLogin`这个参数，它的值可以为`yes/no/without-password`


    yes #允许root用户以任何认证方式登录（貌似也就两种认证方式：用户名密码认证，公钥认证）

    without-password #只允许root用public key认证方式登录

    no #不允许root用户以任何认证方式登录

### 禁用密码登录

编辑文件 `sudo vi /etc/ssh/sshd_config`

`PasswordAuthentication` 参数, 修改为 `no`


### 踢掉某已经登录的用户

`w` 用于查看所有已经登录的用户

`pkill -kill -t pts/1` 踢掉指定用户


