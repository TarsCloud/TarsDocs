## 命令行控制终端

TARS企业版本提供了命令行控制终端 `tarsctl`, 来完成对框架的管理, 包括:

```md
Usage:
   bin/tarsctl help --tars=tars.conf
                  查看帮助 ok
   bin/tarsctl adapter list --target=[App.HelloServer or App.HelloServer_192.168.0.1]
                  获取adapter列表
   bin/tarsctl adapter servants --target=[App.HelloServer]
                  获取服务下的所有servant名称列表
   bin/tarsctl adapter nodes  --node_names=[xxx,xxx]
                  获取指定nodeName列表下的adapter
   bin/tarsctl adapter obj --target=[App.HelloServer_192.168.0.1] --obj=[HelloObj]
                  根据obj获取指定的adapter
   bin/tarsctl adapter delete --target=[App.HelloServer.HelloObj]
                  根据Obj名称删除指定的adapter
   bin/tarsctl adapter update --id=[id] --node-name=[xxxx] --servant=[App.HelloServer.HelloObj] --thread-num=[xxx] --endpoint=[xxx] --max-connections=[xxx] --allow-ip=[xxx] --queuecap=[xxx] --queuetimeout=[xxx]  --protocol=[tars/not_tars]
                  编辑servant: 
                     id: 目标servantId
                     node-name: 节点名称[192.168.1.1]
                     thread-num: 线程数[5]
                     endpoint: 节点[tcp -h 192.168.0.169 -t 60000 -p 12000]
                     max-connections: 最大连接数[2000]
                     servant: OBJ名称, 例如: App.HelloServer.HelloObj
                     allow-ip: 允许IP
                     queuecap: 队列长度[10000]
                     queuetimeout:队列超时(ms)[6000]
                     protocol:协议[tars]
   bin/tarsctl adapter listObjFir --servants=[APP.HelloServer.Obj1, tars.tarsAdminRegistry.AdminRegObj]
                  获取adapter列表
   bin/tarsctl admin help
                  查看帮助
   bin/tarsctl admin create-user --uid=[xxx] --password=[xxx]
                  创建用户
   bin/tarsctl admin list-user
                  获取用户列表
   bin/tarsctl admin get-user --uid=[xxx]
                  查看某个用户信息
   bin/tarsctl admin modify-pass --uid=[xxx] --password=[xxx]
                  修改用户密码
   bin/tarsctl admin delete-users --uids=[xxx,xxx]
                  删除用户列表
   bin/tarsctl admin create-token --uid=[xxx] --expireTime=[xxx]
                  创建指定用户的token
   bin/tarsctl admin list-token --uid=[xxx]
                  获取某个用户的所有token, 如果没有指定uid, 获取所有系统中的token
   bin/tarsctl admin delete-token --token=[xxxxx]
                  删除指定token
   bin/tarsctl admin get-token --token=[xxx]
                  根据token获取token info
                  获取有token
   bin/tarsctl admin valid-token --token=[xxxx] --valid=[1/0]
                  设置token有效性
   bin/tarsctl admin create-auth --flag=[application or application.server_name] --role=[admin/operator/developer] --uid=[_test]
                  为用户创建权限
   bin/tarsctl admin auth-list --uid=[_test]
                  获取指定用户的权限列表, 如果不指定uid, 获取所有auth
   bin/tarsctl admin delete-auth --uid=[test] --roles=[admin,operator,developer] --flag=[application or application.server_name]
                  删除指定的用户权限
   bin/tarsctl application list
                  列出所有application
   bin/tarsctl application create --name=[App]
                  创建一个application
   bin/tarsctl application delete --name=[App]
                  删除一个application
   bin/tarsctl application servers --name=[App]
                  列出应用下所有的服务的名称
   bin/tarsctl auth help
                  查看帮助
   bin/tarsctl auth list
                  获取用户自己的权限列表
   bin/tarsctl auth serverList
                  获取服务的权限列表
   bin/tarsctl business list
                  列出所有业务
   bin/tarsctl business create --name=[Business] --show-name=[系统应用]
                  创建一个业务
   bin/tarsctl business update --newName=[Business] --oldName=[OldBusiness] --show-name=[系统应用]
                  修改业务的显示名称
   bin/tarsctl business view --name=[Business]
                  查看业务中的所有的app
   bin/tarsctl business addApp --name=[Business] --app=[App]
                  在业务中添加app
   bin/tarsctl business delApp --name=[Business] --app=[App]
                  在业务中删除app
   bin/tarsctl business delete --name=[Business]
                  删除一个业务
   bin/tarsctl config list --target=[App or App.HelloServer]
                  应用级别配置或者服务配置, target: App or App.HelloServer
   bin/tarsctl config create --target=[App] --name=[config name] --file=[config file] --reason=test --update
                  创建某个应用配置, --update: 存在则修改配置
   bin/tarsctl config create --target=[App.HelloServer] --name=[config name] --file=[config file] --reason=test --update
                  创建某个服务配置, --update: 存在则修改配置
   bin/tarsctl config create --target=[App.HelloServer_192.168.1.1] --name=[config name] --file=[config file] --reason=test --update
                  创建某个节点配置, --update: 存在则修改配置
   bin/tarsctl config view --target=[App] --name=[config name]
                  查看某个应用配置
   bin/tarsctl config view --target=[App.HelloServer] --name=[config name]
                  查看某个服务配置
   bin/tarsctl config view --target=[App.HelloServer_192.168.1.1] --name=[config name]
                  查看某个服务的节点配置
   bin/tarsctl config view --target=[App.HelloServer_192.168.1.1] --name=[config name] --with-merge
                  查看某个服务的节点配置, 并合并节点配置, --with-merge: 是否合并业务配置
   bin/tarsctl config delete --target=[App] --name=[config name]
                  删除某个应用配置
   bin/tarsctl config delete --target=[App.HelloServer] --name=[config name]
                  删除某个服务配置, 会一并删除所有节点配置
   bin/tarsctl config delete --target=[App.HelloServer_192.168.1.1] --name=[config name]
                  删除某个服务节点配置
   bin/tarsctl config update --target=[App] --name=[config name] --file=[config file] --reason=test --create
                  更新某个配置: --create: 不存在则创建
   bin/tarsctl config update --target=[App.HelloServer] --name=[config name] --file=[config file] --reason=test --create
                  更新某个配置: --create: 不存在则创建
   bin/tarsctl config update --target=[App.HelloServer_192.168.1.1] --name=[config name] --file=[config file] --reason=test --create
                  更新某个节点配置 --create: 不存在则创建
   bin/tarsctl config history --target=[App] --name=[config name]
                  查看某个应用配置所有历史变更记录
   bin/tarsctl config history --target=[App.HelloServer] --name=[config name]
                  查看某个服务配置所有历史变更记录
   bin/tarsctl config history --target=[App.HelloServer_192.168.1.1] --name=[config name]
                  查看某个服务的节点配置所有历史变更记录
   bin/tarsctl config viewHistory --id=[historyId]
                  查看指定的历史配置内容
   bin/tarsctl config restore --target=[App] --name=[config name] --id=[historyId] --reason=test
                  恢复某个应用配置指定的历史配置
   bin/tarsctl config restore --target=[App.HelloServer] --name=[config name] --id=[historyId] --reason=test
                  恢复某个服务配置指定的历史配置
   bin/tarsctl config restore --target=[App.HelloServer_192.168.1.1] --name=[config name] --id=[historyId] --reason=test
                  恢复某个节点配置指定的历史配置
   bin/tarsctl framework check
                  检查框架所有服务
   bin/tarsctl framework ping --obj=[obj]
                  ping所有obj服务
   bin/tarsctl framework iplist --obj=[obj] --node-name=[xxx]
                  获取某个obj对应的活跃和非活跃的ip list, 如果不指定node-name则从主控获取, 如果指定node-name则从对应的tarsnode获取
   bin/tarsctl framework install --install-path=[/usr/local/app] --file=tars.tgz --locator="tcp -h xxx1 -p yyy1:tcp -h xxx2 -p yyy2:tcp -h xxx3 -p yyy3" --registry="tcp -h xxx1 -p yyy1" --localip=[] --node-name=[]
                  安装framework相关组件
                    --install-path: 框架安装目录, 默认为: /usr/local/app
                    --file: 安装包文件
                    --locator: 主控三节点地址
                    --registry: 当前节点tarsregistry的QueryObj的绑定地址, 通常处于locator之一
                    --localip: 可选, 当前节点ip地址, 如果不指定则为registry中指定的ip地址
                    --node-name: 当前节点的名称, 如果不指定则为localip
   bin/tarsctl framework start --install-path=[/usr/local/app] --server-name=[]
                  启动当前节点上tars框架服务
   bin/tarsctl framework stop --install-path=[/usr/local/app] --server-name=[]
                  停止当前节点上tars框架服务
   bin/tarsctl framework restart --install-path=[/usr/local/app] --server-name=[]
                  重启当前节点上tars框架服务
   bin/tarsctl framework list --install-path=[/usr/local/app] --server-name=[]
                  列出当前目录上安装的tars服务的状态
   bin/tarsctl idc list
                  查看所有的分组信息
   bin/tarsctl idc create --update --name=[xxx] --name_cn=[xxx] --order=[denny_allow|allow_denny] --allow_ip_rule=[xxx.xxx.xxx.*,yyy.yyy.yyy.*] --denny_ip_rule=[xxx.xxx.xxx.*,yyy.yyy.yyy.*]
                  创建一个分组信息
                  --name: 分组名称, 请使用英文, 作为分组的关键字, 每个分组唯一标识, 例如: sz1
                  --name_cn: 分组名称, 中文提示, 例如: 深圳机房1
                  --update: 如果存在则修改分组信息
                  --order: 使用ip分组时的顺序, denny_allow: 先使用denny_ip_rule, 再使用allow_ip_rule判断, allow_denny: 先使用allow_ip_rule, 再使用denny_ip_rule判断
                  --allow_ip_rule: 进入分组的ip列表, 可以第四组为*, 比如: 192.168.3.*, 多个ip端用,分割
                  --denny_ip_rule: 不允许进入该分组的ip列表, 可以第四组为*, 比如: 192.168.5.*, 多个ip端用,分割
   bin/tarsctl idc update --name=[xxx] --name_cn=[xxx] --order=[denny_allow|allow_denny] --allow_ip_rule=[xxx.xxx.xxx.*,yyy.yyy.yyy.*] --denny_ip_rule=[xxx.xxx.xxx.*,yyy.yyy.yyy.*]
                  修改指定name的分组信息
                  idc delete --name=[xxx]
                  删除某个分组
                  idc station-list
                  查看站点列表, 多个idc分组可以合并设置为一个站点, 并且站点有优先级, 从主控获取ip list时, 如果同组的idc分组查不到ip list, 则会按照站点优先级匹配其他分组, 查询ip list
                  idc station --name=[xxx] --group=[xxx,xxx,xxx] --order=[order]
                  设置站点信息, 当有多个站点, 每个站点有多个分组, 如果服务只部署在其中某些分组中, 当客户端访问服务时, 主控会按照站点优先级顺序, 返回合适的ip list
                  --name: 站点名称, 例如: sz-idc
                  --group: idc分组列表, 例如: sz1,sz2,sz3
                  --order: 优先级顺序, 数字越小, 优先级越高
   bin/tarsctl image create --update --registry=[docker.io] --target=[tarscloud/tars.cppbase:latest] --remark=[text]
                  添加容器仓库中基础镜像, --registry: 仓库地址, --target: 基础镜像地址, --remark: 备注
   bin/tarsctl image update --id=[id] --target=[tarscloud/tars.cppbase:latest] --remark=[text]
                  修改容器仓库中基础镜像的信息, --registry: 仓库地址, --target: 更新的基础镜像地址, --remark: 备注
   bin/tarsctl image delete --registry=[docker.io]  --target=[tarscloud/tars.cppbase:latest]
                  删除容器仓库中的镜像
   bin/tarsctl image list --registry=[docker.io] 
                  列出所有仓库中所有的基础镜像
   bin/tarsctl tarsctl install web --install-path=[/usr/local/app] --file=web.tgz --locator="tcp -h xxx1 -p yyy1:tcp -h xxx2 -p yyy2:tcp -h xxx3 -p yyy3"
                  安装web组件
   bin/tarsctl load timediff --node-name=[xxx] --start=[%Y%m%d%H%M%S] --end=[%Y%m%d%H%M%S]
                  获取某个node和tarscollect leader节点的时间差异, 如果不指定start, end, 则获取最新的数据
   bin/tarsctl load timediffs
                  获取所有node最新的时间差异
   bin/tarsctl load device --node-name=[]
                  获取某个node, 硬盘设备分区名称
   bin/tarsctl load disk --node-name-[] --device=[] --start=[%Y%m%d%H%M%S] --end=[%Y%m%d%H%M%S]
                  获取某个node, 某个分区硬盘大小变化数据
   bin/tarsctl load cpu --node-name=[] --start=[%Y%m%d%H%M%S] --end=[%Y%m%d%H%M%S]
                  获取某个node, cpu负载变化数据 --start=[%Y%m%d%H%M%S] --end=[%Y%m%d%H%M%S]
   bin/tarsctl load mem --node-name=[] --start=[%Y%m%d%H%M%S] --end=[%Y%m%d%H%M%S]
                  获取某个node, 内存负载变化数据
   bin/tarsctl login help
                  login帮助
   bin/tarsctl login --uid=[uid] --password=[xxx]
                  使用用户uid登录
   bin/tarsctl logout help
                  logout帮助
   bin/tarsctl logout
                  退出当前用户登录
   bin/tarsctl node list --name=[xxx] --idc=[xxx] --curPage=[1] --pageSize=[20]
                  分页所有node, 当不指定pageSize或curPage, 则列出所有node, --name: 模糊搜索节点, 不指定则不检索, --idc: 模糊搜索指定idc的节点, 不指定则不检索
   bin/tarsctl node server --name=192.168.1.1
                  列出node上的所有服务
   bin/tarsctl node stub --name=192.168.1.1 --stub=[]
                  修改某台节点的stub名称
   bin/tarsctl node ping --name=192.168.1.1
                  ping node
   bin/tarsctl notify help
                  查看帮助
   bin/tarsctl notify query --target=[Test or Test.HelloServer or Test.HelloServer_192.168.0.1] --from=[202401021035] --limit=[100]
                  查询异常上报数据, target: 目标服务, from: 从这个数据往前的异常上报数据, limit: 查询条数
   bin/tarsctl patch list --target=[App.HelloServer]
                  查看服务的发布包
   bin/tarsctl patch download --id=[xxx]
                  下载服务的发布包
   bin/tarsctl patch download --target=[App.HelloServer]
                  下载服务缺省发布包
   bin/tarsctl patch delete --id=[xxx]
                  删除服务的发布包
   bin/tarsctl patch default --id=[xxx]
                  将当前id包设置为服务的缺省发布包
   bin/tarsctl prop help
                  查看帮助
   bin/tarsctl prop query --date=[20240102] --from=[0000] --to=[1035] --module-name=[] --node-name=[] --property-name=[] --policy=[Sum,Avg,Count,Max,Min] --group-by-time=[ON/OFF]
                  查询调用数据, date: 日期, 如果不填则默认今天, from: 起始分钟, 例如:1035(10点35分), to: 结束分钟, group-by-time: 是否按时间做group
   bin/tarsctl repo create --update --registry=[docker.io] --user=[xxx] --password=[xxx]
                  添加容器仓库地址, --update: 存在则更新
   bin/tarsctl repo update --id=[id] --registry=[docker.io] --user=[xxx] --password=[xxx]
                  修改指定id的容器仓库信息
   bin/tarsctl repo login --registry=[docker.io]
                  检查仓库是否可以登录
   bin/tarsctl repo delete --registry=[docker.io]
                  删除容器仓库地址
   bin/tarsctl repo list
                  列出所有容器仓库
   bin/tarsctl server list --target=[App.HelloServer] --detail
                  根据名字列出所有的服务, name : App or App.HelloServer, --detail: 输出所有详细信息
   bin/tarsctl server list --obj=[App.HelloServer.Obj] --detail
                  根据Obj列出所有的服务, --detail: 输出所有详细信息
   bin/tarsctl server list --abnormal=[App.HelloServer] --detail
                  根据名字列出所有异常服务, abnormal : App or App.HelloServer,--detail: 输出所有详细信息
   bin/tarsctl server bak
                  列出所有主备模式的服务
   bin/tarsctl server node --target=[App.HelloServer]
                  列出所有node, name: App or App.HelloServer
   bin/tarsctl server create --update --target=[App.HelloServer] --set=[set_name.set_area.set_group] --template-name=[tars.default] --profile=[] --server-type=[tars_cpp,tars_java,tars_nodejs,tars_go] --node-names=[xxx,xxx,xxx] --open-master-slave --config=file1.conf,file1path:file2.conf:file2path --obj=[Obj1,Obj2] --bind-Obj1=[endpoint,endpoint,endpoint] --bind-Obj2=[endpoint,endpoint,endpoint] --Obj1=[threads,maxconns,queuecap,queuetimeout,protocol(tars/not_tars),allow] --Obj2=[...]
                  创建服务: target: 指定服务名称
                           update: 如果存则更新
                           set: set分组信息, 缺省空
                           template-name: 使用的模板名称
                           profile: 私有模板文件
                           server-type: 服务类型
                           node-names: 部署在哪些节点上, 多个节点以,分割
                           open-master-slave: 启用主备模式, 如果不指定则是缺省模式
                           config: 服务的配置文件, 格式为: 文件名:路径,文件名:路径
                           obj: 服务的Obj名字(不需要App.HelloServer)
                           bind-Obj1: Obj1以实际obj中指定的名字为准(比如: HelloObj), endpoint和node-name中个数保持一致, Obj1在对应的节点上的绑定地址, 例如: tcp -h xxx -p yyy
                           Obj1: 指定对应Obj的参数, 如果不填写则使用缺省值[5,10240,10000,20000,tars,]
   bin/tarsctl server send --target=[App.HelloServer_192.168.1.1] --command=[text]
                  给服务发送命令, target: App.HelloServer or App.HelloServer_192.168.1.1
   bin/tarsctl server delete --target=[App.HelloServer_192.168.1.1]
                  删除服务, target: App.HelloServer or App.HelloServer_192.168.1.1
   bin/tarsctl server stop --target=[App.HelloServer_192.168.1.1]
                  停止服务, target: App.HelloServer or App.HelloServer_192.168.1.1
   bin/tarsctl server start --target=[App.HelloServer_192.168.1.1]
                  启动服务, target: App.HelloServer or App.HelloServer_192.168.1.1
   bin/tarsctl server restart --target=[App.HelloServer_192.168.1.1]
                  重启服务, target: App.HelloServer or App.HelloServer_192.168.1.1
   bin/tarsctl server patch --target=[App.HelloServer_192.168.1.1] --file=[tgz] --default-version --update=[text]
                  上传文件并发布服务
                           target: 指定的服务, App.HelloServer or App.HelloServer_192.168.1.1
                           file: 发布文件包
                           default-version: 指定为缺省版本
                           update: 发布描述
   bin/tarsctl server expand --target=[App.HelloServer] --src-node-name=[192.168.1.0] --dst-node-names=[192.168.1.1,192.168.1.2] --set=[xx.xx.xx] --copy-node-config --bind-Obj1=[endpoint,endpoint,endpoint] --bind-Obj2=[endpoint,endpoint,endpoint]
                  扩容服务到指定节点, 并发布重启
                           target: 扩容指定服务
                           src-node-name: 扩容源节点
                           dst-node-names: 扩容目的节点, node_name数组
                           set: 如果指定, 则启用set, 格式为: set_name.set_area.set_group
                           copy-node-config: 如果有节点配置, 则复制节点配置
                           bind-Obj1: Obj1以实际obj中指定的名字为准(比如: HelloObj), endpoint和node-name中个数保持一致, Obj1在对应的节点上的绑定地址, 例如: tcp -h xxx -p yyy
   bin/tarsctl server repatch --target=[App.HelloServer_192.168.1.1] --id=[patch_id]
                  再发布服务, 使用已经存在的patch记录发布服务
   bin/tarsctl server set --target=[App.HelloServer]
                  查看服务的所有set信息
   bin/tarsctl server obj --target=[App.HelloServer]
                  查看服务的obj信息
   bin/tarsctl server log-list --target=[App.HelloServer_192.168.1.1]
                  查看某个服务的日志列表
   bin/tarsctl server log --target=[App.HelloServer_192.168.1.1] --file=[App.HelloServer.log] --search=[xxx]
                  查看某个服务的日志, 根据search实时搜索最后几行, file如果为空, 则默认获取缺省的日志文件
   bin/tarsctl server tree --search=[tars] --type=1 --reload=1
                  web左侧导航菜单树状数据结构
                  search: 搜索关键字
                  type: 1: 应用服务 2: DCache应用服务
                  reload: 刷新缓存
   bin/tarsctl stat help
                  查看帮助
   bin/tarsctl stat query --date=[20240102] --from=[0000] --to=[1035] --master-name=[] --slave-name=[] --master-ip=[] --slave-ip=[] --interface-name=[] --group-by=[master-name,slave-name,master-ip,slave-ip,interface-name] --group-by-time=[ON/OFF]
                  查询调用数据, date: 日期, 如果不填则默认今天, from: 起始分钟, 例如:1035(10点35分), to: 结束分钟, group-by-time: 是否按时间做group
                  station list
                  查看站点列表, 多个station分组可以合并设置为一个站点, 并且站点有优先级, 从主控获取ip list时, 如果同组的station分组查不到ip list, 则会按照站点优先级匹配其他分组, 查询ip list
                  station create --update --station=[xxx] --name=[xxxx] --group=[xxx,xxx,xxx] --order=[order]
                  设置站点信息, 当有多个站点, 每个站点有多个分组, 如果服务只部署在其中某些分组中, 当客户端访问服务时, 主控会按照站点优先级顺序, 返回合适的ip list
                  --station: 站点名称, 关键字, 例如: sz-station
                  --name: 站点描述
                  --group: station分组列表, 例如: sz1,sz2,sz3
                  --order: 优先级顺序, 数字越小, 优先级越高
                  station update --station=[xxx] --group=[xxx,xxx,xxx] --order=[order]
                  修改站点信息
                  station delete --station=[xxx]
                  删除站点信息
   bin/tarsctl stub list
                  列出所有的stub节点
   bin/tarsctl stub update --node-name=[node] --inner-address="tcp -h xxx -p yyy" --outer-address="tcp -h xxx -p yyy"
                  更新stub的信息实例
   bin/tarsctl template list
                  列出所有模板
   bin/tarsctl template view --name=[tars.default] --with-parent
                  查看指定的模板, --with-parent: 是否合并父模板
   bin/tarsctl template create --name=[tars.xxx] --parent=[tars.default] --file=[filepath]
                  添加模板
   bin/tarsctl template update --name=[tars.default] --file=[filepath]
                  更新模板
   bin/tarsctl template delete --name=[tars.default]
                  删除一个模板
   bin/tarsctl help
                  查看帮助
   bin/tarsctl token create --expireTime=[xxsecond/minute/hour/day/month/year]
                  创建token, 例如: token create --expireTime=4hour
   bin/tarsctl token list --valid=[1/0]
                  获取uid下所有token, --valid如果没有指定, 则列出所有token
   bin/tarsctl token delete --token=[xxx]
                  删除token
   bin/tarsctl token get --token=[xxx]
                  根据token获取token info
   bin/tarsctl token valid --token=[xxx] --valid=[1/0]
                  设置token有效性
   bin/tarsctl user modify-pass --password=[xxx]
                  修改用户自己的密码

```