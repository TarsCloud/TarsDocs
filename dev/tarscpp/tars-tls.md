# TLS通信支持

OpenSSL是一个极其健壮、功能齐全的TLS和SSL协议库，也是一个通用的密码算法库。它为我们的通信提供了安全性和完整性保证。tars结合OpenSSL库支持TLS是一个常用的重要功能。 TLS协议和应用层的HTTP结合，即支持了https协议。 另外，TLS功能和[tars鉴权功能](../tars-auth.md)可以同时启用。 目前只有C++支持TLS功能，java即将支持。

具体的demo请参考examples/SSLDemo.

## tars tls使用

tars tls的使用非常简单，你不需要修改一行代码，只需要更改配置文件。 只需要按照下面四个步骤设置：

### 编译tars框架，支持tls

tars框架默认不开启tls, 配置cmake, 开启tls:

```text
cmake .. -DTARS_SSL=ON
```

### 修改被调用方的endpoint协议，将tcp或udp改为ssl

在tars服务平台，选中相应的服务，编辑servant，修改endpoint协议，将原先的tcp或udp改为ssl.

![](../../assets/tars_ssl_endpoint.png)

### 修改被调用方的配置文件添加证书配置

一般来说，TLS使用单向认证，即客户端认证服务器。所以被调用方一定要提供证书。 不再截图，如果不知道怎么改私有模板，查看[tars鉴权](../tars-auth.md)中的截图。在私有模板中添加配置如下：

```text
<tars>
    <application>
    
        <server>
           ca=/certs/ca.pem # ca公有证书，不验证客户端可以不填写
           verifyclient=0 # 不验证客户端
           cert=/certs/server-cert.pem # 服务器public证书
           key=/certs/server-key.pem # 服务器private证书
        </server>
    </application>
</tars>
```

现在，在web平台重启服务。

### 修改调用方的配置文件添加证书

由于主调方从tars registry可以获取被调方的地址，所以被调方这里关于地址的配置不需要改，只要添加证书就好了。 一般来说主调方不需要提供证书，这里只要配置一下PKI的公钥证书用来验证服务器就可以了。如果被调方要求主调方提供证书，那就需要提供证书，第2节已经描述过，这里不再重复。修改主调方私有模板，tls证书配置如下：

```text
<tars>
    <application>
        <client>
           ca=/certs/ca.pem # ca公有证书，验证服务器
           cert=/certs/client-cert.pem # 客户端public证书(不启用双向认证可以不要)
           key=/certs/client-key.pem # 客户端private证(不启用双向认证可以不要)
        </client>
    </application>
</tars>
```

现在，在web平台重启调用方。

没有修改一行代码（但需要重新编译链接），你的服务已经开始使用tls加密通信了。

## 其他你需要知道的

以上方式中, 服务器和客户端的正式都是唯一的, 即没有针对不同的端口绑定不同的证书, 实际上框架也允许你针对不同端口启用不同证书.

### 服务器端

你可以在配置文件中, adapter下增加自己的证书:

```
    <server>
        <Hello2Adapter>
            #ip:port:timeout
            endpoint = ssl -h 127.0.0.1 -p 9007 -t 10000
            #allow ip
            allow	 =
            #max connection num
            maxconns = 4096
            #imp thread num
            threads	 = 5
            #servant
            servant = TestApp.SSLServer.SSL2Obj
            #queue capacity
            queuecap = 1000000
            #tars protocol
	        protocol = tars
            ca          = ../examples/SSLDemo/certs/client1.crt
            cert        = ../examples/SSLDemo/certs/server1.crt
            key         = ../examples/SSLDemo/certs/server1.key
            #default is 0
            verifyclient = 1
        </Hello2Adapter>
    </server>    

```

### 客户端

客户端同样, 也可以在增加某个远端服务的证书, 而不使用统一的, 在配置文件中:

```
    <client>
        <TestApp.SSLServer.SSL2Obj>
            #server crt
            ca                      = ../examples/SSLDemo/certs/server1.crt
            #can be empty
            cert                    = ../examples/SSLDemo/certs/client1.crt
            #can be empty
            key                     = ../examples/SSLDemo/certs/client1.key
        </TestApp.SSLServer.SSL2Obj>
    </client>
```

另外TarsCpp1.x版本, 无法在单纯的客户端中使用, 这一点2.0版本已经修正.



