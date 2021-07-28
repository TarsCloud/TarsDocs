# 安装

## 支持一键安装（需要先具备tarscpp编译环境）
```
    git clone https://github.com/TarsCloud/TarsGateway.git
    cd TarsGateway/install;
    ./install.sh webhost token server_ip tars_db_ip tars_db_port tars_db_user tars_db_pwd

```
### 安装参数如下：
* webhost                  TarsWeb管理端的host或ip:port
* token                    TarsWeb管理端的token，可以通过管理端获取http://webhost:3001/auth.html#/token
* server_ip                GatewayServer部署的ip，目前这里只支持一个，如果需要更多，后面直接在平台上面扩容即可。
* tars_db_ip               tarsdb 所在的数据库服务器ip。
* tars_db_port             tarsdb 端口。
* tars_db_user             tarsdb 用户名（需要有建库建表权限）。
* tars_db_pwd              tarsdb 密码。

### 例如：
```
    ./install.sh 172.16.8.220:3000 036105e1ebfc13843b4db0edcd000b3d9f47b13928423f0443df54d20ca65855 172.16.8.220 172.16.8.221 3306 tars tars2015
```
### 验证安装结果：
在浏览器打开 http://${server_ip}:8200/monitor/monitor.html , 如果能正常显示 hello TupMonitorxxx 就表示安装成功。

### 修改默认安装端口
将server.json中的8200端口值修改为你需要的端口，然后重新执行./install.sh步骤，最后在TarsWeb中将GatewayServer重启即可。

## 手动部署安装（需要先具备tarscpp编译环境）
1. 准备tarscpp 编译环境；
2. 获取源码： git clone https://github.com/TarsCloud/TarsGateway.git
3. 编译： cd TarsGateway; mkdir build; cd build; make GatewayServer && make GatewayServer-tar
4. 在tarsweb 管理平台上面，部署tars.GatewayServer，该服务有两个servant，即部署两个Obj，如下：
    * tars.GatewayServer.ProxyObj           非tars协议  5个线程  端口 xxxx (一键部署的端口默认为8200)
    * tars.GatewayServer.FlowControlObj	    tars协议    1个线程  端口 xxxx
5. 添加配置文件：GatewayServer.conf，内容参考 TarsGateway/conf/GatewayServer.conf
6. 发布服务
7. 验证：同上一键部署 验证安装结果。
