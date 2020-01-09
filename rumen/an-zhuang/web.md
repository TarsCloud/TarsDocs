
# 目录
> * [介绍](#chapter-1)
> * [模块说明](#chapter-2)
> * [权限说明](#chapter-3)
> * [检查问题](#chapter-4)
> * [web开发](#chapter-5)

# 1 <a id="chapter-1"></a>介绍

tars web主要用户管理Tars整体平台, 主要功能如下:
- 服务的部署, 发布, 扩容和配置
- 服务的监控, 统计信息查看
- 节点在线扩容
- 服务日志查看(点击服务名称)
- 权限管理

tars web安装通常是跟随一键安装脚本, 或者docker容器中自动安装好的, 源码安装可参见[Tars源码安装](source.md)

## 2 <a id="chapter-2"></a>模块说明

tars web由两个模块组成(nodejs实现的服务, 用koa框架实现):
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

注意, 如果你发现没有pm2命令, 一般都是node环境遍历没有生效, 你可以执行:
```
source ~/.bashrc
```

node的环境变量是在安装Tars时自动安装的, 注意用户是root.

## 3 <a id="chapter-3"></a>权限说明

tars-user-system 负责维护tars web的权限, 用户权限分为三种:
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

## 4 <a id="chapter-4"></a>检查问题

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

## 5 <a id="chapter-5"></a>web开发

如果你是nodejs开发者, 你也可以参与到web的开发中, web开发方式如下:

tars-node-web & tars-user-system 都是用nodejs + koa模块实现的.

tars-user-system模块对应的目录是web/demo 下.

两个模块的代码架构是一样的, 我们以tars-node-web讲解.

tars-node-web目录:
- app: 后台代码
- bin: 入口
- client: 前端页面代码, 页面代码用webpack组织, 发布前必须:npm run build
- config: 配置文件
- locale: 字符集目前支持(中文/英文)
- app.js: koa 入口
