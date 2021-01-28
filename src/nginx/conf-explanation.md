## 配置文件相关

### 解决跨域问题

在 `nginx.conf` 文件的 `http` 节点中,添加以下字符即可实现跨域请求:

```
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Host $server_name;
proxy_set_header X-Forwarded-Proto "https";
```

### 证书申请/更新

检测SSL证书状态

    openssl x509 -in \*.idx365.com.cer -noout -text

也有可能需要使用以下的命令来指定编码方式:

    openssl x509 -inform pem -in cerfile.cer -noout -text
    openssl x509 -inform der -in cerfile.cer -noout -text

如果申请泛域名,则最好是使用DNS验证的方式

```
./acme.sh --issue -d *.idx365.com --dns --yes-I-know-dns-manual-mode-enough-go-ahead-please
```

可能会提示将新的TEXT记录设置解析,照做后重新运行它提示的命令即可:
```
acme.sh --renew -d *.idx365.com --yes-I-know-dns-manual-mode-enough-go-ahead-please
```