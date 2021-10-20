
# 目录
> * [升级注意事项](#chapter-1)

# 1 <span id="chapter-1"></span>升级注意事项

- TarsFramework>=3.0.1, TarsWeb>=2.4.19, TarsCpp >= 3.0.0 开始支持调用链, 其他语言的库还在陆续支持中!
- 调用链数据的收集以及计算都在tarslog服务中完成
- 调用链数据的存储在外部的es中, 该es需要自己部署, es的信息需要配置到tarslog的模板中
- 如果是升级framework, tarslog需要自己添加一个TopologyObj, 这个端口是给web使用的(查询调用链信息)
- 如果是全新安装framework, 则不需要, 部署脚本会自动添加这个obj

关于调用链的继续信息[请参见文档](../dev/tars-trace.md)