# 关于

- TarsK8S 是为了将 Tars 部署在 K8S平台上而做的适应性改造项目.

- TarsK8S 改造的原则:
  1. 改造仅限于 framework 程序（tarsregistry ,tarsnode , tarsnotify , tarsconfig）
  2. 改造后的 framework 程序对业务服务暴露接口保持完全兼容
 
- 基于以上原则,现在的 tars服务程序在无需大改的情况下就可以迁移到 TarsK8S平台

- TarsK8S 暂时不支持 Set 

- TarsK8S 暂时不能部署 DCache 服务

# TarsK8S 的组成

+ tarscontroller
    > tars 框架在 k8s 平台的控制器程序        
+ tarsregistry
    > k8s tarsRegistry 与 tars 框架中的　tarsRegistry　提供相同的业务服务接口,具备相同的功能. 在实现上是通过 k8s list-watch 机制来感知集群内的各个服务状态,并对外提供服务

+ tarsweb
    > k8s tarsWeb 是对外提供的管理界面

+ tarsadmin
    > k8s tarsAdmin 与 tars 中的　tarsAdminRegistry 服务功能类似, 是 tarsWeb 操作集群的中间层.

+ tarsnotify
    > k8s tarsNotify 与 tars 中的 tarsNotify 服务有相同的业务服务接口,在实现上有细微的差别,但是对业务服务透明.
              
+ tarsconfig
    > k8s tarsConfig 与 tars 中的 tarsConfig 服务有相同的业务服务接口.在使用上有细微差别,具体参见 《TarsK8S的使用细节》
                        
+ tarsproprety ,tarsstat, tarsqueryproperty ,tarsquerystat,tarslog
    > tarsProperty ,tarsStat, tarsQueryProperty ,tarsQueryStat,tarsLog 与 tars 中的同名服务使用相同代码构建，完全兼容.

+ tarsagent
  > tarsAgent 是 tarsk8S 新增的框架服务. 用于提供主机端口检测,日志清理等辅助功能.
 
+ tarsnode
    > k8st tarsnode 程序经过轻量化改造，集成在每一个业务服务镜像中. 作为容器内的1号进程运行
            
+ tarsimage
    > 此服务程序是 tars-k8s  框架新增的服务，用于提供镜像生成功能, web平台上可以直接上传服务的tgz包, tarsimage会把tars服务制作成docker镜像.

+ 删除了 tarspatch , tarsAdminRegistry
