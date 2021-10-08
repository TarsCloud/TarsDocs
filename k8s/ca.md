
# 证书问题

如果有需要, 你可以替换控制器的证书, 一般你不需要替换证书!
```
openssl genrsa -out ca.key 2048 
openssl req -new -x509 -key ca.key -out ca.crt -days 9999
```

**注意, 默认超时时间是9999天!**

生成的公私钥替换安装目录以及yaml文件中的, 注意yaml文件的caBundle是ca base64后的字符串, 一般情况下可以不管!