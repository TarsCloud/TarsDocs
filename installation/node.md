
# 目录
> * [TarsNode部署](#chapter-1)
> * [web在线部署](#chapter-2)
> * [节点机脚本部署](#chapter-3)
> * [docker化部署](#chapter-4)

# 1 <span id="chapter-1"></span>TarsNode部署

当完成Tars框架部署之后, 如果希望业务服务发布到节点服务器, 就需要将节点服务器连接到框架上, 这步操作即在节点服务器上安装tarsnode.

安装Tarsnode有三种模式:
- web平台在线部署
- 节点机脚本部署
- docker化部署

tarsnode运行的用户可以不用是root, 可以安装以后, 将目录切换到其他用户, 例如当前是root用户, 希望切换到tars用户下

```
#root用户下, 屏蔽crontab, 注释掉tarsnode的监控
#* * * * * /usr/local/app/tars/tarsnode/util/monitor.sh

#停掉tarsnode(注意crontab监控也要屏蔽, 否则会被自动拉起)
/usr/local/app/tarsnode/util/stop.sh

#修改目录权限
chown -R tars:tars /usr/local/app/tarsnode

#切换到tars用户
su tars

#启动
/usr/local/app/tarsnode/util/start.sh

#增加当前用户的crontab的监控
* * * * * /usr/local/app/tars/tarsnode/util/monitor.sh
```

# 2 <span id="chapter-2"></span>web在线安装

web(>=1.4.1)提供了在线安装tarsnode的功能, 安装时需要输入节点机的ip, 密码等信息, 完成自动tarsnode的安装(需要自己增加crontab监控tarsnode)

注意:
- tarsnode.tgz安装包是在部署时, 安装脚本自动copy到web/files目录下的
- 如果不存在, 需要自己生成tarsnode.tgz, 如下操作
>- 编译framework, make install
```
cd /usr/local/tars/cpp/framework/servers
tar czf tarsnode.tgz tarsnode
cp tarsnode.tgz yourweb/files
```

在crontab配置一个进程监控，确保TARS框架服务在出现异常后能够重新启动。
```
* * * * * /usr/local/app/tars/tarsnode/util/monitor.sh
```

**注意:节点机需要支持wget命令, 否则无法从web拉取tarsnode到本机**

# 3 <span id="chapter-3"></span>节点机脚本安装

节点机上也可以自动去安装tarsnode, 前提是节点机能正常访问web, 且web支持online安装

在节点上运行:
```
wget http://webhost/get_tarsnode?ip=xxx&runuser=root
chmod a+x get_tarsnode
./get_tarsnode
```

参数说明:
- ip: 本机ip
- runuser: 运行tarsnode的用户

即完成tarsnode的安装, 然后添加监控:

在crontab配置一个进程监控，确保TARS框架服务在出现异常后能够重新启动。
```
* * * * * /usr/local/app/tars/tarsnode/util/monitor.sh
```

# 4 <span id="chapter-4"></span>docker化安装

- 
如果希望业务服务运行在一个docker里面, 可以采用该方式:

```sh
docker pull tarscloud/tars-node:stable
```

```sh
docker run -d --net=host -eINET=eth0 -eWEB_HOST=xxxxx \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/tars-node:stable

#例如:
docker run -d --net=host -eINET=eth0 -eWEB_HOST=http://172.16.0.7:3000 \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/tars-node:stable     
```

**注意: http://172.16.0.7:3000 是TarsWeb的访问地址**

tarsnode docker的制作脚本参考[Dockerfile](https://github.com/TarsCloud/TarsDocker/blob/master/tarsnode/Dockerfile)

该Docker内置了java, nodejs等运行时支持, 即可以把java, nodejs服务发布到docker里面(docker里面安装了jdk, node, php环境)

这种方式通常使用在k8s的部署中, 此时不需要--net=host, docker被k8s管理.

