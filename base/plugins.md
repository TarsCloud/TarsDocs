# 框架拓展思路

Tars 虽然提供了基础的微服务框架, 但是一个好的系统还需要大量周边支撑服务甚至业务服务, 一味的在 Tars 框架中扩展相关功能会导致框架越来越庞大, 且不利于维护. 因此 Tars 微服务框架采用了全新的思路构建框架, 通过打造Tars 微服务市场使用者可以在市场上安装服务或产品, 扩展框架或业务能力, 从而尽量减少框架的升级.

## 服务市场

## TarsWeb 嵌入

如果单纯的从服务市场上安装后台服务, 这种能力仍然先得单薄, 我们目标是安装的服务可以提供能力能嵌入到 TarsWeb 中, 仿佛 TarsWeb 的自带的基础能力.

目前框架(>=TarsFramework:v3.0.9, >=TarsWeb:v3.0.3)已经将压力测试, 网关, DCache 独立成单独的产品, 上架到市场上, 通过市场安装即可扩展 TarsWeb 的基础能力.

## 嵌入目标

对于扩展的后台服务而言, 通常是比较简单的, 安装到框架上即可. 但是如果这些后台服务对应了一个 web 管理平台, 那么就需要考虑如何更好的访问到这些管理平台(比如压力测试的 web 管理平台).

通常我们希望它能无缝和 TarsWeb 衔接到一起, 用户访问时打开 TarsWeb 即可.

要做到这一点就需要解决以下几个问题:

- 网络联通性
- 权限问题
- 嵌入在哪

### 网络联通性

通常而言部署在 Tars 框架上的 web管理服务, 都希望通过浏览器能访问到, 你需要能访问到它的 ip 或者通过反向代理访问. 但是如果作为 TarsWeb 的扩展服务, 安装后都需要在配置访问的网络联通性, 会给使用带来比较大的麻烦, 为了解决这个问题, TarsWeb 直接提供了反向代理到这些服务(因为 TarsWeb 部署的服务器通常和这些服务网络层都是相同的), 从而解决了网络联通性的问题.

那么这里有一个核心的问题: 不同扩展服务的转发路由必须不同, 否则 TarsWeb 无法正确路由, 这个问题后续会介绍.

### 嵌入方式

这些扩展的管理平台嵌入到 TarsWeb 时, 提供了三种扩展方式:

- 全局扩展: 通过顶部扩展服务下拉框选择(比如网关服务), type=1
- 服务级别扩展: 在服务管理界面, 增加独立 tab(比如压力测试服务), 这种扩展情况下, 访问时会在url中传递一个服务 id 过来(treeid), type=2
- 运维扩展: 在运维管理界面, 增加独立 tab, type=3

type 的值为 1, 2, 3, 用于注册时使用, 请参见后续文档.

### 基础接口

扩展的 web 服务有时候需要访问框架的接口, 此时如何解决?

通常访问接口有两种模式:

- 前端页面
  由于扩展服务是被 TarsWeb 反向代理的, 如果页面层面需要访问框架数据, 可以直接访问 TarsWeb 提供的 http 接口即可!

- 后端服务

请参考网关的 web 代码(https://TarsCloud/TarsGateway.git), src/common/AdminService.js, 提供了: 注册扩展服务, 验证框架 ticket, 获取用户权限等接口

### 权限问题

TarsWeb 转发请求时, 实际上 cookie 中会将 ticket 带过来, 因此你的服务可以获取到 ticket, 并通过 AdminService.checkTicket 来验证 ticket 的合法性.

## 扩展服务

如果希望扩展服务能被 TarsWeb 识别, 需要在你的 web 服务启动时, 调用`AdminService.registerPlugin` 将自己注册到 TarsWeb 中, 可以参考这个函数的说明:

```js
AdminService.registerPlugin = async (name, name_en, obj, type, path) => {

    ...
}
```

示例代码如下:

```js
const registerPlugin = async () => {
  if (process.env.TARS_CONFIG) {
    let config = new Configure();
    config.parseFile(process.env.TARS_CONFIG);

    webConf.gatewayObj =
      config.get("tars.application.server.app") +
      ".GatewayServer.FlowControlObj";

    try {
      const rst = await AdminService.registerPlugin(
        "网关管理平台",
        "TarsGatewayWeb",
        config.get("tars.application.server.app") +
          "." +
          config.get("tars.application.server.server") +
          ".WebObj",
        1,
        webConf.path
      );

      console.log(rst);
    } catch (e) {
      console.log(e.message);
    }
  } else {
    try {
      const rst = await AdminService.registerPlugin(
        "网关管理平台",
        "TarsGatewayWeb",
        "Base.TarsGatewayWeb.WebObj",
        1,
        webConf.path
      );

      console.log(rst);
    } catch (e) {
      console.log("error:", e.message);
    }
  }
};
```

registerPlugin 的参数说明如下:

- name: 中文名称
- name_en: 英文名称
- obj: 部署在 Tars 框架上 obj 名称, TarsWeb 会转发请求到个 obj 的服务(目前只会转到第一个活跃的)
- type: 扩展类型(1, 2, 3)
- path: 路由地址, 必须以`/plugins/${group}`开头, 其中${group}是上架到 Tars 服务市场上的 group, 从而避免相互冲突, 比如网关是: /plugins/base/gateway, 压力测试系统是: /plugins/base/benchmark

**注意这里path是TarsWeb用来路由请求到你的服务的, 因此你的服务所有请求都必须是这个path路径!**