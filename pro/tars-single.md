## 框架单节点机制

企业版本框架除了支持集群化机制以外, 也支持单节点机制, 在某些轻量级场合可以使用.


### 数据目录

tarsregistry的数据文件目录在: `/usr/local/app/tars/tarsnode/data/tars.tarsregistry/data/DB` 目录下

### 框架扩容

单节点的框架如果希望扩容成集群化机制, 只需要将DB文件copy多其他节点, 然后启动时设置为集群模式启动即可(多台节点都需要重新启动).

注意: tarspatch, tarscollect也需要做同理处理, 当然你可以让tarsregistry是集群化机制, tarspatch, tarscollect仍然是单节点机制. 