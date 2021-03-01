
# 目录
> * [介绍](#chapter-1)
> * [模块说明](#chapter-2)
> * [权限说明](#chapter-3)
> * [模块调用](#chapter-4)
> * [检查问题](#chapter-5)
> * [Token管理](#chapter-6)
> * [密码遗忘处理](#chapter-7)
> * [web开发](#chapter-8)

# 1 <span id="chapter-1"></span>介绍

tars web主要用于开发或运维人员管理Tars整体平台, 主要功能如下:
- 服务的部署, 发布, 扩容和配置
- 服务的监控, 统计信息查看
- 节点在线扩容
- 服务日志查看(点击服务名称)
- 权限管理

tars web安装通常是跟随一键安装脚本, 或者docker容器中自动安装好的, 源码安装可参见[Tars源码安装](source.md)

## 2 <span id="chapter-2"></span>模块说明

###  tars web < v2.4.7

tars web在v2.4.7之前由两个模块组成(nodejs实现的服务, 用koa框架实现):
- tars-node-web: 核心功能界面, 默认绑定本机: 0.0.0.0:3000 
- tars-user-system: 用户权限系统, 默认绑定本机: 0.0.0.0:3001

系统安装好以后, 是被pm2来管理的, 可以查看:
```
pm2 list
```

可以查看到模块的状态.

常见命令:
- 启动模块
```
pm2 start tars-node-web
pm2 start tars-user-system
```

- 停止模块
```
pm2 stop tars-node-web
pm2 stop tars-user-system
```
- 重启模块
```
pm2 start tars-node-web
pm2 start tars-user-system
```

###  tars web >= v2.4.7

tars web在v2.4.7之后, 将用户体系和逻辑模块合并到一起, 并内嵌了ldap, 未来会打开ldap, 通过ldap来完成和其他鉴权系统的对接

系统安装好以后, 是被pm2来管理的, 可以查看:
```
pm2 list
```

可以查看到模块的状态.

常见命令:
- 启动模块
```
pm2 start tars-node-web
```

- 停止模块
```
pm2 stop tars-node-web
```
- 重启模块
```
pm2 start tars-node-web
```

### 其他说明

注意, 如果你发现没有pm2命令, 一般都是node环境遍历没有生效, 你可以执行:
```
source /etc/profile
```

node的环境变量是在安装Tars时自动安装的, 注意用户是root.

## 3 <span id="chapter-3"></span>权限说明

用户权限分为三种:
- admin: 管理员
- operator: 运维人员
- developer: 开发人员, 不能发布以及修改配置, 重启等写操作

权限的粒度可以到应用 或者 服务级别.

在web管理平台上, 点击右上角箭头, 进入用户中心, 就可以创建用户以及给用户赋值权限.

比如: 你想给运维用户test, app为Test, 服务为HelloServer的权限:
- 在用户管理界面, 以operator创建用户test
- 在权限管理界面, 新增权限, 注意标示为: Test.HelloServer

如果你想test用户, app为Test的所有服务权限, 则标示为: Test 即可

你可以在web管理平台, 点击具体服务->点击权限管理-> 在运维, 开发的输入框中输入用户名称, 多用户以;分隔

**注意: 在v2.4.7之前的版本, 由tars-user-system负责维护tars web的权限, v2.4.7及之后的版本和逻辑服务合并到一起了**

## 4 <span id="chapter-4"></span>模块调用

###  tars web < v2.4.7

tars-node-web & tars-user-system默认是部署在同一台机器上, 并且都绑定了0.0.0.0 (即也绑定了127.0.0.1)

tars-node-web通过localhost(127.0.0.1)来访问tars-user-system, 如果未绑定127.0.0.1, 则无权限, 此时需要修改tars-user-system模块的配置(demo/config/loginConf.js), 修改ignoreIps, 开放白名单.

正常情况你可以通过机器的外网ip来访问, 例如:http://xxx.xxx.xxx.xxx:3000 web管理平台

如果web & demo前面挂了nginx代理, 反向代理到不同端口(一个3000, 一个3001)来访问, 则需要设置不同域名来访问, web & demo 的登录态通过cookie传递, 因此需要部署在同一个根域名下面.

比如: web的nginx的入口域名是: http://user.tars.com, demo的nginx域名是: http://auth.tars.com, (注意根域名都是tars.com), 那么需要在web服务器上设定环境变量:

```
export USER_CENTER_HOST=http://auth.tars.com
export COOKIE_DOMAIN=.tars.com
```

###  tars web >= v2.4.7

tars-node-web绑定了0.0.0.0 (即也绑定了127.0.0.1), 并且内部鉴权会访问自己的127.0.0.1 来实现.

正常情况你可以通过机器的外网ip来访问, 例如:http://xxx.xxx.xxx.xxx:3000 web管理平台

由于鉴权模块和web逻辑模块合并, 通过nginx反向代理来访问更加简单, 只需要反向道理到3000端口即可.

## 5 <span id="chapter-5"></span>检查问题

###  tars web < v2.4.7

如果tars web运行异常, 比如:页面打不开, 可以通过以下方式检查:

- 先把web模块停止
```
pm2 stop tars-node-web
```

- 进入目录, 手工启动服务
```
cd /usr/local/app/web
npm run dev
```

npm run dev 会把错误输出在屏幕上, 根据错误判断如果修改, ctrl + c 退出 npm run dev

bug解决后, 重新用pm2 启动服务:
```
pm2 start tars-node-web
pm2 start tars-user-system
```

两个重要的web日志:
- tars-node-web模块运行过程中的日志, 会输出在 /usr/local/app/web/log/ 目录下, 便于定位问题
- tars-user-system模块运行过程中的日志, 会输出在 /usr/local/app/web/demo/log 目录下, 便于定位问题.

###  tars web >= v2.4.7


如果tars web运行异常, 比如:页面打不开, 可以通过以下方式检查:

- 先把web模块停止
```
pm2 stop tars-node-web
```

- 进入目录, 手工启动服务
```
cd /usr/local/app/web
npm run dev
```

npm run dev 会把错误输出在屏幕上, 根据错误判断如果修改, ctrl + c 退出 npm run dev

bug解决后, 重新用pm2 启动服务:
```
pm2 start tars-node-web
```

重要的web日志:
- tars-node-web模块运行过程中的日志, 会输出在 /usr/local/app/web/log/ 目录下, 便于定位问题

## 6 <span id="chapter-6"></span>Token管理

Web的用户模块, 提供了token管理功能, 即可以创建token, 并通过web api来调用和获取相关数据, [请参见](../dev/tars-web-api.md)

## 7 <span id="chapter-7"></span>密码遗忘处理

如果admin的密码遗忘, 目前只能重置数据库中相关表的字段来解决:
- 登录mysql
- 查看db: db_user_system
- 修改表: t_user_info
- 重置admin用户记录, 将password重置为空

登录web时, 将会提示设置admin密码.

## 8 <span id="chapter-8"></span>web开发

如果你是nodejs开发者, 你也可以参与到web的开发中, 目前采用: nodejs + koa实现, web开发方式如下:

目录:
- app: 后台代码
- bin: 入口
- client: 前端页面代码, 页面代码用webpack组织, 发布前必须:npm run build
- config: 配置文件
- locale: 字符集目前支持(中文/英文)
- app.js: koa 入口
