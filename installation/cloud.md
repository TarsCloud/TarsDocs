# 目录

> - [介绍](#chapter-1)
> - [使用说明](#chapter-2)
> - [其他说明](#chapter-3)

## 1 <span id="chapter-1"></span>介绍

TARS提供了一个云部署环境(TARSCLOUD), 云环境主要是帮助用户维护了TARS的框架, 这样用户只需要购买CVM节点, 安装tarsnode连接过来即可.
## 2 <span id="chapter-2"></span>使用说明

主要步骤如下:
- 使用者可以通过 http://www.tarsyun.com, 申请完成TARS环境的部署, 完成申请后你可以打开web管理管平台;
- 完成部署并打开tars web以后, 并不表示你可以直接发布服务, 你还需要做以下工作;
- 在腾讯云上, 选择TARS CLOUD同一个地域的机房, 购买CVM;
- 将CVM的网段设置为TARS CLOUD为你分配的网段(安装完TARS后, web界面上会提示你的!);
- 在腾讯云控制台, 私有网络->云联网中, 申请网络连通, 完成这一步以后, 表示你的服务器和TARS框架网络是连通的(注意每一步的参数不要填错, 尤其是私有网络的名称, 网段信息!具体参考web上的提示!);
- 在你的服务器上运行tarsnode(注意必须手工安装tarsnode, 具体参考http://www.tarsyun.com 上, 点击安装tarsnode的提示!);

以上步骤细节, 在http://www.tarsyun.com 中创建TARS环境后, 点击"申请网络连通"后可以查看详细步骤

## 3 <span id="chapter-2"></span>其他说明

- 考虑到资源利用率, 如果你的环境超过7天没有活跃的tarsnode连接, 这该套环境会被自动删除!
- tarsweb还提供内网地址, 完成网连通以后, 你还可以通过内网来访问tars web
- 考虑到tarslog需要大硬盘, 且会记录远程业务日志, 因此你的服务被部署到TARS之前, 需要购买CVM连接到TARS环境, 并将tarslog服务迁移到你自己的节点上
- 目前每个账号最多创建2个TARS环境, 并第一个环境完成联通以后才能创建第二个环境