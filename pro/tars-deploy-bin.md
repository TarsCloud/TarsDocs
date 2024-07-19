
# 安装tars框架企业版本

使用tarsctl + framework二进制包来完成tars企业版本框架的安装.

## 安装说明

- 正常情况下, 需要选取2n+1节点服务器, 从而完成框架组件的容灾;
- 如果有需要, 也可以在同一个服务器上安装三次, 每次安装在不同目录, 使用不同的节点名称;
- 也可以将框架部署成单节模式;

**tars企业版本可以在同一台节点上安装多个tarsnode, 只需要每个tarsnode的节点名称不同即可!**

## 安装框架

### 安装framework

- 得到安装包: framework.tgz, 源码下可以执行: `make publish`, 得到安装包: framework.tgz
- 解压framework.tgz, 目录下得到tarsctl
- 通常至少在2n+1台节点(建议3台)的运行以下命令安装framework
```shell
tarsctl install framework --mode=cluster --install-path=/usr/local/app --file=framework.tgz --locator="tcp -h xxx1 -p yyy1:tcp -h xxx2 -p yyy2:tcp -h xxx3 -p yyy3" --localip=[xxx] --node-name=tarsnode-0 --registry=\"tcp -h xxx1 -p xxx2\"
```
>- file: 制定了安装包文件, 如果是源码编译出来的, 通过`make publish`来生成
>- mode: 集群机制, 默认是集群机制, 如果希望只部署一个节点(单节点机制), 则mode设置为single
>- install-path: 指定了安装目录, 当前用户需要有这个目录的权限, 缺省为: /usr/local/app
>- locator: 指定了主控的地址, 指定了三个地址, 必须和三台节点相匹配
>- localip: 本节点的ip
>- node-name: 当前安装的节点名称, 指定了`tarsnode`的节点名称(必须唯一)
>- registry: 当前安装的主控节点QueryObj绑定的地址, 必须是locator中的一个(如果是单节点机制, registry不需要设置)
>- tarslog只部署在第一台节点`xxx1`上, 其他服务都是部署了三台节点

**注意: 可以在同一台机器上安装三个framework, 注意每次的安装目录(install-path)和节点名称(node-name)要不同, 另外注意locator和registry的地址要争取(比如ip相同, 端口不同)**

### 安装TarsNode节点

安装框架时, 每台节点会自动安装tarsnode, 当需要添加新的节点时, 只需要安装节点即可, 可以通过以下方式在新的节点上安装tarsnode
```shell
tarsctl install node --install-path=/usr/local/app --file=framework.tgz --locator="tcp -h xxx1 -p yyy1:tcp -h xxx2 -p yyy2:tcp -h xxx3 -p yyy3" --localip=[xxx] --node-name=tarsnode-x
```
>- file: 制定了安装包文件, 如果是源码编译出来的, 通过`make publish`来生成
>- install-path: 指定了安装目录, 当前用户需要有这个目录的权限, 缺省为: /usr/local/app
>- locator: 指定了主控的地址
>- localip: 本节点的ip, 如果节点上多张网卡, 你希望都绑定, 你可以使用`0.0.0.0`来支持多网卡

### 安装web平台

- 得到web安装包web.tgz
- 在节点上安装web.tgz

```shell
tarsctl install web --install-path=/usr/local/app --file=web.tgz --locator="tcp -h xxx1 -p yyy1:tcp -h xxx2 -p yyy2:tcp -h xxx3 -p yyy3"
```
>- install-path: 指定了安装目录, 当前用户需要有这个目录的权限
>- web: 指定了web安装包
>- locator: 指定了主控的地址

你可以在多台机器上安装台web管理平台, 前面配置负载均衡即可.

启动管理平台:
```shell
cd /usr/local/app/web

```
## 框架启动方式

启动组件服务有两种方式, 一种是手动拉起, 一种是自动拉起

手动拉起:
- 适用于开发调试阶段, 一般只在一台节点上启动, 方便调试, 通过`tarsctl framework start --install-path=/usr/local/app` 来启动
- 通过: `tarsctl framework stop --install-path=/usr/local/app` 来关闭服务
- 通过: `tarsctl framework restart --install-path=/usr/local/app` 来重启服务
- 通过: `tarsctl framework list --install-path=/usr/local/app` 来查看服务的状态

**注意: install-path参数和安装时指定的目录相同**

这种模式下, 如果tarsnode挂掉, 是不会被自动拉起的, 其他服务挂掉会被tarsnode启动拉起, 这种模式你需要自己监控tarsnode的死活, 并拉起.

自动拉起:
- 自动拉起只适用于linux系统, 通过系统的systemd来拉起服务
- 适用于生成环境, tarsnode会被自动拉起, 其他服务挂掉会被tarsnode自动拉起, tarsnode的启动通过linux系统systemd服务来实现
- 为了方便管理, tarsnode启动建议不采用root用户, 采用普通用户来启动, 这样所有服务都以普通用户启动, 因此如果涉及到特殊的目录权限, 需要自己控制

实现方式如下:
- 切换到`root`用户, 执行: loginctl enable-linger $user, 注意: $user 要替换为普通用户名称
- 切换到`$user`用户: su $user
- `tarsctl framework systemd --install-path=....`, 在systemd系统服务中安装tarsnode.service, 注意安装的service名称为: `$nodename.service`
- `systemctl enable $nodename.service`, 注意: $nodename 要替换为当前节点的名称
- 启动tarsnode服务: `systemctl --user start $nodename.service`, 注意: $nodename 要替换为当前节点的名称

这种模式下, 会在 `~/.config/systemd/user/$nodename.service` 生成文件, 通过systemctl来启动/重启/停止服务
- 启动: systemctl --user start $nodename.service
- 重启: systemctl --user restart $nodename.service
- 停止: systemctl --user stop $nodename.service

**注意: systemd只管理了tarsnode服务, 其他组件服务都被tarsnode所管理**
