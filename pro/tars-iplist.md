## ip list 实时同步机制

在开源版本设计中, ip list是通过业务服务定时刷新tarsregistry来获取的, 但是这种方式存在以下问题:
- 业务服务需要定时刷新, 会给tarsregistry带来较大的压力
- 实时性不高, 一般都配置的60秒触发一次;
- 负载均衡无法做得更加实时, 比如添加或减小服务, 需要等待60秒才能生效, 如果要做无损发布, 则需要每发布一个服务都要等待>60s;

当然这种模式下, 由于客户端会自动屏蔽出问题的服务, 因此对业务调用影响不大, 但是如果能够更实时的刷新ip list会更有效的实现服务的负载均衡.

为了更有效的实现ip list的实时刷新, 建立了tarsregistry服务的数据级联机制.

### tarsregistry 数据级联机制

tarsregistry的数据级联机制即tarsnode和tarsregistry服务可以级联起来, 实时同步tarsregistry的所有关于ip list解析相关的数据, 实现机制如下:

- tarsregistry提供注册接口, 用于tarsnode注册到tarsregistry, 同步数据
- 每类数据都有时间戳, 用于判断是否需要更新;
- tarsnode定时上报心跳, 如果时间戳不一致, 则更新全量数据;
- 服务端角度, 只有obj数据和节点数据是增量更新机制变, 其他数据量较小, 一旦变更则全量推送
- 对于tarsregistry, obj数据如果是delete行为, 会全量加载, 并判断是否有变化, 根据变化通知下游

### ip list刷新机制变更

- tarsnode拥有tarsregistry全量的数据, 并且几乎实时的同步tarsregistry的数据;
- 业务服务的访问某服务时获取的ip list从tarsnode中获取;
- 业务服务可以更加密集的刷新ip list(比如3秒), 不会给tarsregistry带来更大的压力;


