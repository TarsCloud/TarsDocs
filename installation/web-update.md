
# 目录
> * [升级说明](#chapter-1)
> * [扩展部分](#chapter-2)
> * [扩展说明](#chapter-3)

# 1 <span id="chapter-1"></span>升级说明

tarsweb-v3.0.3做了代码架构上的拆分, tarsweb只保留了框架的最核心功能, 其他扩展功能都通过服务市场去安装和扩展.

# 2 <span id="chapter-2"></span>扩展部分

tarsweb中以下功能部分都是以扩展实现的:
- 接口测试和压力测试, 通过市场上: `benchmark/benchmark-system`来扩展
- 网关web配置, 通过市场上: `base/gateway-system`来扩展
- dcache, 通过市场上: `dcache/dcache-system`来扩展

扩展的部分都依赖自己的mysql, tarsweb不再访问压力测试, 网关, dcache等依赖的数据库了.

# 3 <span id="chapter-3"></span>扩展说明

扩展的说明请参考: [服务扩展](../base/plugins.md), 原则上你也可以实现自己的扩展.

