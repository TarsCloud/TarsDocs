2020/6/15 

https://my.oschina.net/u/4021301/blog/4311402

2020.6.22 
1、 在对TARS支持负载均衡进行测试时遇到问题。
https://git.io/JfNON
2、TarsFramework 忘记密码如何处理？
https://git.io/JfN3t
3、TARS部署在本地，如何规避IP是 DHCP来的会经常变化的问题？
https://git.io/JfN3g
4、Monitor的initialize，用shm初始化，计算queueSize的原因是什么？
https://git.io/JfNOt
5、MDC支持跨服务链路追踪吗？
https://git.io/JfN31

20200623

1、基于官网提供的docker生产环境例子，使用host网络，部署后能成功访问3000端口吗？
https://git.io/JfNOv
2、P90，P99和P999的汇总思路是什么？
https://git.io/JfNOI
3、微服务通过linux控制台直接关闭和启动，那么框架里面的web管理后台的控制会失效吗？
https://git.io/JfAcX
4、为什么TARS的生产代码中存在大量的assert？
https://git.io/JfACT
5、现在最新版本的server端还不支持异步处理请求吗？
https://git.io/JfACW

20200624
1、 为什么使用集群MySQL会报错？
https://git.io/JfNYs
2、 是否可以在一台服务器上部署同一个端口不同的Object？
https://git.io/JfNYG
3、 TARS使用k8s部署遇到的一个问题。
https://git.io/JfxaC
4、 远程日志支持分散打印到远程多个服务中吗？
https://git.io/Jfxas
5、 TARS框架部署时，每个组件需要的硬件要求是怎么样的？
https://git.io/JfxaO

20200625

1、 为什么在TARS注册中心中添加一个Servant，数据库可以查到数据但注册中心没有？
https://git.io/JfbJU
2、 为什么centos可以联通 windows的mysql，容器里面启动却连不上？
https://git.io/JfNYB
3、为什么win下执行脚本不会生产C:\tars\cpp 这个目录？
https://git.io/JfNYE
4、HelloGo发布后运行不起来是什么原因？
https://git.io/JfNYu
5、AdminF.tars: 17: error: identifier can't start with 'tars'怎么解决？
https://git.io/JfNYK

20200626

1、 java里的stringToProxy，会触发死循环bug。
https://git.io/JfNYH
2、 tc_mysql的库是线程安全的吗？
https://git.io/JfNYp
3、 tars-install里的replace是脚本中的replace函数还是mysql-tool替换文本？
https://git.io/Jfbfx
4、 如果服务器上有两个网卡，能否把服务的ip改成跟tarsnode不同的ip？ 
https://git.io/JfNYZ
5、 mac自动化部署是实际安装过程中好像没法自动安装node npm。
https://git.io/JfNYR

20200627

1、TarsFramework的master分支编译完后，升级tars-node只需要替换tars-node的执行文件吗? 
https://git.io/JfNYa
2、在HelloServer/bin目录下make release后，/home/tarsproto/TestApp/HelloServer目录下无tars和mk文件。
https://git.io/JfNY6
3、上线服务并打包上传程序包后报错，日志提示端口被占用，更换空闲端口后仍然报错。
https://git.io/JfNYx
4、为什么我的日志的记录时间总是比框架和业务节点的时间少8h？
https://git.io/JfjQn
5、在已有的http服务中，拷贝TARS文件做了一个请求其他tcp服务的客户端，请求时出现错误。
https://git.io/JfjHu

20200628


1、 ImComm.IggBrokerSvr.memsize的单位是什么？
https://git.io/JfNYC
2、 "flex --nounist / home/cjs/ tars/ TarsFramework/ tarscpp/ tools/ tarsgrammar/tars. 1/bin/sh: 1: flex: not found"这是缺了那个库？
https://git.io/JfNY0
3、 想用单项调用提高效率，是用tcp还是udp？
https://git.io/JfNY9
4、在下线清理服务后想要马上删除节点，但无法删除节点怎么办？
https://git.io/Jfj7d
5、跑TARS自带example下的 CoroutineDemo 用例 testCoro， 抛出异常怎么处理？
https://git.io/Jfj7B

20200629

1、 上传程序包到服务上提示错误
[ServantProxy::invoke timeout:10000,servant:tars.tarsnode.NodeObj,func:patchPro,a
dapter:tcp -h 10.0.1.8 -p 19385 -t 60000,reqid:26]是什么原因？
https://git.io/JfNYb
2、 TARS运维管理中节点管理的时间戳是用的哪个系统的时间？
https://git.io/JfNOm
3、 有方便的更换web port的方式么？
https://git.io/JfjQ7
4、 安装的时候报错(Completed 'ADD mysqlclient'；Built target ADD_mysqlclient；make :*** [all]错误 2)该怎么处理？
https://git.io/Jfj7F
5、 client.pom.xml server.pom.xml是做什么用的？
https://git.io/JJv3n

20200701

1、 vertx改造，已经基本能运行了，但为什么出现了日志乱码？
https://git.io/JJvjj
2、 为什么我在使用TARS时，logback.xml文件获取不到？
https://git.io/JJfem
3、 web过来的请求直接到node的话，都有哪些功能操作？
https://git.io/JJfeZ
4、 日志打到TarsLog里后，在web页面上可以流式查看检索吗？
https://git.io/JJfec
5、 跑java微服需要自己进docker环境中配置java吗？
https://git.io/JJfeB


20200702

1、 如何在TarsCpp中创建自己定义tcp连接外部服务器(非TAF)？
https://git.io/JJJJc
2、 使用docker创建虚拟网络，TarsFrame-work中使用宿主的ip，但发布时会报错 。
https://git.io/JJJJ8
3、 TARS框架现在访问是http，可以改成https吗？
https://git.io/JJJJH
4、 1.7.1的tars-Java支持染色日志吗？
https://git.io/JJJJx
5、 处理远程日志需要把这个打成jar包放到我的工程，还是maven依赖下就可以了？
https://git.io/JJJUJ

20200703

1、 tars有分布式事务解决方案吗？
https://git.io/JJJpR
2、 tarsnode和framework docker都安装在一台宿主机上，会有什么冲突吗？
https://git.io/JJJp6
3、 本地开发服务端总报错但是客户端可正常调用是什么原因？
https://git.io/JJJpH
4、 （图）cache在哪里会更新？
https://git.io/JJJhk
5、 代码管理与代码编译是怎样在tars中使用的？
https://git.io/JJJhO

20200704

1、 C++版本支持调用链了吗？
https://git.io/JJUdW
2、 业务初始化接口里是不是不能进行耗时操作？
https://git.io/JJUdR
3、 ServerObject::isAutoStart tars.auth not allow to restart in limited state
请问一下这个是什么情况呢？限制状态怎么解决呢？
https://git.io/JJUd2
4、 github.com/TarsCloud/TarsGo/tars/tools/create_tars_server.sh: line 86: rename: command not found这个会有什么影响？
https://git.io/JJUdo
5、 想增加一个PHP扩展，在TarsNode的docker里找不到PHP的源码，应该怎么操作呢？
https://git.io/JJUd6

20200705

1、 为什么win7系统下编译TarsFramework无法通过？（144）
https://git.io/JJkaK
2、 make_pair(TC_Mysql::DB_INT, 100);  100会变成d，这个如何编译？（147）
https://git.io/JJka6
3、 framework 和 tars-node应该用什么方式部署到生产环境？（149）
https://git.io/JJka7
4、 tarsgo在master中注册的服务报错，直接寻址没有问题，这是缺了什么 config 文件吗？（155）
https://git.io/JJkaj
5、 tars部署Java，怎样指定内存大小？（157）
https://git.io/JJkVe

20200706

1、 golang中，TARS基本类型怎样映射 uint64？（158）
https://git.io/JJIOo
2、 erp系统，在删除基础部门信息时，要检查是否被其他表引用，有什么比较好的解决方案？（162）
https://git.io/JJIO6
3、 java 已经安装配置了，服务提示启动成功， 但是状态还是off，服务的端口未被占用，是因为什么呢？（163）
https://git.io/JJIOM
4、 脱离TARS环境调用微服务，setLocator怎么传参？（164）
https://git.io/JJIOH
5、 阿里云VPS，有内网IP，自动安装节点成功，节点管理里找不到怎么办？（166）
https://git.io/JJIOb

20200707

1、 从web控制端 配置了配置文件，go代码里我该怎么获取？
https://git.io/JJLc7
2、 （图）在同一个进程内为什么会有那么多127.0.0.1的连接？
https://git.io/JJLcj
3、 A服务器上部署了Tars环境，如果将一些服务部署在B服务器上，是否需要在B服务器上也部署一套Tars环境？
https://git.io/JJLCU
4、 新增部署节点时，可以填写我的节点地址吗？我想添加一个有公网ip的服务器地址，要如何操作？
https://git.io/JJLCC
5、 将配置文件push到服务节点之后。解析的时候，提示找不到文件是什么原因？
https://git.io/JJLCr


20200708

1、 TARS服务管理的重启按钮，会调用哪个shell脚本来执行？
https://git.io/JfNYc
2、 (图)这个tars每天都会重置么？
https://git.io/JfNYz
3、 (图)遇到这个配置问题如何解决？
https://git.io/JfNYV
4、 按照文档写HelloServer,未发现config.conf配置文件，这个服务的配置文件是手动生成的还是有工具自动生成的？
https://git.io/JfNYw
5、 (图)main.cpp是客户端代码，引用了头文件servant/Communicator.h，而Communicator.h又引用了头文件util/tc_ex.h，那么每次编译需要自己将servant目录和util目录复制到工程目录下面吗？
https://git.io/JfNYo

20200709

1、 （图）node的地址和端口号如何填写？
https://git.io/JfNY1
2、 （图）version=2或者version=3的时候，也是服务端做上报吗？
https://git.io/JfNYQ
3、 提示tars not find adapter 是什么原因？
https://git.io/JJqFh
4、 （图）这个log 目录为什么会生成那么多core文件？
https://git.io/JfjH5
5、（图）这里的urldecode，是符合标准的吗？(encode不对称)
https://git.io/JfjQb


20200710

1、 TARS用gson替代fastjson而不是用jackson替代主要是出于哪些考虑呢？
https://git.io/JJmn2
2、 TARS有链路追踪日志没？
https://git.io/JJmnK
3、 TarsContext 是不是1.7.1版本才有的？1.6.1暂时还没有？
https://git.io/JJmnX
4、 在往TARS上部署应用的时候，通过jenkins进行一键部署，我看有上传并发布的接口，但是不会用。
https://git.io/JJmn1
5、 （图）调用时候报错：requird field not exist ，这一般是什么原因造成的？
https://git.io/JJmn9

20200711

1、 1.7版本后，日志直接用@Slf4j注解，看不到日志了，需要在哪儿配置吗？
https://git.io/JJmAX
2、 如何看待RPC框架中的单连接和多连接(或连接池)设计?
https://git.io/JJmA1
3、 proto3协议，java客户端调c服务端，返回结果null，无报错，怀疑解析有问题，是否有开关能在解析异常时打印信息？
https://git.io/JJmAN
4、 在外网安装好了TARS，要搬到内网去，改变了IP，有什么办法让TARS能工作起来？
https://git.io/JJmAx
5、 服务是活的的，obj的端口却没有监听该怎么办？
https://git.io/JJmxk

20200712

1、 (图)这代码配置读取是不是写漏了个斜杠？
https://git.io/JJmx8
2、 非TARS框架创建两个Communicateor会有什么问题吗？
https://git.io/JJmx0
3、 (图)这种错误一般是什么原因呢？
https://git.io/JJmxg
4、 滚动日志超过配置文件中的个数后会被自动删除吗？
https://git.io/JJmxw
5、 在部署TARS时 Mysql IP Address 和 INET 怎么找？
https://git.io/JJYFZ

20200713

1、利用docker.sh制作镜像，出现该问题怎么解决？ 
https://git.io/JJYbu 
2、打包发布后想要修改代码，是在本地修改然后再压缩上
传吗？ 
https://git.io/JJYbh 
3、在make docker 的时候，输入.\ docker.sh v1 ，提示
commond not found是什么原因？ 
https://git.io/JJYNT 
4、各个服务之间怎么调用的？用的方法是什么？ 
https://git.io/JJYNt 
5、正式服更新TARS框架如何更新？ 
https://git.io/JJOfy 


20200714

1、 只clone tarscpp在mac上能通过编译吗？
https://git.io/JJOjN
2、 想开发一个TARS 的UDP服务，该怎么写？
https://git.io/JJOjh
3、 c++后台能用vs编译吗？
https://git.io/JJ3ef
4、 tars app_log目录占分区空间较大，怎么安全迁移到/data分区呢？
https://git.io/JJ3eO
5、 （图）这是少配置了什么导致错误？
https://git.io/JJ3eq

20200715

1、 发现服务每隔10分钟会重启，这是什么原因？
https://git.io/JJ378
2、 调用服务的频率快一些服务就会重启，有什么解决办法？
https://git.io/JJ374
3、 TARS的每个组件都哪些对数据库有依赖呀，每个组件有自己的独立数据库表吗？
https://git.io/JJ3pN
4、 （图）TarsCpp库已经正常编译安装，并生成TARS协议了，但我运行后没生成cmakelist.txt。
https://git.io/JJ3he
5、 报错 ’STATUS_GRID_KEY‘ is not a member of 'tars::Servantproxy'的原因是什么？
https://git.io/JJ3hT

20200716

1、 提示 “bash: cd: C:\Users\Admin\go/src/github.com/TarsCloud/TarsGo/tars/tools/tars2go: No such file or directory” 是什么原因呢？
https://git.io/JJsNj
2、 （图）主控和节点目前在一台机器，部署的服务启动不了，我看了下启动脚本，执行路径里多一个空格有可能是什么原因？
https://git.io/JJsAV
3、 （图）标注处所示windows挂载不上去，因为没有这个目录，这是什么原因呢？
https://git.io/JJsA6
4、 （图）图中的错误是跟什么插件版本有关？
https://git.io/JJsA1
5、 （图）第一次登录，在修改密码时提示“系统内部错误”，检查日志提示表只读，可能是什么原因呢？
https://git.io/JJsAM

20200717

1、TRACE swPort_onRead_http (ERRNO 8002): Bad Request: unknown protocol from session#4 on 172 .25.0.4:21777  Communicator monitor route failed servantName :tars . tarsstat . Statobj
这是什么问题？
https://git.io/JJGdF
2、 如何编译protoc-gen-go插件？
https://git.io/JJGFe
3、 (图)这里的客服端报错是什么原因呢？
https://git.io/JJGFO
4、 平台上，服务管理-》服务监控，有的基础服务没有数据，业务服务全都没有数据，这个情况正常吗？
https://git.io/JJGFB
5、 看上去TARS运行起来了，但通过浏览器访问公网的 tars web无法连接，有什么排查的方法？.
https://git.io/JJGFg


20200718

1、 （图）使用1核2GB的腾讯云在编译tars源码的时候报错，请问如何解决？
https://git.io/JJZQG
2、（图）时间不准，怎样同步成为最新的时间？mysql是通过docker安装的。
https://git.io/JJZQc
3、（图）一个关于servant的小问题。直连node节点的时候，服务名字不重要吗？
https://git.io/JJZQ0
4、（图）文件解析有问题，是目前pom.xml的依赖不全吗？我还需要导入别的依赖吗？
https://git.io/JJZQM
5、在服务部署时，线程数、最大连接数、队列最大长度等修改后会立即生效吗？
https://git.io/JJZQH


20200719

1、 （图）这两个TARS服务间调用时报错是什么原因？
https://git.io/JJnEc
2、 （图）在调用服务接口的时候，客户端日志提示链接被关闭，但是服务端是收到了这个请求的，请问是什么原因呢？
https://git.io/JJnEZ
3、 （图）请问这个是添加在模板配置文件里吗？
https://git.io/JJnE4
4、 请问java中定义xxx.tars文件时，有时间类型吗？
https://git.io/JJnER
5、 （图）服务监控不能用，报错提示是查询602，我使用windows的docker里没有对应的localtime文件，是因为没有导入时区吗？
https://git.io/JJnEz


20200720

1、 （图）这是framwork的3001吗？什么是管理端口？
https://git.io/JJcwe
2、 例如3000这个端口已经改成我自定义的8000端口，但是访问web控制台的时候，第一步index.html中会自动跳转到:3001的端口， 这个3001端口又在哪里改呢？
https://git.io/JfjQQ
3、 （图）这个镜像在哪里可以看到对应的TarsFramework代码的版本号？
https://git.io/JJcwk
4、 （图）请问图中-cp后面的目录是怎么加进来的？程序中没有这部分地址要怎么处理呢？
https://git.io/JJcwO
5、 （图）请问HelloPrx和HelloPrxCallback这两个类是自动生成的吗？
https://git.io/JJcwl

20200721

1、 测试环境如何保证日志的级别是debug级别的呢？
https://git.io/JJC8i
2、 （图）提示这个错误，是什么原因导致的？
https://git.io/JJC81
3、 TARS的服务，要使用自己的自定义的配置文档怎么操作？
https://git.io/JJC8y
4、 （图）我添加新节点之后为什么节点列表里没有显示新节点呢？
https://git.io/JJC87
5、 使用go语言，TARS如何实现https？
https://git.io/JJC8b


20200722

1、 TarsGateway支持HTTPS吗？
https://git.io/JJC8N
2、 ResponsePacket 和 RequestPacket 里都有context 和 status ，这两个值有什么区别？
https://git.io/JJC8x
3、 (图)tars+Yii远程下载，不在exit的话就这种情况。
https://git.io/JJC4Y
4、 如果都是TARS协议，可以在A中调用A::async_response_函数，但在http协议中如何做呢？
https://git.io/JJC4W
5、 (图)虚拟机发生了断电，机器重启后TARS的应用服务都启动不了了，运行 /usr/local/app/tars/check.sh，显示这样的内容，这是什么情况？
https://git.io/JJC4E


20200723

1、 TARS灰度发布，现在支持吗?
https://git.io/JJC4V
2、 TarsJava: >= v1.7.2　网关要求1.7.2版本？
https://git.io/JJC4K
3、 关于tars http server的三个疑问。
https://git.io/JJC4P
4、 TARS有没有对连接做保活？
https://git.io/JJC4b
5、 为什么TarsGateway会依赖到tars_user_system？
https://git.io/JJC4h


20200724

1、 gateway在内部是什么场景？
https://git.io/JJCBJ
2、 一个疑问：我有个服务A，线上环境，我有多个地区部署，配置不一样，这块是怎么搞呢？
https://git.io/JJCBI
3、 (图)安装TarsGateway时这个token获取不到怎么办？
https://git.io/JJCBO
4、 (图)手动启动TarsGateway，报了这个错，是怎么回事？
https://git.io/JJCB3
5、  业务服务平台上面启动不了，发现start和stop脚本，执行不太一样，start直接php命令 stop是执行路径，这样做是有什么用意吗？
https://git.io/JJlWT


20200725

1、 传输的二进制数据里面, tag是严格有序的吗？
https://git.io/JJ40o
2、 同一个TARS集群下，TarLog、TasPatch是不是和tarsAdminRegisrty一样，只能有一个实例？
https://git.io/JJ406
3、 远程登录，不方便用web停止。Tars cpp2.x在节点侧，手工停止服务的脚本命令是什么？
https://git.io/JJ40M
4、 服务两个节点，我想做负载均衡怎么处理？
https://git.io/JJ40D
5、节点正常启动，和framework的docker在同一台机器上，但是后台看不到新加入的节点？这可能是什么原因呢？
https://git.io/JJ409


20200726

1、 (图)框架部署的时候已经启动了TarNode，还需要TarNode部署这一步单独安装TarNode吗？
https://git.io/JJ40N
2、 TLOG/DLOG可以定制日志格式吗？
https://git.io/JJ40h
3、 (图)这个web的端口必须指定3000和 3001吗？
https://git.io/JJ4EG
4、 (图)这个属性上报的入口在哪里？
https://git.io/JJ4EZ
5、 调用TarsGateway，用的json协议，报了版本错误，该怎么处理？
https://git.io/JJ4EB


20200727

1、 (图)gateway文档这部分，要传输的内容格式是什么？
https://git.io/JJ4Ea
2、 请问如果使用tars部署的http网站，怎么配置域名指向，是还需要nginx进行转发吗？
https://git.io/JJR9B
3、（图）虚拟机docker安装的,分配了4G内存，报错的原因是什么？
https://git.io/JfNOk
4、（图）报错的原因是什么？为什么显示没有数据？
https://git.io/JfNOU
5、请问TARS调用参数可以传递list对象吗？
https://git.io/JJR9R


20200728

1、 请问部署集群的时候，是否可以通过 keepalived + 多台 Tars网关服务器呢？
https://git.io/JJ0xB
2、 如果节点分布在各个实体机器上，一个节点上部署了多个服务，可能有java、php、go，一旦其中一个服务出现异常，有可能其它服务会受牵连，有什么办法可以避免这种情况吗？
https://git.io/JJ0xR
3、 jump_fcontext make_fcontext 的实现代码在哪儿？这两个接口说明在哪儿？
https://git.io/JJ0xE
4、 代码里用的相对路径，原来项目里都是自己脚本启动的，是相对路径，拿过来后，项目中代码找不到服务的根目录了，所以报错，这是怎么回事？(详情见issue)
https://git.io/JJ0pe
5、 我要发布个服务，一个 Demo 的 Hello World 服务，服务端准备用 Go 写。但有三个问题不懂。(详情见issue)
https://git.io/JJ0pL


20200729

1、 （图）这里的逻辑是正常的吗? 查日志没看到loadData()里面的逻辑定期执行？
https://git.io/JfNYd
2、 （图）运行一段时间后，两个服务自动off，socket连接不上，提示“Communicator monitor route failed servantName:tars.tarsproperty.PropertyObj”      “ Communicator monitor route failed servantName:tars.tarsstat.StatObj”，可能是什么原因呢？
https://git.io/JfjHj
3、 （图）windows上源码安装的时候出现这个问题可能是什么原因导致的？
https://git.io/Jfj7V
4、 2.4.7版TARS自带的DCache，按照文档装完了3个基础服务，然后在执行到创建应用时，请问IDC地区应该怎么处理？
https://git.io/JJu0Q
5、 （图）出现这样的报错，如何进行问题的排查？
https://git.io/JfNYF


20200731

1、 (图)发布DCacheOptServer失败。
https://git.io/JJzt9
2、 (图)重启后，pm2 list 下面没有 web 和 system，这是什么原因？
https://git.io/JJzqB
3、 （图）这里生产环境部署，服务节点和应用节点可以放在一台服务器上吗？
https://git.io/JJz0w
4、 GatewayServer 要使用某个服务，必须先配置<proxy>吗？
https://git.io/JJz0S
5、 （图）在服务器上通过docker安装框架成功之后，3001端口可以访问并重置了密码，安装网关生成token时发现3001无法访问了，3000可以正常访问，请问下有可能是什么原因导致的？
https://git.io/JJz0H

20200801
️

1、 （图）一般是什么程序去更新present_state这个字段呢？
https://git.io/JJa3H
2、 （图）IsActivatingLimited（）的机制是什么？
https://git.io/JJa3d
3、 （图）请问我在tars-java模板里面配置了tracing的配置，并且启动了一个zipkin的server，发现数据并没有上报到zipkin。可能会是什么原因呢? 
https://git.io/JJa3x
4、 （图）在Application.cpp中 ，为什么要设置一个AdminObj？
https://git.io/JJasf
5、 TARS框架最新版本可以离线安装吗？
https://git.io/JJask



20200802

️

1、 web本身的日志在哪里看？
https://git.io/JJasm
2、 请问TARS微服调用超时时间怎么设置？
https://git.io/JJV1Q
3、 （图）http解析里使用了许多cstring null terminator 的函数，解析时是否要在buffer后面补上'\0'?
https://git.io/JfNYy
4、 （图）TARS2.3服务启动的超时时间是写死两秒的吗？
https://git.io/JfNY7
5、 （图）这里的负载是什么意思呢？为什么有的显示0，有的显示1， 安装的时候没有做出相关的选择。
https://git.io/JJV17



20200803
️

1、 一个节点只能发布一个服务吗？我的服务是tar.gz，用TARS发布完后我的安装包会解压到哪里？
https://git.io/Jfj7x
2、（图）HelloGo 入门，这里的执行文件在前面代码里没有指定 ip和端口，有什么用？
https://git.io/JJw2F
3、如备注图所示的两个锁会被锁住吗？
https://git.io/JfNYg
4、（图）防火墙已关闭，配置文件也配置好了，出现如图所示的问题如何解决？
https://git.io/JfjHO
5、通过docker方式本地安装了 framework 和 node 各一台，现在通过web控制台启动一个spring boot 项目，无法顺利启动。但是我登入容器后，启动这个jar，可以正常工作，这个问题该怎么处理？
https://git.io/JfjH7



20200804

️

1、 （图）TARS管理后台超时提示没有操作权限，是因为cookie中uid值变了，不知道为什么从admin 变成一个很长的字符串了，打开管理后台后 ，挂在那里过20多分钟uid值就变化了。
https://git.io/JfjQF
2、 （图）发生这个错误是怎么回事？TarsWeb是新版的，TarsFramwork是旧版本的，不是docker部署。下载源码之后，执行sql文件夹的sql语句，修改config里面的配置，主要改数据库地址跟registry地址，把enable全部设置成false，最后执行npm run prd。
https://git.io/JJCBW
3、 （图）无法连接mysql问题怎么解决？mysql都重装了，命令行可以连上。
https://git.io/JJ3ek
4、 如何看系统里面现在使用的TARS框架版本呢？
https://git.io/JJoJA
5、 （图）之前部署的业务可以跑起来，今天想在另一台重新部署出现这个问题。登录后出现这样的提示是什么原因？刷新或者退出后重新登进也不行，依然提示未登录。
https://git.io/JJoUk



20200805
️

1、 TarsGo如何实现HTTPS？
https://git.io/JJKOF
2、 tars2php是不是对枚举类型不支持，和cpp交互是怎么处理枚举类型呢？ 
https://git.io/JJK3e
3、 Tars cpp2.x在节点侧，手工停止服务的脚本命令是？ 
https://git.io/JJK3T
4、 TarsGateway使用安装脚本install.sh安装过程显示成功，为什么最后的测试结果显示失败？ 
https://git.io/JJK3Y
5、 如何看系统里面现在使用的TARS框架版本呢？ 
https://git.io/JJK33



20200806

️

1、  首次搭建TARS，搭建环境，cmake时报错怎么处理？
https://git.io/JJ6FG
2、 重启后 pm2 list 下面没有 web 和 system 是什么原因？
https://git.io/JJ6F3
3、 运行linux_install.sh安装TARS框架出错，日志输出mysql访问失败？是否是bug？
https://git.io/JJ6FR
4、  问：为什么服务打包发布后后无法运行？报错“down，server is inactive”.
https://git.io/JfNOf
5、  TARS_CLIENT_LOGGER 可以在哪里配置关闭吗？
https://git.io/JJ6bl



20200807
️

1、安装发布提示超时，应该如何处理？docker版本为Version:2.1.0。
https://git.io/JJPTp
2、restart server, stop error:EM_TARS_UNKNOWN_ERR  出现这种错误怎么排查问题？
https://git.io/JJPkv
3、chmod a+x linux-install.sh  ./linux-install.sh MYSQL_HOST MYSQL_PASSWORD INET REBUILD(false[default]/true) SLAVE(false[default]/true) MYSQL_USER MYSQL_PORT，mysql 必须设置密码吗？
https://git.io/JJPkk
4、提示下载的Tarsweb没有demo那个目录是怎么回事？
https://git.io/JJPkt
5、（图）在TARS管理管理后台修改替换模板，是不是之前添加的微服要手动去修改模板文件才会生效？
https://git.io/JJPk8



20200808
️

1、 (图)swool扩展已经安装过了，模板里的php执行路径也改过了，出现如图所示的提示是怎么回事？
https://git.io/JJXg9
2、 (图)这个问题有可能是什么导致的？
https://git.io/JJX2f
3、 (图&日志)这里出现报错是什么原因？
https://git.io/JJX2U
4、 (图)这里这个报错是配置的问题吗？
https://git.io/JJX2T
5、 imp.go里面的接口实现，是以协程的方式被调用的吗？
https://git.io/JJX2k



20200809

️

1、 pp监控功能上报的数据需要自己去写ui展示吗？
https://git.io/JJX2s
2、 (图)为什么使用集群MySQL会报错？
https://git.io/JfNYs
3、 可以在一台服务器上部署同一个端口不同的Object吗？
https://git.io/JfNYG
4、 为什么在Tars注册中心中添加一个Servant，在数据库可以查到数据，但是注册中心没有？
https://git.io/JfbJU
5、 如果服务器上有两个网卡，能否把服务的ip改成跟tarsnode不同的ip？
https://git.io/JfNYZ



20200810
️

1、 低版本1.5.0 客户端里框架会有一个tars_client.log 日志，这个日志文件是一定打出来么？
https://git.io/JJCBv
2、 外网机器迁移了，目标obj的ip和端口修改了，这时候tars stringToProxy 会即刻拿到新的地址吗？
https://git.io/JJas3
3、 tars在k8s已创建好，怎么做才能让外网访问里面的服务？
https://git.io/JJasn
4、 tars-install里的replace是脚本中的replace函数还是mysql-tool替换文本？
https://git.io/Jfbfx
5、 docker容器的宿主机vmware的centos可以联通 windows的mysql，容器里面启动却连不上mysql，怎么解决？ 
https://git.io/JfNYB



20200811
️

1、为什么win下执行脚本不会生产C:\tars\cpp 这个目录？
https://git.io/JfNYE
2、（图）如图所示的两个锁会被锁住吗？
https://git.io/JfNYg
3、（图）遇到这个配置问题如何解决？备注图为TarsNode的启动日志
https://git.io/JfNYV
4、AdminF.tars: 17: error: identifier can't start with 'tars'怎么解决？
https://git.io/JfNYK
5、（图）java里的stringToProxy，会触发ConCurrentHashMap computeIfAbsent的死循环bug。
https://git.io/JfNYH



20200812
️

1、上线服务并打包上传程序包后报错，日志提示端口被占用，更换空闲端口后仍然报错。
https://git.io/JfNYx
2、tc_mysql的库是线程安全的吗？
https://git.io/JfNYp
3、P90，P99和P999的汇总思路是什么？
https://git.io/JfNOI
4、在已有的http服务中，拷贝TARS文件做了一个请求其他tcp服务的客户端，请求时出现错误。
https://git.io/JfjHu
5、微服务通过linux控制台直接关闭和启动，那么框架里面的web管理后台的控制会失效吗？
https://git.io/JfAcX



20200813

️

1、 Tarsframework的master分支编译完后，如果我需要升级tarsnode，只替换tarsnode的执行文件还是所有的tarsnode都替换一遍? 
https://git.io/JfNYa
2、 (图)按照文档写HelloServer，未发现这个配置文件，这个服务的配置文件是手动生成的还是有工具自动生成的？
https://git.io/JfNYw
3、 (图)main.cpp是客户端代码，引用了头文件servant/Communicator.h，而Communicator.h又引用了头文件util/tc_ex.h，那么每次编译需要自己将servant目录和util目录复制到工程目录下面吗？
https://git.io/JfNYo
4、 (图)在HelloServer/bin目录下执行 make release，最后在/home/tarsproto/TestApp/HelloServer目录下只看到hello.h，没有tars和mk文件是什么问题？
https://git.io/JfNY6
5、 (图)把IP地址和端口号改为自己的地址和端口号，直接编译运行这段代码就可以吗？
https://git.io/JfNY1



20200814
️

1、 (图)这里的问题出现的原因是什么？tarsAdminRegistry有启动。
https://git.io/JfNYM
2、 http解析里使用了许多cstring null terminator 的函数，解析时是否要在buffer后面补上'\0'?
https://git.io/JfNYy
3、 想用单项调用提高效率，是用tcp还是udp？
https://git.io/JfNY9
4、 (图)version=2或者version=3的时候，也是服务端做上报吗？
https://git.io/JfNYQ
5、 tars2.3服务启动的超时时间是写死两秒的吗？
https://git.io/JfNY7



20200815
️

1、 (图)这里逻辑是正常的吗? 感觉_interval不能生效, 查日志没看到loadData()里面的逻辑定期执行。
https://git.io/JfNYd
2、 (图)这里在上传程序包到服务上时，提示这个错误的原因是什么？
https://git.io/JfNYb
3、 Monitor的initialize，用shm初始化，计算queueSize的原因是什么？
https://git.io/JfNOt
4、 (图)防火墙已关闭，配置文件也配置好了，出现这里所示的问题如何解决？
https://git.io/JfjHO
5、 (图)通过docker方式本地安装了 framework 和 node 各一台，现在通过web控制台启动一个spring boot 项目，无法顺利启动。
https://git.io/JfjH7



20200816
️

1、为什么我的日志的记录时间总是比框架和业务节点的时间少8h？
https://git.io/JfjQn
2、有方便的更换web port的方式么？
https://git.io/JfjQ7
3、（图）TARS管理后台超时提示没有操作权限，是因为cookie中uid值变了，不知道为什么从admin 变成一个很长的字符串了，打开管理后台后 ，挂在那里过20多分钟uid值就变化了。
https://git.io/JfjQF
4、跑TARS自带example下的 CoroutineDemo 用例 testCoro， 抛出异常怎么处理？
https://git.io/Jfj7B
5、为什么TARS的生产代码中存在大量的assert？
https://git.io/JfACT



20200817
️

1、 图中在windows上源码安装的时候出现这个问题是怎么回事？
https://git.io/Jfj7V
2、 TarsFramework 密码忘了该如何处理？
https://git.io/JfN3t
3、 在下线清理服务后马上删除节点， 提示要求先暂停节点再删除，在登录服务器stop掉节点后，依然无法删除节点。
https://git.io/Jfj7d
4、 一个节点只能发布一个服务吗？我的服务是tar.gz，用tars发布完后我的安装包会解压到哪里？
https://git.io/Jfj7x
5、 (图)现在最新版本的server端还不支持异步处理请求吗？图中这块代码，这里的writeResult方法没有触发调用。
https://git.io/JfACW



20200818
️

1、 client.pom.xml server.pom.xml是做什么用的？
https://git.io/JJv3n
2、 (图)这个镜像在哪里可以看到对应的TarsFramework代码的版本号？还有这个stable呢？
https://git.io/JJcwk
3、 (图)这里-cp后面的目录是怎么加进来的？程序中没有这部分地址要怎么处理呢？
https://git.io/JJcwO
4、 vertx改造已经基本能跑了，但是为什么出现了日志乱码？
https://git.io/JJvjj
5、 为什么在使用TARS时，logback.xml文件获取不到？
https://git.io/JJfem


20200819
️

1、 web过来的请求直接到node的话，都有哪些功能操作？
https://git.io/JJfeZ
2、 日志打到tarslog里后，在web页面上可以流式查看检索吗？
https://git.io/JJfec
3、 (图)这两个类是自动生成的？需要执行怎样的命令？或者文档在哪里？
https://git.io/JJcwl
4、 (图)这里的负载是什么意思呢？为什么有的显示0，有的显示1， 安装的时候没有做出相关的选择。
https://git.io/JJV1Q
5、 docker镜像没有java环境吗？跑java微服需要自己进docker环境中配置java吗？
https://git.io/JJfeB



20200820
️

1、使用docker创建虚拟网络，TarsFrame-work中使用宿主的ip，但发布时会报错 。
https://git.io/JJJJ8
2、TARS框架现在访问是http，可以改成https吗？
https://git.io/JJJJH
3、1.7.1的tars-Java支持染色日志吗？
https://git.io/JJJJx
4、（图）这两个TARS服务间调用时报错是什么原因？
https://git.io/JJnEc
5、（图）TarsCpp库已经正常编译安装，并生成TARS协议了，但我运行后没生成cmakelist.txt。
https://git.io/JJ3he



20200821
️

1、（图）在调用服务接口的时候，客户端日志提示链接被关闭，但是服务端是收到了这个请求的，请问是什么原因呢？
https://git.io/JJnEZ
2、（图）之前部署的业务可以跑起来，今天想在另一台重新部署出现这个问题。登录后出现这样的提示是什么原因？刷新或者退出后重新登进也不行，依然提示未登录。
https://git.io/JJoUk
3、（图）HelloGo 入门，这里的执行文件在前面代码里没有指定 ip和端口，有什么用？
https://git.io/JJw2F
4、本地开发服务端总报错但是客户端可正常调用是什么原因？
https://git.io/JJJpH
5、提示tars not find adapter 是什么原因？
https://git.io/JJqFh



20200824

️

1、 github.com/TarsCloud/TarsGo/tars/tools/create_tars_server.sh: line 86: rename: command not found这个会有什么影响？
https://git.io/JJUdo
2、 (图)这里是什么问题？
https://git.io/JJGdF
3、 (图)按照教程编译，仍然提示tarsrpc 插件是unknown的，该如何编译这个protoc-gen-go插件？
https://git.io/JJGFe
4、 如果想增加一个PHP扩展，在TarsNode的docker里找不到PHP的源码，请问应该怎么操作呢？
https://git.io/JJUd6
5、 win7系统下编译TarsFramework无法通过是什么原因呢？TarsFramework才是从github上第一次下载的。
https://git.io/JJkaK


20200826
️

1、(图)这里的客服端报错是什么原因呢？
https://github.com/TarsCloud/Tars/issues/701
2、TARS使用k8s部署遇到的一个问题。
https://github.com/TarsCloud/Tars/issues/596
3、make_pair(TC_Mysql::DB_INT, 100);  100会变成d，这个如何编译？
https://github.com/TarsCloud/Tars/issues/642
4、如果要部署到生产环境，framework 和 tars-node建议用什么方式部署呢？
https://github.com/TarsCloud/Tars/issues/643
5、（图）时间不准，怎样同步成为最新的时间？mysql是通过docker安装的。
https://github.com/TarsCloud/Tars/issues/706



20200827
️

1、TARS 部署在本地，如何规避IP是 DHCP来的会经常变化的问题？
https://github.com/TarsCloud/Tars/issues/589
2、（图）一个关于servant的小问题。直连node节点的时候，服务名字不重要吗？
https://github.com/TarsCloud/Tars/issues/707
3、在对tars支持负载均衡进行测试时遇到问题。
https://github.com/TarsCloud/Tars/issues/587
4、erp系统，在删除基础部门信息时，要检查是否被其他表引用，有什么比较好的解决方案？
https://github.com/TarsCloud/Tars/issues/647
5、java 已经安装配置了，服务提示启动成功， 但是状态还是off，服务的端口未被占用，是因为什么呢？
https://github.com/TarsCloud/Tars/issues/648



20200828

️

1、脱离TARS环境调用微服务，setLocator怎么传参？
https://github.com/TarsCloud/Tars/issues/649
2、阿里云VPS，有内网IP，自动安装节点成功，节点管理里找不到怎么办？
https://github.com/TarsCloud/Tars/issues/650
3、A服务器上部署了Tars环境，如果将一些服务部署在B服务器上，是否需要在B服务器上也部署一套Tars环境？
https://github.com/TarsCloud/Tars/issues/655
4、新增部署节点时，可以填写我的节点地址吗？我想添加一个有公网ip的服务器地址，要如何操作？
https://github.com/TarsCloud/Tars/issues/656
5、从web控制端 配置了配置文件，go代码里我该怎么获取？
https://github.com/TarsCloud/Tars/issues/653



20200901

️

1、将配置文件push到服务节点之后。解析的时候，提示找不到文件是什么原因？
https://github.com/TarsCloud/Tars/issues/657
2、TARS用gson替代fastjson而不是用jackson替代主要是出于哪些考虑呢？
https://github.com/TarsCloud/Tars/issues/659
3、在往TARS上部署应用的时候，通过jenkins进行一键部署，我看有上传并发布的接口，但是不会用。
https://github.com/TarsCloud/Tars/issues/662
4、1.7版本后，日志直接用@Slf4j注解，看不到日志了，需要在哪儿配置吗？
https://github.com/TarsCloud/Tars/issues/665
5、如何看待RPC框架中的单连接和多连接(或连接池)设计?
https://github.com/TarsCloud/Tars/issues/666



20200902

️

1、proto3协议，java客户端调c服务端，返回结果null，无报错，怀疑解析有问题，是否有开关能在解析异常时打印信息？
https://github.com/TarsCloud/Tars/issues/667
2、滚动日志超过配置文件中的个数后会被自动删除吗？
https://github.com/TarsCloud/Tars/issues/673
3、利用docker.sh制作镜像，出现该问题怎么解决？
https://github.com/TarsCloud/Tars/issues/675
4、在部署TARS时 Mysql IP Address 和 INET 怎么找？
https://github.com/TarsCloud/Tars/issues/674
5、如何看系统里面现在使用的TARS框架版本呢？
https://github.com/TarsCloud/Tars/issues/783



20200903

️

1、打包发布后想要修改代码，是在本地修改然后再压缩上传吗？
https://github.com/TarsCloud/Tars/issues/676
2、在make docker 的时候，输入.\ docker.sh v1 ，提示commond not found是什么原因？
https://github.com/TarsCloud/Tars/issues/677
3、想开发一个TARS 的UDP服务，该怎么写？
https://github.com/TarsCloud/Tars/issues/683
4、（图）无法连接mysql问题怎么解决？mysql都重装了，命令行可以连上。
https://github.com/TarsCloud/Tars/issues/685
5、（图）这是少配置了什么导致错误？
https://github.com/TarsCloud/Tars/issues/686



20200904

️

1、tars app_log目录占分区空间较大，怎么安全迁移到/data分区呢？
https://github.com/TarsCloud/Tars/issues/687
2、调用服务的频率快一些服务就会重启，有什么解决办法？
https://github.com/TarsCloud/Tars/issues/689
3、TARS的服务，要使用自己的自定义的配置文档怎么操作？
https://github.com/TarsCloud/Tars/issues/725
4、（图）我添加新节点之后为什么节点列表里没有显示新节点呢？
https://github.com/TarsCloud/Tars/issues/726
5、使用go语言，TARS如何实现https？
https://github.com/TarsCloud/Tars/issues/727



20200905

️

1、ResponsePacket 和 RequestPacket 里都有context 和 status ，这两个值有什么区别？
https://github.com/TarsCloud/Tars/issues/729
2、如果都是TARS协议，可以在A中调用A::async_response_函数，但在http协议中如何做呢？
https://github.com/TarsCloud/Tars/issues/732
3、(图)虚拟机发生了断电，机器重启后TARS的应用服务都启动不了了，运行 /usr/local/app/tars/check.sh，显示这样的内容，这是什么情况？
https://github.com/TarsCloud/Tars/issues/733
4、关于tars http server的三个疑问。
https://github.com/TarsCloud/Tars/issues/736
5、TARS有没有对连接做保活？
https://github.com/TarsCloud/Tars/issues/737



20200906
️

1、为什么TarsGateway会依赖到tars_user_system？
https://github.com/TarsCloud/Tars/issues/738
2、低版本1.5.0 客户端里框架会有一个tars_client.log 日志，这个日志文件是一定打出来么？
https://github.com/TarsCloud/Tars/issues/739
3、gateway在内部是什么场景？
https://github.com/TarsCloud/Tars/issues/740
4、一个疑问：我有个服务A，线上环境，我有多个地区部署，配置不一样，这块是怎么搞呢？
https://github.com/TarsCloud/Tars/issues/741
5、(图)安装TarsGateway时这个token获取不到怎么办？
https://github.com/TarsCloud/Tars/issues/742



20200907

️

1、（图）发生这个错误是怎么回事？TarsWeb是新版的，TarsFramwork是旧版本的，不是docker部署。下载源码之后，执行sql文件夹的sql语句，修改config里面的配置，主要改数据库地址跟registry地址，把enable全部设置成false，最后执行npm run prd。
https://github.com/TarsCloud/Tars/issues/744
2、同一个TARS集群下，TarLog、TasPatch是不是和tarsAdminRegisrty一样，只能有一个实例？
https://github.com/TarsCloud/Tars/issues/747
3、远程登录，不方便用web停止。Tars cpp2.x在节点侧，手工停止服务的脚本命令是什么？
https://github.com/TarsCloud/Tars/issues/748
4、节点正常启动，和framework的docker在同一台机器上，但是后台看不到新加入的节点？这可能是什么原因呢？
https://github.com/TarsCloud/Tars/issues/750
5、(图)框架部署的时候已经启动了TarNode，还需要TarNode部署这一步单独安装TarNode吗？
https://github.com/TarsCloud/Tars/issues/751


20200908

️

1、请问部署集群的时候，是否可以通过 keepalived + 多台 Tars网关服务器呢？
https://github.com/TarsCloud/Tars/issues/760
2、请问部署集群的时候，是否可以通过 keepalived + 多台 Tars网关服务器呢？
https://github.com/TarsCloud/Tars/issues/761
3、jump_fcontext make_fcontext 的实现代码在哪儿？这两个接口说明在哪儿？
https://github.com/TarsCloud/Tars/issues/762
4、(图)重启后，pm2 list 下面没有 web 和 system，这是什么原因？
https://github.com/TarsCloud/Tars/issues/768
5、（图）这里生产环境部署，服务节点和应用节点可以放在一台服务器上吗？
https://github.com/TarsCloud/Tars/issues/769



20200909

️

1、如果节点分布在各个实体机器上，一个节点上部署了多个服务，可能有java、php、go，一旦其中一个服务出现异常，有可能其它服务会受牵连，有什么办法可以避免这种情况吗？
https://github.com/TarsCloud/Tars/issues/761
2、GatewayServer 要使用哪个服务，必须先配置<proxy>吗？
https://github.com/TarsCloud/Tars/issues/770
3、一般是什么程序去更新present_state这个字段呢？
https://github.com/TarsCloud/Tars/issues/772
4、在tars-java模板里面配置了tracing的配置，并且启动了一个zipkin的server，发现数据并没有上报到zipkin。可能会是什么原因呢? 
https://github.com/TarsCloud/Tars/issues/774
5、(图)在Application.cpp中 ，为什么要设置一个AdminObj？
https://github.com/TarsCloud/Tars/issues/775



20200910

️

1、TARS_CLIENT_LOGGER 可以在哪里配置关闭吗？
https://github.com/TarsCloud/Tars/issues/795
2、外网机器迁移了，目标obj的ip和端口修改了，这时候tars stringToProxy 会即刻拿到新的地址吗？
https://github.com/TarsCloud/Tars/issues/778
3、TARS在k8s已创建好，怎么做才能让外网访问里面的服务？
https://github.com/TarsCloud/Tars/issues/779
4、请问TARS微服调用超时时间怎么设置？
https://github.com/TarsCloud/Tars/issues/781
5、chmod a+x linux-install.sh ./linux-install.sh MYSQL_HOST MYSQL_PASSWORD INET REBUILD(false[default]/true) SLAVE(false[default]/true) MYSQL_USER MYSQL_PORT，mysql 必须设置密码吗？
https://github.com/TarsCloud/Tars/issues/799



20200911

️

1、提示下载的Tarsweb没有demo那个目录是怎么回事？
https://github.com/TarsCloud/Tars/issues/800
2、（图）在TARS管理管理后台修改替换模板，是不是之前添加的微服要手动去修改模板文件才会生效？
https://github.com/TarsCloud/Tars/issues/801
3、(图)swoole扩展已经安装过了，模板里的php执行路径也改过了，出现如图所示的提示是怎么回事？
https://github.com/TarsCloud/Tars/issues/802
4、(图)这个问题有可能是什么导致的？
https://github.com/TarsCloud/Tars/issues/803
5、(图&日志)这里出现报错是什么原因？
https://github.com/TarsCloud/Tars/issues/804



20200913

️

1、imp.go里面的接口实现，是以协程的方式被调用的吗？
https://github.com/TarsCloud/Tars/issues/806
2、pp监控功能上报的数据需要自己去写ui展示吗？
https://github.com/TarsCloud/Tars/issues/807
3、TarsGo如何实现HTTPS？
https://github.com/TarsCloud/Tars/issues/786
4、tars2php是不是对枚举类型不支持，和cpp交互是怎么处理枚举类型呢？
https://github.com/TarsCloud/Tars/issues/787
5、Tars cpp2.x在节点侧，手工停止服务的脚本命令是什么？
https://github.com/TarsCloud/Tars/issues/788



20200914

️

1、TarsGateway使用安装脚本install.sh安装过程显示成功，最后的测试结果显示失败是怎么回事？
https://github.com/TarsCloud/Tars/issues/789
2、重启后 pm2 list 下面没有 web 和 system 请问是什么原因呢？
https://github.com/TarsCloud/Tars/issues/792
3、运行linux_install.sh安装TARS框架出错，日志输出mysql访问失败？是否是bug？
https://github.com/TarsCloud/Tars/issues/793
4、TARS可以定期清理上传的包吗？
https://github.com/TarsCloud/Tars/issues/814
5、（图）标题在哪里可以修改？
https://github.com/TarsCloud/Tars/issues/815



20200915

️

1、TRACE swPort_onRead_http (ERRNO 8002): Bad Request: unknown protocol from session#4 on 172 .25.0.4:21777  Communicator monitor route failed servantName :tars . tarsstat . Statobj
这是什么问题？
https://github.com/TarsCloud/Tars/issues/699
2、如何编译protoc-gen-go插件？
https://github.com/TarsCloud/Tars/issues/700
3、(图)这里的客服端报错是什么原因呢？
https://github.com/TarsCloud/Tars/issues/701
4、平台上，服务管理-》服务监控，有的基础服务没有数据，业务服务全都没有数据，这个情况正常吗？
https://github.com/TarsCloud/Tars/issues/702
5、看上去TARS运行起来了，但通过浏览器访问公网的 tars web无法连接，有什么排查的方法？.
https://github.com/TarsCloud/Tars/issues/703



20200916

️

1、（图）使用1核2GB的腾讯云在编译TARS源码的时候报错，请问如何解决？
https://github.com/TarsCloud/Tars/issues/705
2、（图）时间不准，怎样同步成为最新的时间？mysql是通过docker安装的。
https://github.com/TarsCloud/Tars/issues/706
3、（图）一个关于servant的小问题。直连node节点的时候，服务名字不重要吗？
https://github.com/TarsCloud/Tars/issues/707
4、（图）文件解析有问题，是目前pom.xml的依赖不全吗？我还需要导入别的依赖吗？
https://github.com/TarsCloud/Tars/issues/708
5、在服务部署时，线程数、最大连接数、队列最大长度等修改后会立即生效吗？
https://github.com/TarsCloud/Tars/issues/709



20200917

️

1、1.7版本后，日志直接用@Slf4j注解，看不到日志了，需要在哪儿配置吗？
https://github.com/TarsCloud/Tars/issues/665
2、如何看待RPC框架中的单连接和多连接(或连接池)设计?
https://github.com/TarsCloud/Tars/issues/666
3、proto3协议，java客户端调c服务端，返回结果null，无报错，怀疑解析有问题，是否有开关能在解析异常时打印信息？
https://github.com/TarsCloud/Tars/issues/667
4、在外网安装好了TARS，要搬到内网去，改变了IP，有什么办法让TARS能工作起来？
https://github.com/TarsCloud/Tars/issues/668
5、服务是活的的，obj的端口却没有监听该怎么办？
https://github.com/TarsCloud/Tars/issues/669



20200918

️

1、一个节点只能发布一个服务吗？我的服务是tar.gz，用tars发布完后我的安装包会解压到哪里？
https://github.com/TarsCloud/Tars/issues/615
2、TarsGo如何实现HTTPS？
https://github.com/TarsCloud/Tars/issues/786
3、tars2php是不是对枚举类型不支持，和cpp交互是怎么处理枚举类型呢？
https://github.com/TarsCloud/Tars/issues/787
4、Tars cpp2.x在节点侧，手工停止服务的脚本命令是？远程登录，不方便用web停止。
https://github.com/TarsCloud/Tars/issues/788
5、（图）防火墙已关闭，配置文件也配置好了，出现如图所示的问题如何解决？CPU使用情况见图
https://github.com/TarsCloud/Tars/issues/600



20200919

️

1、(图)这两个TARS服务间调用时报的错误。
https://github.com/TarsCloud/Tars/issues/710
2、(图)文档上说的cmake_tars_server.sh是自动生成cmakelists.txt的工具。tarscpp库已经正常编译安装，并生成tar协议了，但我运行后没生成cmakelist.txt。
https://github.com/TarsCloud/Tars/issues/691
3、(图)在调用服务接口的时候，出现错误，客户端日志说是链接被关闭了，但是服务端是收到了这个请求的，请问是怎么回事呢？
https://github.com/TarsCloud/Tars/issues/711
4、(图)这种报错的原因是什么？
https://github.com/TarsCloud/Tars/issues/692
5、(图)全局开启了go mod，执行图中的划线处提示bash: cd: C:\Users\Admin\go/src/github.com/TarsCloud/TarsGo/tars/tools/tars2go: No such file or directory，全局开启go mod是什么原因呢？
https://github.com/TarsCloud/Tars/issues/694



20200920

️

1、通过docker方式本地安装了 framework 和 node 各一台，现在通过web控制台启动一个spring boot 项目，无法顺利启动。
https://github.com/TarsCloud/Tars/issues/601
2、(图)这个镜像在哪里可以看到对应的TarsFramework代码的版本号？还有这个stable呢？
https://github.com/TarsCloud/Tars/issues/716
3、(图)这里-cp后面的目录是怎么加进来的？程序中没有这部分地址要怎么处理呢？
https://github.com/TarsCloud/Tars/issues/717
4、TARS框架现在访问是http，可以改成https吗？
https://github.com/TarsCloud/Tars/issues/628
5、（图）之前部署的业务可以跑起来，今天想在另一台重新部署出现这个问题。登录后出现这样的提示是什么原因？刷新或者退出后重新登进也不行，依然提示未登录。https://github.com/TarsCloud/Tars/issues/784



20200921

️

1、Tarsframework的master分支编译完后，如果我需要升级tarsnode，只替换tarsnode的执行文件还是所有的tarsnode都替换一遍? 
https://github.com/TarsCloud/Tars/ISSUE/556
2、遇到下面的配置问题如何解决？备注图为tarsnode的启动日志（见备注图）
https://github.com/TarsCloud/Tars/ISSUE/557
3、如备注图所示，按照文档写HelloServer,未发现这个配置文件，这个服务的配置文件是手动生成的还是有工具自动生成的？
https://github.com/TarsCloud/Tars/ISSUE/558
4、如图所示，每次编译需要自己将servant目录和util目录复制到工程目录下面吗？
https://github.com/TarsCloud/Tars/ISSUE/559
5、AdminF.tars: 17: error: identifier can't start with 'tars'怎么解决？
https://github.com/TarsCloud/Tars/ISSUE/560



20200922

️

1、（图）跑 TARS 自带 example 下的 CoroutineDemo 用例 testCoro， 抛出这样的异常，是什么原因？
https://github.com/TarsCloud/Tars/ISSUE/611
2、为什么TARS的生产代码中存在大量的assert，这个在编译的时候会被禁止掉，是吗？线上的会禁掉呀？用哪个编译选项禁止？
https://github.com/TarsCloud/Tars/ISSUE/592
3、请问备注中的图在windows上源码安装的时候出现这个问题是怎么回事？ 
https://github.com/TarsCloud/Tars/ISSUE/612
4、（图）TarsFramework 密码忘了如何处理？
https://github.com/TarsCloud/Tars/ISSUE/588
5、（图）请问安装的时候报错该怎么处理？
https://github.com/TarsCloud/Tars/ISSUE/613



20200923

️

1、为什么在使用TARS时，logback.xml文件获取不到？
https://github.com/TarsCloud/Tars/issues/621
2、请问web过来的请求直接到node的话，都有哪些功能操作？
https://github.com/TarsCloud/Tars/issues/622
3、日志打到TarsLog里后，在web页面上可以流式查看检索吗？
https://github.com/TarsCloud/Tars/issues/623
4、docker镜像没有java环境吗？跑java微服需要自己进docker环境中配置java吗？
https://github.com/TarsCloud/Tars/issues/624
5、如何在TarsCPP中创建自己定义tcp连接外部服务器(非TAF)？
https://github.com/TarsCloud/Tars/issues/626



20200924

️

1、使用docker创建虚拟网络，TarsFramework中使用宿主的ip，但发布时会报错 。
https://github.com/TarsCloud/Tars/issues/627
2、MDC支持跨服务链路追踪吗？
https://github.com/TarsCloud/Tars/issues/590
3、1.7.1的tars-Java是不是不支持染色日志？
https://github.com/TarsCloud/Tars/issues/629
4、请问TARS有分布式事务解决方案吗？
https://github.com/TarsCloud/Tars/issues/631
5、TARS框架部署时，每个组件需要的硬件要求是怎么样的？
https://github.com/TarsCloud/Tars/issues/594



20200925

️

1、这两个TARS服务间调用时报的错误。
https://github.com/TarsCloud/Tars/issues/710
2、（图）文档上说的cmake_tars_server.sh是自动生成cmakelists.txt的工具。TarsCpp库已经正常编译安装，并生成tar协议了，但我运行后没生成cmakelist.txt。
https://github.com/TarsCloud/Tars/issues/691
3、（图）在调用服务接口的时候，出现错误，客户端日志说是链接被关闭了，但是服务端是收到了这个请求的，请问是怎么回事呢？
https://github.com/TarsCloud/Tars/issues/711
4、（图）如备注图所示这种报错的原因是什么？
https://github.com/TarsCloud/Tars/issues/692
5、全局开启了go mod，执行备注图1的划线处提示bash: cd: C:\Users\Admin\go/src/github.com/TarsCloud/TarsGo/tars/tools/tars2go: No such file or directory，全局开启go mod是什么原因呢？
https://github.com/TarsCloud/Tars/issues/694



20200926

️

1、(图)这个镜像在哪里可以看到对应的TarsFramework代码的版本号？还有这个stable呢？
https://github.com/TarsCloud/Tars/issues/716
2、(图)这里-cp后面的目录是怎么加进来的？程序中没有这部分地址要怎么处理呢？
https://github.com/TarsCloud/Tars/issues/717
3、TARS框架现在访问是http，可以改成https吗？
https://github.com/TarsCloud/Tars/issues/628
4、（图）之前部署的业务可以跑起来，今天想在另一台重新部署出现这个问题。登录后出现这样的提示是什么原因？刷新或者退出后重新登进也不行，依然提示未登录。https://github.com/TarsCloud/Tars/issues/784
5、（图）HelloGo 入门，这里的执行文件在前面代码里没有指定 ip和端口，有什么用？
https://github.com/TarsCloud/Tars/issues/782



20200927

1、业务初始化接口里是不是不能进行耗时操作？
https://github.com/TarsCloud/Tars/issues/637
2、tc_mysql的库是线程安全的吗？
https://github.com/TarsCloud/Tars/issues/575
3、如图所示的数据库问题，为什么显示没有数据？
https://github.com/TarsCloud/Tars/issues/580
4、如备注如图所示，提示tars not find adapter 是什么原因？
https://github.com/TarsCloud/Tars/issues/658
5、（图）请问这个是添加在模板配置文件里吗？
https://github.com/TarsCloud/Tars/issues/712



20200928

1、（图）如果我把IP地址和端口号改为自己的地址和端口号，直接编译运行这段代码就可以吗？
https://github.com/TarsCloud/Tars/issues/562
2、想用单项调用提高效率，是用tcp还是udp？
https://github.com/TarsCloud/Tars/issues/565
3、java里的stringToProxy，会触发ConCurrentHashMap computeIfAbsent的死循环bug。(见备注例子，jdk版本为1.8)
https://github.com/TarsCloud/Tars/issues/566
4、P90，P99和P999的汇总思路是什么？
https://github.com/TarsCloud/Tars/issues/582
5、微服务通过linux控制台直接关闭和启动，那么框架里面的web管理后台的控制会失效吗？
https://github.com/TarsCloud/Tars/issues/591



20200929

1、web过来的请求直接到node的话，都有哪些功能操作？
https://github.com/TarsCloud/Tars/issues/622
2、日志打到TarsLog里后，在web页面上可以流式查看检索吗？
https://github.com/TarsCloud/Tars/issues/623
3、(图)这两个类是自动生成的？需要执行怎样的命令？或者文档在哪里？
https://github.com/TarsCloud/Tars/issues/718
4、(图)这里的负载是什么意思呢？为什么有的显示0，有的显示1， 安装的时候没有做出相关的选择。
https://github.com/TarsCloud/Tars/issues/780
5、docker镜像没有java环境吗？跑java微服需要自己进docker环境中配置java吗？
https://github.com/TarsCloud/Tars/issues/624



20200930

1、（图）在调用服务接口的时候，客户端日志提示链接被关闭，但是服务端是收到了这个请求的，请问是什么原因呢？
https://github.com/TarsCloud/Tars/issues/711
2、（图）请问这个是添加在模板配置文件里吗？
https://github.com/TarsCloud/Tars/issues/712
3、请问java中定义xxx.tars文件时，有时间类型吗？
https://github.com/TarsCloud/Tars/issues/713
4、（图）服务监控不能用，报错提示是查询602，我使用windows的docker里没有对应的localtime文件，是因为没有导入时区吗？
https://github.com/TarsCloud/Tars/issues/714
5、mac自动化部署是实际安装过程中好像没法自动安装node npm。
https://github.com/TarsCloud/Tars/issues/549

20201001

1、TARS 部署在本地，如何规避IP是 DHCP来的会经常变化的问题？
https://github.com/TarsCloud/Tars/issues/589
2、（图）一个关于servant的小问题。直连node节点的时候，服务名字不重要吗？
https://github.com/TarsCloud/Tars/issues/707
3、在对tars支持负载均衡进行测试时遇到问题。
https://github.com/TarsCloud/Tars/issues/587
4、erp系统，在删除基础部门信息时，要检查是否被其他表引用，有什么比较好的解决方案？
https://github.com/TarsCloud/Tars/issues/647
5、java 已经安装配置了，服务提示启动成功， 但是状态还是off，服务的端口未被占用，是因为什么呢？
https://github.com/TarsCloud/Tars/issues/648



20201002

1、 首次搭建TARS，搭建环境，cmake时报错怎么处理？
https://github.com/TarsCloud/Tars/issues/791
2、重启后 pm2 list 下面没有 web 和 system 是什么原因？
https://github.com/TarsCloud/Tars/issues/792
3、运行linux_install.sh安装TARS框架出错，日志输出mysql访问失败？是否是bug？
https://github.com/TarsCloud/Tars/issues/793 
4、为什么服务打包发布后后无法运行？报错“down，server is inactive”.
https://github.com/TarsCloud/Tars/issues/579 
5、TARS_CLIENT_LOGGER 可以在哪里配置关闭吗？
https://github.com/TarsCloud/Tars/issues/795



20201003

1、TARS有分布式事务解决方案吗？
https://github.com/TarsCloud/Tars/issues/631
2、TarsNode和framework docker都安装在一台宿主机上，会有什么冲突吗？
https://github.com/TarsCloud/Tars/issues/632
3、本地开发服务端总报错但是客户端可正常调用是什么原因？
https://github.com/TarsCloud/Tars/issues/633
4、（图）cache在哪里会更新？
https://github.com/TarsCloud/Tars/issues/634
5、代码管理与代码编译是怎样在TARS中使用的？
https://github.com/TarsCloud/Tars/issues/635



20201005

1、提示 “bash: cd: C:\Users\Admin\go/src/github.com/TarsCloud/TarsGo/tars/tools/tars2go: No such file or directory” 是什么原因呢？
https://github.com/TarsCloud/Tars/issues/694
2、（图）主控和节点目前在一台机器，部署的服务启动不了，我看了下启动脚本，执行路径里多一个空格有可能是什么原因？
https://github.com/TarsCloud/Tars/issues/695
3、（图）标注处所示windows挂载不上去，因为没有这个目录，这是什么原因呢？
https://github.com/TarsCloud/Tars/issues/696
4、（图）图中的错误是跟什么插件版本有关？
https://github.com/TarsCloud/Tars/issues/697
5、（图）第一次登录，在修改密码时提示“系统内部错误”，检查日志提示表只读，可能是什么原因呢？
https://github.com/TarsCloud/Tars/issues/698



20201006
1、（图）这是framwork的3001吗？什么是管理端口？
https://github.com/TarsCloud/Tars/issues/715
2、例如3000这个端口已经改成我自定义的8000端口，但是访问web控制台的时候，第一步index.html中会自动跳转到:3001的端口， 这个3001端口又在哪里改呢？
https://github.com/TarsCloud/Tars/issues/609
3、（图）这个镜像在哪里可以看到对应的TarsFramework代码的版本号？
https://github.com/TarsCloud/Tars/issues/716
4、（图）请问图中-cp后面的目录是怎么加进来的？程序中没有这部分地址要怎么处理呢？
https://github.com/TarsCloud/Tars/issues/717
5、（图）请问HelloPrx和HelloPrxCallback这两个类是自动生成的吗？
https://github.com/TarsCloud/Tars/issues/718



20201007
1、测试环境如何保证日志的级别是debug级别的呢？
https://github.com/TarsCloud/Tars/issues/723
2、（图）提示这个错误，是什么原因导致的？
https://github.com/TarsCloud/Tars/issues/724
3、TARS的服务，要使用自己的自定义的配置文档怎么操作？
https://github.com/TarsCloud/Tars/issues/725
4、（图）我添加新节点之后为什么节点列表里没有显示新节点呢？
https://github.com/TarsCloud/Tars/issues/726
5、ResponsePacket 和 RequestPacket 里都有context 和 status ，这两个值有什么区别？
https://github.com/TarsCloud/Tars/issues/729



20201008
1、(图)tars+Yii远程下载，不在exit的话就这种情况。
https://github.com/TarsCloud/Tars/issues/730
2、如果都是TARS协议，可以在A中调用A::async_response_函数，但在http协议中如何做呢？
https://github.com/TarsCloud/Tars/issues/732
3、(图)虚拟机发生了断电，机器重启后TARS的应用服务都启动不了了，运行 /usr/local/app/tars/check.sh，显示这样的内容，这是什么情况？
https://github.com/TarsCloud/Tars/issues/733
4、关于tars http server的三个疑问。
https://github.com/TarsCloud/Tars/issues/736
5、TARS有没有对连接做保活？
https://github.com/TarsCloud/Tars/issues/737


20201010

1、一个疑问：我有个服务A，线上环境，我有多个地区部署，配置不一样，这块是怎么搞呢？
https://github.com/TarsCloud/Tars/issues/741
2、(图)安装TarsGateway时这个token获取不到怎么办？
https://github.com/TarsCloud/Tars/issues/742
3、(图)手动启动TarsGateway，报了这个错，是怎么回事？
https://github.com/TarsCloud/Tars/issues/743
4、 业务服务平台上面启动不了，发现start和stop脚本，执行不太一样，start直接php命令 stop是执行路径，这样做是有什么用意吗？
https://github.com/TarsCloud/Tars/issues/745
5、同一个TARS集群下，TarLog、TasPatch是不是和tarsAdminRegisrty一样，只能有一个实例？
https://github.com/TarsCloud/Tars/issues/747




20201011


1、(图)gateway文档这部分，要传输的内容格式是什么？
https://github.com/TarsCloud/Tars/issues/756
2、请问如果使用TARS部署的http网站，怎么配置域名指向，是还需要nginx进行转发吗？
https://github.com/TarsCloud/Tars/issues/757
3、（图）报错的原因是什么？为什么显示没有数据？
https://github.com/TarsCloud/Tars/issues/580
4、请问TARS调用参数可以传递list对象吗？
https://github.com/TarsCloud/Tars/issues/758
5、请问部署集群的时候，是否可以通过 keepalived + 多台 Tars网关服务器呢？
https://github.com/TarsCloud/Tars/issues/760



20201012
1、如果节点分布在各个实体机器上，一个节点上部署了多个服务，可能有java、php、go，一旦其中一个服务出现异常，有可能其它服务会受牵连，有什么办法可以避免这种情况吗？
https://github.com/TarsCloud/Tars/issues/761
2、jump_fcontext make_fcontext 的实现代码在哪儿？这两个接口说明在哪儿？
https://github.com/TarsCloud/Tars/issues/762
3、代码里用的相对路径，原来项目里都是自己脚本启动的，是相对路径，拿过来后，项目中代码找不到服务的根目录了，所以报错，这是怎么回事？(详情见issue)
https://github.com/TarsCloud/Tars/issues/763
4、我要发布个服务，一个 Demo 的 Hello World 服务，服务端准备用 Go 写。但有三个问题不懂。(详情见issue)
https://github.com/TarsCloud/Tars/issues/764
5、（图）windows上源码安装的时候出现这个问题可能是什么原因导致的？
https://github.com/TarsCloud/Tars/issues/612




20201013

1、(图)发布DCacheOptServer失败。
https://github.com/TarsCloud/Tars/issues/767
2、GatewayServer 要使用某个服务，必须先配置<proxy>吗？
https://github.com/TarsCloud/Tars/issues/770
3、（图）在服务器上通过docker安装框架成功之后，3001端口可以访问并重置了密码，安装网关生成token时发现3001无法访问了，3000可以正常访问，请问下有可能是什么原因导致的？
https://github.com/TarsCloud/Tars/issues/771
4、（图）运行一段时间后，两个服务自动off，socket连接不上，提示“Communicator monitor route failed servantName:tars.tarsproperty.PropertyObj” “ Communicator monitor route failed servantName:tars.tarsstat.StatObj”，可能是什么原因呢？
https://github.com/TarsCloud/Tars/issues/603
5、（图）一般是什么程序去更新present_state这个字段呢？
https://github.com/TarsCloud/Tars/issues/772



20201015

1、一个节点只能发布一个服务吗？我的服务是tar.gz，用TARS发布完后我的安装包会解压到哪里？
https://github.com/TarsCloud/Tars/issues/615
2、（图）HelloGo 入门，这里的执行文件在前面代码里没有指定 ip和端口，有什么用？
https://github.com/TarsCloud/Tars/issues/782
3、如备注图所示的两个锁会被锁住吗？
https://github.com/TarsCloud/Tars/issues/542
4、（图）防火墙已关闭，配置文件也配置好了，出现如图所示的问题如何解决？
https://github.com/TarsCloud/Tars/issues/600
5、通过docker方式本地安装了 framework 和 node 各一台，现在通过web控制台启动一个spring boot 项目，无法顺利启动。但是我登入容器后，启动这个jar，可以正常工作，这个问题该怎么处理？
https://github.com/TarsCloud/Tars/issues/601



20201016

1、（图）TARS管理后台超时提示没有操作权限，是因为cookie中uid值变了，不知道为什么从admin 变成一个很长的字符串了，打开管理后台后 ，挂在那里过20多分钟uid值就变化了。
https://github.com/TarsCloud/Tars/issues/608
2、（图）发生这个错误是怎么回事？TarsWeb是新版的，TarsFramwork是旧版本的，不是docker部署。下载源码之后，执行sql文件夹的sql语句，修改config里面的配置，主要改数据库地址跟registry地址，把enable全部设置成false，最后执行npm run prd。
https://github.com/TarsCloud/Tars/issues/744
3、（图）无法连接mysql问题怎么解决？mysql都重装了，命令行可以连上。
https://github.com/TarsCloud/Tars/issues/685
4、如何看系统里面现在使用的TARS框架版本呢？
https://github.com/TarsCloud/Tars/issues/783
5、（图）之前部署的业务可以跑起来，今天想在另一台重新部署出现这个问题。登录后出现这样的提示是什么原因？刷新或者退出后重新登进也不行，依然提示未登录。
https://github.com/TarsCloud/Tars/issues/784




20201017


1、打包发布后想要修改代码，是在本地修改然后再压缩上传吗？
https://github.com/TarsCloud/Tars/issues/676
2、在make docker 的时候，输入.\ docker.sh v1 ，提示commond not found是什么原因？
https://github.com/TarsCloud/Tars/issues/677
3、想开发一个TARS 的UDP服务，该怎么写？
https://github.com/TarsCloud/Tars/issues/683
4、（图）无法连接mysql问题怎么解决？mysql都重装了，命令行可以连上。
https://github.com/TarsCloud/Tars/issues/685
5、（图）这是少配置了什么导致错误？
https://github.com/TarsCloud/Tars/issues/686




20201019

1、打包发布后想要修改代码，是在本地修改然后再压缩上传吗？
https://github.com/TarsCloud/Tars/issues/676
2、在make docker 的时候，输入.\ docker.sh v1 ，提示commond not found是什么原因？
https://github.com/TarsCloud/Tars/issues/677
3、想开发一个TARS 的UDP服务，该怎么写？
https://github.com/TarsCloud/Tars/issues/683
4、（图）无法连接mysql问题怎么解决？mysql都重装了，命令行可以连上。
https://github.com/TarsCloud/Tars/issues/685
5、（图）这是少配置了什么导致错误？
https://github.com/TarsCloud/Tars/issues/686




20201020


1、低版本1.5.0 客户端里框架会有一个tars_client.log 日志，这个日志文件是一定打出来么？
https://github.com/TarsCloud/Tars/issues/739
2、外网机器迁移了，目标obj的ip和端口修改了，这时候tars stringToProxy 会即刻拿到新的地址吗？
https://github.com/TarsCloud/Tars/issues/778
3、TARS在k8s已创建好，怎么做才能让外网访问里面的服务？
https://github.com/TarsCloud/Tars/issues/779
4、tars-install里的replace是脚本中的replace函数还是mysql-tool替换文本？
https://github.com/TarsCloud/Tars/issues/545
5、docker容器的宿主机vmware的centos可以联通 windows的mysql，容器里面启动却连不上mysql，怎么解决？ 
https://github.com/TarsCloud/Tars/issues/548



20201021


1、(图)这里的客服端报错是什么原因呢？
https://github.com/TarsCloud/Tars/issues/701
2、TARS使用k8s部署遇到的一个问题。
https://github.com/TarsCloud/Tars/issues/596
3、make_pair(TC_Mysql::DB_INT, 100); 100会变成d，这个如何编译？
https://github.com/TarsCloud/Tars/issues/642
4、如果要部署到生产环境，framework 和 tars-node建议用什么方式部署呢？
https://github.com/TarsCloud/Tars/issues/643
5、（图）时间不准，怎样同步成为最新的时间？mysql是通过docker安装的。
https://github.com/TarsCloud/Tars/issues/706



20201022

1、TARS 部署在本地，如何规避IP是 DHCP来的会经常变化的问题？
https://github.com/TarsCloud/Tars/issues/589
2、（图）一个关于servant的小问题。直连node节点的时候，服务名字不重要吗？
https://github.com/TarsCloud/Tars/issues/707
3、在对TARS支持负载均衡进行测试时遇到问题。
https://github.com/TarsCloud/Tars/issues/587
4、erp系统，在删除基础部门信息时，要检查是否被其他表引用，有什么比较好的解决方案？
https://github.com/TarsCloud/Tars/issues/647
5、java 已经安装配置了，服务提示启动成功， 但是状态还是off，服务的端口未被占用，是因为什么呢？
https://github.com/TarsCloud/Tars/issues/648



20201023

1、脱离TARS环境调用微服务，setLocator怎么传参？
https://github.com/TarsCloud/Tars/issues/649
2、阿里云VPS，有内网IP，自动安装节点成功，节点管理里找不到怎么办？
https://github.com/TarsCloud/Tars/issues/650
3、A服务器上部署了Tars环境，如果将一些服务部署在B服务器上，是否需要在B服务器上也部署一套Tars环境？
https://github.com/TarsCloud/Tars/issues/655
4、新增部署节点时，可以填写我的节点地址吗？我想添加一个有公网ip的服务器地址，要如何操作？
https://github.com/TarsCloud/Tars/issues/656
5、从web控制端 配置了配置文件，go代码里我该怎么获取？
https://github.com/TarsCloud/Tars/issues/653




20201025

1、将配置文件push到服务节点之后。解析的时候，提示找不到文件是什么原因？
https://github.com/TarsCloud/Tars/issues/657
2、TARS用gson替代fastjson而不是用jackson替代主要是出于哪些考虑呢？
https://github.com/TarsCloud/Tars/issues/659
3、在往TARS上部署应用的时候，通过jenkins进行一键部署，我看有上传并发布的接口，但是不会用。
https://github.com/TarsCloud/Tars/issues/662
4、1.7版本后，日志直接用@Slf4j注解，看不到日志了，需要在哪儿配置吗？
https://github.com/TarsCloud/Tars/issues/665
5、如何看待RPC框架中的单连接和多连接(或连接池)设计?
https://github.com/TarsCloud/Tars/issues/666



20201026


1、proto3协议，java客户端调c服务端，返回结果null，无报错，怀疑解析有问题，是否有开关能在解析异常时打印信息？
https://github.com/TarsCloud/Tars/issues/667
2、滚动日志超过配置文件中的个数后会被自动删除吗？
https://github.com/TarsCloud/Tars/issues/673
3、利用docker.sh制作镜像，出现该问题怎么解决？
https://github.com/TarsCloud/Tars/issues/675
4、在部署TARS时 Mysql IP Address 和 INET 怎么找？
https://github.com/TarsCloud/Tars/issues/674
5、如何看系统里面现在使用的TARS框架版本呢？
https://github.com/TarsCloud/Tars/issues/783




20201028


1、为什么TarsGateway会依赖到tars_user_system？
https://github.com/TarsCloud/Tars/issues/738
2、低版本1.5.0 客户端里框架会有一个tars_client.log 日志，这个日志文件是一定打出来么？
https://github.com/TarsCloud/Tars/issues/739
3、gateway在内部是什么场景？
https://github.com/TarsCloud/Tars/issues/740
4、一个疑问：我有个服务A，线上环境，我有多个地区部署，配置不一样，这块是怎么搞呢？
https://github.com/TarsCloud/Tars/issues/741
5、(图)安装TarsGateway时这个token获取不到怎么办？
https://github.com/TarsCloud/Tars/issues/742




20201029

1、（图）发生这个错误是怎么回事？TarsWeb是新版的，TarsFramwork是旧版本的，不是docker部署。下载源码之后，执行sql文件夹的sql语句，修改config里面的配置，主要改数据库地址跟registry地址，把enable全部设置成false，最后执行npm run prd。
https://github.com/TarsCloud/Tars/issues/744
2、同一个TARS集群下，TarLog、TasPatch是不是和tarsAdminRegisrty一样，只能有一个实例？
https://github.com/TarsCloud/Tars/issues/747
3、远程登录，不方便用web停止。Tars cpp2.x在节点侧，手工停止服务的脚本命令是什么？
https://github.com/TarsCloud/Tars/issues/748
4、节点正常启动，和framework的docker在同一台机器上，但是后台看不到新加入的节点？这可能是什么原因呢？
https://github.com/TarsCloud/Tars/issues/750
5、(图)框架部署的时候已经启动了TarNode，还需要TarNode部署这一步单独安装TarNode吗？
https://github.com/TarsCloud/Tars/issues/751



20201030

1、请问部署集群的时候，是否可以通过 keepalived + 多台 Tars网关服务器呢？
https://github.com/TarsCloud/Tars/issues/760
2、请问部署集群的时候，是否可以通过 keepalived + 多台 Tars网关服务器呢？
https://github.com/TarsCloud/Tars/issues/761
3、jump_fcontext make_fcontext 的实现代码在哪儿？这两个接口说明在哪儿？
https://github.com/TarsCloud/Tars/issues/762
4、(图)重启后，pm2 list 下面没有 web 和 system，这是什么原因？
https://github.com/TarsCloud/Tars/issues/768
5、（图）这里生产环境部署，服务节点和应用节点可以放在一台服务器上吗？
https://github.com/TarsCloud/Tars/issues/769




20201031


1、如果节点分布在各个实体机器上，一个节点上部署了多个服务，可能有java、php、go，一旦其中一个服务出现异常，有可能其它服务会受牵连，有什么办法可以避免这种情况吗？
https://github.com/TarsCloud/Tars/issues/761
2、GatewayServer 要使用哪个服务，必须先配置<proxy>吗？
https://github.com/TarsCloud/Tars/issues/770
3、一般是什么程序去更新present_state这个字段呢？
https://github.com/TarsCloud/Tars/issues/772
4、在tars-java模板里面配置了tracing的配置，并且启动了一个zipkin的server，发现数据并没有上报到zipkin。可能会是什么原因呢? 
https://github.com/TarsCloud/Tars/issues/774
5、(图)在Application.cpp中 ，为什么要设置一个AdminObj？
https://github.com/TarsCloud/Tars/issues/775




20201101

1、TARS_CLIENT_LOGGER 可以在哪里配置关闭吗？
https://github.com/TarsCloud/Tars/issues/795
2、外网机器迁移了，目标obj的ip和端口修改了，这时候tars stringToProxy 会即刻拿到新的地址吗？
https://github.com/TarsCloud/Tars/issues/778
3、TARS在k8s已创建好，怎么做才能让外网访问里面的服务？
https://github.com/TarsCloud/Tars/issues/779
4、请问TARS微服调用超时时间怎么设置？
https://github.com/TarsCloud/Tars/issues/781
5、chmod a+x linux-install.sh ./linux-install.sh MYSQL_HOST MYSQL_PASSWORD INET REBUILD(false[default]/true) SLAVE(false[default]/true) MYSQL_USER MYSQL_PORT，mysql 必须设置密码吗？
https://github.com/TarsCloud/Tars/issues/799



20201103

1、imp.go里面的接口实现，是以协程的方式被调用的吗？
https://github.com/TarsCloud/Tars/issues/806
2、pp监控功能上报的数据需要自己去写ui展示吗？
https://github.com/TarsCloud/Tars/issues/807
3、TarsGo如何实现HTTPS？
https://github.com/TarsCloud/Tars/issues/786
4、tars2php是不是对枚举类型不支持，和cpp交互是怎么处理枚举类型呢？
https://github.com/TarsCloud/Tars/issues/787
5、Tars cpp2.x在节点侧，手工停止服务的脚本命令是什么？
https://github.com/TarsCloud/Tars/issues/788



20201106

1、TRACE swPort_onRead_http (ERRNO 8002): Bad Request: unknown protocol from session#4 on 172 .25.0.4:21777  Communicator monitor route failed servantName :tars . tarsstat . Statobj这是什么问题？
https://github.com/TarsCloud/Tars/issues/699
2、如何编译protoc-gen-go插件？
https://github.com/TarsCloud/Tars/issues/700
3、(图)这里的客服端报错是什么原因呢？
https://github.com/TarsCloud/Tars/issues/701
4、平台上，服务管理-》服务监控，有的基础服务没有数据，业务服务全都没有数据，这个情况正常吗？
https://github.com/TarsCloud/Tars/issues/702
5、看上去TARS运行起来了，但通过浏览器访问公网的 tars web无法连接，有什么排查的方法？.
https://github.com/TarsCloud/Tars/issues/703



20201108

1、将配置文件push到服务节点之后。解析的时候，提示找不到文件是什么原因？
https://github.com/TarsCloud/Tars/issues/657
2、TARS用gson替代fastjson而不是用jackson替代主要是出于哪些考虑呢？
https://github.com/TarsCloud/Tars/issues/659
3、在往TARS上部署应用的时候，通过jenkins进行一键部署，我看有上传并发布的接口，但是不会用。
https://github.com/TarsCloud/Tars/issues/662
4、1.7版本后，日志直接用@Slf4j注解，看不到日志了，需要在哪儿配置吗？
https://github.com/TarsCloud/Tars/issues/665
5、如何看待RPC框架中的单连接和多连接(或连接池)设计?
https://github.com/TarsCloud/Tars/issues/666



20201110

1、安装发布提示超时，应该如何处理？docker版本为Version:2.1.0。
https://github.com/TarsCloud/Tars/issues/797
2、restart server, stop error:EM_TARS_UNKNOWN_ERR 出现这种错误怎么排查问题？
https://github.com/TarsCloud/Tars/issues/798
3、chmod a+x linux-install.sh ./linux-install.sh MYSQL_HOST MYSQL_PASSWORD INET REBUILD(false[default]/true) SLAVE(false[default]/true) MYSQL_USER MYSQL_PORT，mysql 必须设置密码吗？
https://github.com/TarsCloud/Tars/issues/799
4、提示下载的Tarsweb没有demo那个目录是怎么回事？
https://github.com/TarsCloud/Tars/issues/800
5、（图）在TARS管理管理后台修改替换模板，是不是之前添加的微服要手动去修改模板文件才会生效？
https://github.com/TarsCloud/Tars/issues/801



20201111

1、(图)这里的客服端报错是什么原因呢？
https://github.com/TarsCloud/Tars/issues/701
2、TARS使用k8s部署遇到的一个问题。
https://github.com/TarsCloud/Tars/issues/596
3、make_pair(TC_Mysql::DB_INT, 100); 100会变成d，这个如何编译？
https://github.com/TarsCloud/Tars/issues/642
4、如果要部署到生产环境，framework 和 tars-node建议用什么方式部署呢？
https://github.com/TarsCloud/Tars/issues/643
5、（图）时间不准，怎样同步成为最新的时间？mysql是通过docker安装的。
https://github.com/TarsCloud/Tars/issues/706



20201112

1、为什么在Tars注册中心中添加一个Servant，数据库可以查到数据但注册中心没有？
https://github.com/TarsCloud/Tars/issues/543
2、为什么centos可以联通 windows的mysql，容器里面启动却连不上？
https://github.com/TarsCloud/Tars/issues/548
3、为什么win下执行脚本不会生产C:\tars\cpp 这个目录？
https://github.com/TarsCloud/Tars/issues/551
4、HelloGo发布后运行不起来是什么原因？
https://github.com/TarsCloud/Tars/issues/552
5、AdminF.tars: 17: error: identifier can't start with 'tars'怎么解决？
https://github.com/TarsCloud/Tars/issues/560




20201113

1、（图）跑 TARS 自带 example 下的 CoroutineDemo 用例 testCoro， 抛出这样的异常，是什么原因？
https://github.com/TarsCloud/Tars/issues/611
2、erp系统，在删除基础部门信息时，要检查是否被其他表引用，有什么比较好的解决方案？
https://github.com/TarsCloud/Tars/issues/647
3、如果要部署到生产环境，framework 和 tars-node建议用什么方式部署呢？
https://github.com/TarsCloud/Tars/issues/643
4、A服务器上部署了Tars环境，如果将一些服务部署在B服务器上，是否需要在B服务器上也部署一套Tars环境？
https://github.com/TarsCloud/Tars/issues/655
5、调用服务的频率快一些服务就会重启，有什么解决办法？
https://github.com/TarsCloud/Tars/issues/689




20201115

1、(图)tars+Yii远程下载，不在exit的话就这种情况。
https://github.com/TarsCloud/Tars/issues/730
2、如果都是TARS协议，可以在A中调用A::async_response_函数，但在http协议中如何做呢？
https://github.com/TarsCloud/Tars/issues/732
3、(图)虚拟机发生了断电，机器重启后TARS的应用服务都启动不了了，运行 /usr/local/app/tars/check.sh，显示这样的内容，这是什么情况？
https://github.com/TarsCloud/Tars/issues/733
4、关于tars http server的三个疑问。
https://github.com/TarsCloud/Tars/issues/736
5、（图）在TARS管理管理后台修改替换模板，是不是之前添加的微服要手动去修改模板文件才会生效？
https://github.com/TarsCloud/Tars/issues/801




20201117

1、TARS 部署在本地，如何规避IP是 DHCP来的会经常变化的问题？
https://github.com/TarsCloud/Tars/issues/589
2、（图）一个关于servant的小问题。直连node节点的时候，服务名字不重要吗？
https://github.com/TarsCloud/Tars/issues/707
3、在对tars支持负载均衡进行测试时遇到问题。
https://github.com/TarsCloud/Tars/issues/587
4、erp系统，在删除基础部门信息时，要检查是否被其他表引用，有什么比较好的解决方案？
https://github.com/TarsCloud/Tars/issues/647
5、java 已经安装配置了，服务提示启动成功， 但是状态还是off，服务的端口未被占用，是因为什么呢？
https://github.com/TarsCloud/Tars/issues/648




20201119

1、运行linux_install.sh安装TARS框架出错，日志输出mysql访问失败？是否是bug？
https://github.com/TarsCloud/Tars/issues/793
2、如何看待RPC框架中的单连接和多连接(或连接池)设计?
https://github.com/TarsCloud/Tars/issues/666
3、proto3协议，java客户端调c服务端，返回结果null，无报错，怀疑解析有问题，是否有开关能在解析异常时打印信息？
https://github.com/TarsCloud/Tars/issues/667
4、在外网安装好了TARS，要搬到内网去，改变了IP，有什么办法让TARS能工作起来？
https://github.com/TarsCloud/Tars/issues/668
5、服务是活的的，obj的端口却没有监听该怎么办？
https://github.com/TarsCloud/Tars/issues/669




20201123

1、提示下载的Tarsweb没有demo那个目录是怎么回事？
https://github.com/TarsCloud/Tars/issues/800
2、（图）在TARS管理管理后台修改替换模板，是不是之前添加的微服要手动去修改模板文件才会生效？
https://github.com/TarsCloud/Tars/issues/801
3、(图)swoole扩展已经安装过了，模板里的php执行路径也改过了，出现如图所示的提示是怎么回事？
https://github.com/TarsCloud/Tars/issues/802
4、(图)这个问题有可能是什么导致的？
https://github.com/TarsCloud/Tars/issues/803
5、(图&日志)这里出现报错是什么原因？
https://github.com/TarsCloud/Tars/issues/804



20201129

1、（图）发生这个错误是怎么回事？TarsWeb是新版的，TarsFramwork是旧版本的，不是docker部署。下载源码之后，执行sql文件夹的sql语句，修改config里面的配置，主要改数据库地址跟registry地址，把enable全部设置成false，最后执行npm run prd。
https://github.com/TarsCloud/Tars/issues/744
2、同一个TARS集群下，TarLog、TasPatch是不是和tarsAdminRegisrty一样，只能有一个实例？
https://github.com/TarsCloud/Tars/issues/747
3、远程登录，不方便用web停止。Tars cpp2.x在节点侧，手工停止服务的脚本命令是什么？
https://github.com/TarsCloud/Tars/issues/748
4、节点正常启动，和framework的docker在同一台机器上，但是后台看不到新加入的节点？这可能是什么原因呢？
https://github.com/TarsCloud/Tars/issues/750
5、(图)框架部署的时候已经启动了TarNode，还需要TarNode部署这一步单独安装TarNode吗？
https://github.com/TarsCloud/Tars/issues/751



20201130

1、请问部署集群的时候，是否可以通过 keepalived + 多台 Tars网关服务器呢？
https://github.com/TarsCloud/Tars/issues/760
2、想开发一个TARS 的UDP服务，该怎么写？
https://github.com/TarsCloud/Tars/issues/683
3、jump_fcontext make_fcontext 的实现代码在哪儿？这两个接口说明在哪儿？
https://github.com/TarsCloud/Tars/issues/762
4、(图)重启后，pm2 list 下面没有 web 和 system，这是什么原因？
https://github.com/TarsCloud/Tars/issues/768
5、（图）这里生产环境部署，服务节点和应用节点可以放在一台服务器上吗？
https://github.com/TarsCloud/Tars/issues/769

20201201

1、脱离TARS环境调用微服务，setLocator怎么传参？
https://github.com/TarsCloud/Tars/issues/649
2、阿里云VPS，有内网IP，自动安装节点成功，节点管理里找不到怎么办？
https://github.com/TarsCloud/Tars/issues/650
3、A服务器上部署了Tars环境，如果将一些服务部署在B服务器上，是否需要在B服务器上也部署一套Tars环境？
https://github.com/TarsCloud/Tars/issues/655
4、新增部署节点时，可以填写我的节点地址吗？我想添加一个有公网ip的服务器地址，要如何操作？
https://github.com/TarsCloud/Tars/issues/656
5、从web控制端 配置了配置文件，go代码里我该怎么获取？
https://github.com/TarsCloud/Tars/issues/653



20201202

1、将配置文件push到服务节点之后。解析的时候，提示找不到文件是什么原因？
https://github.com/TarsCloud/Tars/issues/657
2、TARS用gson替代fastjson而不是用jackson替代主要是出于哪些考虑呢？
https://github.com/TarsCloud/Tars/issues/659
3、在往TARS上部署应用的时候，通过jenkins进行一键部署，我看有上传并发布的接口，但是不会用。
https://github.com/TarsCloud/Tars/issues/662
4、1.7版本后，日志直接用@Slf4j注解，看不到日志了，需要在哪儿配置吗？
https://github.com/TarsCloud/Tars/issues/665
5、如何看待RPC框架中的单连接和多连接(或连接池)设计?
https://github.com/TarsCloud/Tars/issues/666




20201203


1、proto3协议，java客户端调c服务端，返回结果null，无报错，怀疑解析有问题，是否有开关能在解析异常时打印信息？
https://github.com/TarsCloud/Tars/issues/667
2、滚动日志超过配置文件中的个数后会被自动删除吗？
https://github.com/TarsCloud/Tars/issues/673
3、利用docker.sh制作镜像，出现该问题怎么解决？
https://github.com/TarsCloud/Tars/issues/675
4、在部署TARS时 Mysql IP Address 和 INET 怎么找？
https://github.com/TarsCloud/Tars/issues/674
5、如何看系统里面现在使用的TARS框架版本呢？
https://github.com/TarsCloud/Tars/issues/783




20201204


1、（图）这是framwork的3001吗？什么是管理端口？
https://github.com/TarsCloud/Tars/issues/715
2、例如3000这个端口已经改成我自定义的8000端口，但是访问web控制台的时候，第一步index.html中会自动跳转到:3001的端口， 这个3001端口又在哪里改呢？
https://github.com/TarsCloud/Tars/issues/609
3、（图）这个镜像在哪里可以看到对应的TarsFramework代码的版本号？
https://github.com/TarsCloud/Tars/issues/716
4、（图）请问图中-cp后面的目录是怎么加进来的？程序中没有这部分地址要怎么处理呢？
https://github.com/TarsCloud/Tars/issues/717
5、（图）请问HelloPrx和HelloPrxCallback这两个类是自动生成的吗？
https://github.com/TarsCloud/Tars/issues/718




20201209


1、为什么我的日志的记录时间总是比框架和业务节点的时间少8h？
https://git.io/JfjQn
2、有方便的更换web port的方式么？
https://git.io/JfjQ7
3、（图）TARS管理后台超时提示没有操作权限，是因为cookie中uid值变了，不知道为什么从admin 变成一个很长的字符串了，打开管理后台后 ，挂在那里过20多分钟uid值就变化了。
https://git.io/JfjQF
4、跑TARS自带example下的 CoroutineDemo 用例 testCoro， 抛出异常怎么处理？
https://git.io/Jfj7B
5、为什么TARS的生产代码中存在大量的assert？
https://git.io/JfACT




20201214


1、（图）发生这个错误是怎么回事？TarsWeb是新版的，TarsFramwork是旧版本的，不是docker部署。下载源码之后，执行sql文件夹的sql语句，修改config里面的配置，主要改数据库地址跟registry地址，把enable全部设置成false，最后执行npm run prd。
https://github.com/TarsCloud/Tars/issues/744
2、同一个TARS集群下，TarLog、TasPatch是不是和tarsAdminRegisrty一样，只能有一个实例？
https://github.com/TarsCloud/Tars/issues/747
3、远程登录，不方便用web停止。Tars cpp2.x在节点侧，手工停止服务的脚本命令是什么？
https://github.com/TarsCloud/Tars/issues/748
4、节点正常启动，和framework的docker在同一台机器上，但是后台看不到新加入的节点？这可能是什么原因呢？
https://github.com/TarsCloud/Tars/issues/750
5、(图)框架部署的时候已经启动了TarNode，还需要TarNode部署这一步单独安装TarNode吗？
https://github.com/TarsCloud/Tars/issues/751



20210106

1、（图）在调用服务接口的时候，客户端日志提示链接被关闭，但是服务端是收到了这个请求的，请问是什么原因呢？
https://github.com/TarsCloud/Tars/issues/711
2、（图）请问这个是添加在模板配置文件里吗？
https://github.com/TarsCloud/Tars/issues/712
3、请问java中定义xxx.tars文件时，有时间类型吗？
https://github.com/TarsCloud/Tars/issues/713
4、（图）服务监控不能用，报错提示是查询602，我使用windows的docker里没有对应的localtime文件，是因为没有导入时区吗？
https://github.com/TarsCloud/Tars/issues/714
5、mac自动化部署是实际安装过程中好像没法自动安装node npm。
https://github.com/TarsCloud/Tars/issues/549



20210118
1、一个节点只能发布一个服务吗？我的服务是tar.gz，用tars发布完后我的安装包会解压到哪里？.
https://github.com/TarsCloud/Tars/issues/615
2、TarsGo如何实现HTTPS？
https://github.com/TarsCloud/Tars/issues/786
3、Tars cpp2.x在节点侧，手工停止服务的脚本命令有吗？
https://github.com/TarsCloud/Tars/issues/788
4、TarsGateway使用安装脚本install.sh安装过程显示成功，最后的测试结果显示失败？
https://github.com/TarsCloud/Tars/issues/789
5、（图）防火墙已关闭，配置文件也配置好了，出现如备注图1所示的问题如何解决？CPU使用情况见图
https://github.com/TarsCloud/Tars/issues/600



20210119


1、请问部署集群的时候，是否可以通过 keepalived + 多台 Tars网关服务器呢？
https://github.com/TarsCloud/Tars/issues/760
2、如果节点分布在各个实体机器上，在实际的使用过程中可能遇到一些问题，例如，一个节点上部署了多个服务，可能有java、php、go一旦其中一个服务出现异常，比如把cpu占用完了，那么其它服务也会受牵连，有什么办法可以避免这种情况吗？
https://github.com/TarsCloud/Tars/issues/761
3、重启后，pm2 list 下面没有 web 和 system，这是什么原因？
https://github.com/TarsCloud/Tars/issues/768
4、外网机器迁移了，目标obj的ip和端口修改了，这时候Tars stringToProxy 会即刻拿到新的地址吗？这块是怎么同步的？
https://github.com/TarsCloud/Tars/issues/778
5、TARS在k8s已创建好，怎么做才能让外网访问里面的服务？
https://github.com/TarsCloud/Tars/issues/779



20210120

1、运行linux_install.sh安装TARS框架出错，日志输出mysql访问失败？是否是bug？使用的是MariaDB10.4.13
https://github.com/TarsCloud/Tars/issues/793
2、restart server, stop error:EM_TARS_UNKNOWN_ERR 这种错误怎么排查问题？
https://github.com/TarsCloud/Tars/issues/798
3、chmod a+x linux-install.sh./linux-install.sh MYSQL_HOST MYSQL_PASSWORD INET REBUILD(false[default]/true) SLAVE(false[default]/true) MYSQL_USER MYSQL_PORT，mysql 必须设置密码吗？
https://github.com/TarsCloud/Tars/issues/799
4、如图这个问题有可能是什么导致的？
https://github.com/TarsCloud/Tars/issues/803
5、（图）swoole扩展已经安装过了,，模板里的php执行路径也改过了，出现如图2所示的提示是怎么回事？
https://github.com/TarsCloud/Tars/issues/802



20210121

1、TARS_CLIENT_LOGGER 可以在哪里配置关闭吗？
https://github.com/TarsCloud/Tars/issues/795
2、如图所示，框架部署的时候已经启动了TarNode,还需要TarNode部署这一步单独安装TarNode吗？
https://github.com/TarsCloud/Tars/issues/751
3、节点正常启动，和framework的docker在同一台机器上，但是后台看不到新加入的节点？这可能是什么原因呢？
https://github.com/TarsCloud/Tars/issues/750
4、同一个TARS集群下，TarLog、TasPatch是不是和tarsAdminRegisrty一样，只能有一个实例？
https://github.com/TarsCloud/Tars/issues/747
5、一个疑问：我有个服务A，线上环境，我有多个地区部署，配置不一样，这块是怎么搞呢？
https://github.com/TarsCloud/Tars/issues/741




20210123

1、TARS 部署在本地，如何规避IP是 DHCP来的会经常变化的问题？
https://github.com/TarsCloud/Tars/issues/589
2、（图）一个关于servant的小问题。直连node节点的时候，服务名字不重要吗？
https://github.com/TarsCloud/Tars/issues/707
3、在对TARS支持负载均衡进行测试时遇到问题。
https://github.com/TarsCloud/Tars/issues/587
4、erp系统，在删除基础部门信息时，要检查是否被其他表引用，有什么比较好的解决方案？
https://github.com/TarsCloud/Tars/issues/647
5、java 已经安装配置了，服务提示启动成功， 但是状态还是off，服务的端口未被占用，是因为什么呢？
https://github.com/TarsCloud/Tars/issues/648



20210124

1、proto3协议，java客户端调c服务端，返回结果null，无报错，怀疑解析有问题，是否有开关能在解析异常时打印信息？
https://github.com/TarsCloud/Tars/issues/667
2、滚动日志超过配置文件中的个数后会被自动删除吗？
https://github.com/TarsCloud/Tars/issues/673
3、利用docker.sh制作镜像，出现该问题怎么解决？
https://github.com/TarsCloud/Tars/issues/675
4、在部署TARS时 Mysql IP Address 和 INET 怎么找？
https://github.com/TarsCloud/Tars/issues/674
5、如何看系统里面现在使用的TARS框架版本呢？
https://github.com/TarsCloud/Tars/issues/783




20210125

1、一个节点只能发布一个服务吗？我的服务是tar.gz，用TARS发布完后我的安装包会解压到哪里？
https://github.com/TarsCloud/Tars/issues/615
2、（图）HelloGo 入门，这里的执行文件在前面代码里没有指定 ip和端口，有什么用？
https://github.com/TarsCloud/Tars/issues/782
3、如备注图所示的两个锁会被锁住吗？
https://github.com/TarsCloud/Tars/issues/542
4、（图）防火墙已关闭，配置文件也配置好了，出现如图所示的问题如何解决？
https://github.com/TarsCloud/Tars/issues/600
5、通过docker方式本地安装了 framework 和 node 各一台，现在通过web控制台启动一个spring boot 项目，无法顺利启动。但是我登入容器后，启动这个jar，可以正常工作，这个问题该怎么处理？
https://github.com/TarsCloud/Tars/issues/601



20210126

1、脱离TARS环境调用微服务，setLocator怎么传参？
https://github.com/TarsCloud/Tars/issues/649
2、阿里云VPS，有内网IP，自动安装节点成功，节点管理里找不到怎么办？
https://github.com/TarsCloud/Tars/issues/650
3、A服务器上部署了Tars环境，如果将一些服务部署在B服务器上，是否需要在B服务器上也部署一套Tars环境？
https://github.com/TarsCloud/Tars/issues/655
4、新增部署节点时，可以填写我的节点地址吗？我想添加一个有公网ip的服务器地址，要如何操作？
https://github.com/TarsCloud/Tars/issues/656
5、从web控制端 配置了配置文件，go代码里我该怎么获取？
https://github.com/TarsCloud/Tars/issues/653




20210127

1、脱离TARS环境调用微服务，setLocator怎么传参？
https://github.com/TarsCloud/Tars/issues/649
2、阿里云VPS，有内网IP，自动安装节点成功，节点管理里找不到怎么办？
https://github.com/TarsCloud/Tars/issues/650
3、A服务器上部署了Tars环境，如果将一些服务部署在B服务器上，是否需要在B服务器上也部署一套Tars环境？
https://github.com/TarsCloud/Tars/issues/655
4、新增部署节点时，可以填写我的节点地址吗？我想添加一个有公网ip的服务器地址，要如何操作？
https://github.com/TarsCloud/Tars/issues/656
5、从web控制端 配置了配置文件，go代码里我该怎么获取？
https://github.com/TarsCloud/Tars/issues/653




20210129


1、提示 “bash: cd: C:\Users\Admin\go/src/github.com/TarsCloud/TarsGo/tars/tools/tars2go: No such file or directory” 是什么原因呢？
https://github.com/TarsCloud/Tars/issues/694
2、（图）主控和节点目前在一台机器，部署的服务启动不了，我看了下启动脚本，执行路径里多一个空格有可能是什么原因？
https://github.com/TarsCloud/Tars/issues/695
3、（图）标注处所示windows挂载不上去，因为没有这个目录，这是什么原因呢？
https://github.com/TarsCloud/Tars/issues/696
4、（图）图中的错误是跟什么插件版本有关？
https://github.com/TarsCloud/Tars/issues/697
5、（图）第一次登录，在修改密码时提示“系统内部错误”，检查日志提示表只读，可能是什么原因呢？
https://github.com/TarsCloud/Tars/issues/698




20210130

1、（图）这是framwork的3001吗？什么是管理端口？
https://github.com/TarsCloud/Tars/issues/715
2、例如3000这个端口已经改成我自定义的8000端口，但是访问web控制台的时候，第一步index.html中会自动跳转到:3001的端口， 这个3001端口又在哪里改呢？
https://github.com/TarsCloud/Tars/issues/609
3、（图）这个镜像在哪里可以看到对应的TarsFramework代码的版本号？
https://github.com/TarsCloud/Tars/issues/716
4、（图）请问图中-cp后面的目录是怎么加进来的？程序中没有这部分地址要怎么处理呢？
https://github.com/TarsCloud/Tars/issues/717
5、（图）请问HelloPrx和HelloPrxCallback这两个类是自动生成的吗？
https://github.com/TarsCloud/Tars/issues/718



20210131

1、测试环境如何保证日志的级别是debug级别的呢？
https://github.com/TarsCloud/Tars/issues/723
2、（图）提示这个错误，是什么原因导致的？
https://github.com/TarsCloud/Tars/issues/724
3、TARS的服务，要使用自己的自定义的配置文档怎么操作？
https://github.com/TarsCloud/Tars/issues/725
4、（图）我添加新节点之后为什么节点列表里没有显示新节点呢？
https://github.com/TarsCloud/Tars/issues/726
5、ResponsePacket 和 RequestPacket 里都有context 和 status ，这两个值有什么区别？
https://github.com/TarsCloud/Tars/issues/729

20210201

1、(图)tars+Yii远程下载，不在exit的话就这种情况。
https://github.com/TarsCloud/Tars/issues/730
2、如果都是TARS协议，可以在A中调用A::async_response_函数，但在http协议中如何做呢？
https://github.com/TarsCloud/Tars/issues/732
3、(图)虚拟机发生了断电，机器重启后TARS的应用服务都启动不了了，运行 /usr/local/app/tars/check.sh，显示这样的内容，这是什么情况？
https://github.com/TarsCloud/Tars/issues/733
4、关于tars http server的三个疑问。
https://github.com/TarsCloud/Tars/issues/736
5、TARS有没有对连接做保活？
https://github.com/TarsCloud/Tars/issues/737




20210202

1、(图)tars+Yii远程下载，不在exit的话就这种情况。
https://github.com/TarsCloud/Tars/issues/730
2、如果都是TARS协议，可以在A中调用A::async_response_函数，但在http协议中如何做呢？
https://github.com/TarsCloud/Tars/issues/732
3、(图)虚拟机发生了断电，机器重启后TARS的应用服务都启动不了了，运行 /usr/local/app/tars/check.sh，显示这样的内容，这是什么情况？
https://github.com/TarsCloud/Tars/issues/733
4、关于tars http server的三个疑问。
https://github.com/TarsCloud/Tars/issues/736
5、TARS有没有对连接做保活？
https://github.com/TarsCloud/Tars/issues/737



20210203

1、 首次搭建TARS，搭建环境，cmake时报错怎么处理？
https://github.com/TarsCloud/Tars/issues/791
2、重启后 pm2 list 下面没有 web 和 system 是什么原因？
https://github.com/TarsCloud/Tars/issues/792
3、运行linux_install.sh安装TARS框架出错，日志输出mysql访问失败？是否是bug？
https://github.com/TarsCloud/Tars/issues/793 
4、为什么服务打包发布后后无法运行？报错“down，server is inactive”.
https://github.com/TarsCloud/Tars/issues/579 
5、TARS_CLIENT_LOGGER 可以在哪里配置关闭吗？
https://github.com/TarsCloud/Tars/issues/795




20210204

1、这两个TARS服务间调用时报的错误。
https://github.com/TarsCloud/Tars/issues/710
2、（图）文档上说的cmake_tars_server.sh是自动生成cmakelists.txt的工具。TarsCpp库已经正常编译安装，并生成tar协议了，但我运行后没生成cmakelist.txt。
https://github.com/TarsCloud/Tars/issues/691
3、（图）在调用服务接口的时候，出现错误，客户端日志说是链接被关闭了，但是服务端是收到了这个请求的，请问是怎么回事呢？
https://github.com/TarsCloud/Tars/issues/711
4、（图）如备注图所示这种报错的原因是什么？
https://github.com/TarsCloud/Tars/issues/692
5、全局开启了go mod，执行备注图1的划线处提示bash: cd: C:\Users\Admin\go/src/github.com/TarsCloud/TarsGo/tars/tools/tars2go: No such file or directory，全局开启go mod是什么原因呢？
https://github.com/TarsCloud/Tars/issues/694



20210207
1、业务初始化接口里是不是不能进行耗时操作？
https://github.com/TarsCloud/Tars/issues/637
2、tc_mysql的库是线程安全的吗？
https://github.com/TarsCloud/Tars/issues/575
3、如图所示的数据库问题，为什么显示没有数据？
https://github.com/TarsCloud/Tars/issues/580
4、如备注如图所示，提示tars not find adapter 是什么原因？
https://github.com/TarsCloud/Tars/issues/658
5、（图）请问这个是添加在模板配置文件里吗？
https://github.com/TarsCloud/Tars/issues/712



20210208

1、（图）如果我把IP地址和端口号改为自己的地址和端口号，直接编译运行这段代码就可以吗？
https://github.com/TarsCloud/Tars/issues/562
2、想用单项调用提高效率，是用tcp还是udp？
https://github.com/TarsCloud/Tars/issues/565
3、java里的stringToProxy，会触发ConCurrentHashMap computeIfAbsent的死循环bug。(见备注例子，jdk版本为1.8)
https://github.com/TarsCloud/Tars/issues/566
4、P90，P99和P999的汇总思路是什么？
https://github.com/TarsCloud/Tars/issues/582
5、微服务通过linux控制台直接关闭和启动，那么框架里面的web管理后台的控制会失效吗？
https://github.com/TarsCloud/Tars/issues/591



20210210
1、web过来的请求直接到node的话，都有哪些功能操作？
https://github.com/TarsCloud/Tars/issues/622
2、日志打到TarsLog里后，在web页面上可以流式查看检索吗？
https://github.com/TarsCloud/Tars/issues/623
3、(图)这两个类是自动生成的？需要执行怎样的命令？或者文档在哪里？
https://github.com/TarsCloud/Tars/issues/718
4、(图)这里的负载是什么意思呢？为什么有的显示0，有的显示1， 安装的时候没有做出相关的选择。
https://github.com/TarsCloud/Tars/issues/780
5、docker镜像没有java环境吗？跑java微服需要自己进docker环境中配置java吗？
https://github.com/TarsCloud/Tars/issues/624




20210211

1、使用docker创建虚拟网络，TarsFramework中使用宿主的ip，但发布时会报错 。
https://github.com/TarsCloud/Tars/issues/627
2、MDC支持跨服务链路追踪吗？
https://github.com/TarsCloud/Tars/issues/590
3、1.7.1的tars-Java是不是不支持染色日志？
https://github.com/TarsCloud/Tars/issues/629
4、请问TARS有分布式事务解决方案吗？
https://github.com/TarsCloud/Tars/issues/631
5、TARS框架部署时，每个组件需要的硬件要求是怎么样的？
https://github.com/TarsCloud/Tars/issues/594



20210213

1、这两个TARS服务间调用时报的错误。
https://github.com/TarsCloud/Tars/issues/710
2、（图）文档上说的cmake_tars_server.sh是自动生成cmakelists.txt的工具。TarsCpp库已经正常编译安装，并生成tar协议了，但我运行后没生成cmakelist.txt。
https://github.com/TarsCloud/Tars/issues/691
3、（图）在调用服务接口的时候，出现错误，客户端日志说是链接被关闭了，但是服务端是收到了这个请求的，请问是怎么回事呢？
https://github.com/TarsCloud/Tars/issues/711
4、（图）如备注图所示这种报错的原因是什么？
https://github.com/TarsCloud/Tars/issues/692
5、全局开启了go mod，执行备注图1的划线处提示bash: cd: C:\Users\Admin\go/src/github.com/TarsCloud/TarsGo/tars/tools/tars2go: No such file or directory，全局开启go mod是什么原因呢？
https://github.com/TarsCloud/Tars/issues/694




20210217

1、（图）使用1核2GB的腾讯云在编译TARS源码的时候报错，请问如何解决？
https://github.com/TarsCloud/Tars/issues/705
2、（图）时间不准，怎样同步成为最新的时间？mysql是通过docker安装的。
https://github.com/TarsCloud/Tars/issues/706
3、（图）一个关于servant的小问题。直连node节点的时候，服务名字不重要吗？
https://github.com/TarsCloud/Tars/issues/707
4、（图）文件解析有问题，是目前pom.xml的依赖不全吗？我还需要导入别的依赖吗？
https://github.com/TarsCloud/Tars/issues/708
5、在服务部署时，线程数、最大连接数、队列最大长度等修改后会立即生效吗？
https://github.com/TarsCloud/Tars/issues/709




20210220

1、1.7版本后，日志直接用@Slf4j注解，看不到日志了，需要在哪儿配置吗？
https://github.com/TarsCloud/Tars/issues/665
2、如何看待RPC框架中的单连接和多连接(或连接池)设计?
https://github.com/TarsCloud/Tars/issues/666
3、proto3协议，java客户端调c服务端，返回结果null，无报错，怀疑解析有问题，是否有开关能在解析异常时打印信息？
https://github.com/TarsCloud/Tars/issues/667
4、在外网安装好了TARS，要搬到内网去，改变了IP，有什么办法让TARS能工作起来？
https://github.com/TarsCloud/Tars/issues/668
5、服务是活的的，obj的端口却没有监听该怎么办？
https://github.com/TarsCloud/Tars/issues/669



20210222


1、提示下载的Tarsweb没有demo那个目录是怎么回事？
https://github.com/TarsCloud/Tars/issues/800
2、（图）在TARS管理管理后台修改替换模板，是不是之前添加的微服要手动去修改模板文件才会生效？
https://github.com/TarsCloud/Tars/issues/801
3、(图)swoole扩展已经安装过了，模板里的php执行路径也改过了，出现如图所示的提示是怎么回事？
https://github.com/TarsCloud/Tars/issues/802
4、(图)这个问题有可能是什么导致的？
https://github.com/TarsCloud/Tars/issues/803
5、(图&日志)这里出现报错是什么原因？
https://github.com/TarsCloud/Tars/issues/804




20210224

1、为什么TarsGateway会依赖到tars_user_system？
https://github.com/TarsCloud/Tars/issues/738
2、低版本1.5.0 客户端里框架会有一个tars_client.log 日志，这个日志文件是一定打出来么？
https://github.com/TarsCloud/Tars/issues/739
3、gateway在内部是什么场景？
https://github.com/TarsCloud/Tars/issues/740
4、一个疑问：我有个服务A，线上环境，我有多个地区部署，配置不一样，这块是怎么搞呢？
https://github.com/TarsCloud/Tars/issues/741
5、(图)安装TarsGateway时这个token获取不到怎么办？
https://github.com/TarsCloud/Tars/issues/742


20210228


1、打包发布后想要修改代码，是在本地修改然后再压缩上传吗？
https://github.com/TarsCloud/Tars/issues/676
2、在make docker 的时候，输入.\ docker.sh v1 ，提示commond not found是什么原因？
https://github.com/TarsCloud/Tars/issues/677
3、想开发一个TARS 的UDP服务，该怎么写？
https://github.com/TarsCloud/Tars/issues/683
4、（图）无法连接mysql问题怎么解决？mysql都重装了，命令行可以连上。
https://github.com/TarsCloud/Tars/issues/685
5、（图）这是少配置了什么导致错误？
https://github.com/TarsCloud/Tars/issues/686




20210301

1、tars app_log目录占分区空间较大，怎么安全迁移到/data分区呢？
https://github.com/TarsCloud/Tars/issues/687
2、调用服务的频率快一些服务就会重启，有什么解决办法？
https://github.com/TarsCloud/Tars/issues/689
3、TARS的服务，要使用自己的自定义的配置文档怎么操作？
https://github.com/TarsCloud/Tars/issues/725
4、（图）我添加新节点之后为什么节点列表里没有显示新节点呢？
https://github.com/TarsCloud/Tars/issues/726
5、使用go语言，TARS如何实现https？
https://github.com/TarsCloud/Tars/issues/727




20210302


1、为什么在使用TARS时，logback.xml文件获取不到？
https://github.com/TarsCloud/Tars/issues/621
2、请问web过来的请求直接到node的话，都有哪些功能操作？
https://github.com/TarsCloud/Tars/issues/622
3、日志打到TarsLog里后，在web页面上可以流式查看检索吗？
https://github.com/TarsCloud/Tars/issues/623
4、docker镜像没有java环境吗？跑java微服需要自己进docker环境中配置java吗？
https://github.com/TarsCloud/Tars/issues/624
5、如何在TarsCPP中创建自己定义tcp连接外部服务器(非TAF)？
https://github.com/TarsCloud/Tars/issues/626




20210303

1、使用docker创建虚拟网络，TarsFramework中使用宿主的ip，但发布时会报错 。
https://github.com/TarsCloud/Tars/issues/627
2、MDC支持跨服务链路追踪吗？
https://github.com/TarsCloud/Tars/issues/590
3、1.7.1的tars-Java是不是不支持染色日志？
https://github.com/TarsCloud/Tars/issues/629
4、请问TARS有分布式事务解决方案吗？
https://github.com/TarsCloud/Tars/issues/631
5、TARS框架部署时，每个组件需要的硬件要求是怎么样的？
https://github.com/TarsCloud/Tars/issues/594



20210304

1、这两个TARS服务间调用时报的错误。
https://github.com/TarsCloud/Tars/issues/710
2、（图）文档上说的cmake_tars_server.sh是自动生成cmakelists.txt的工具。TarsCpp库已经正常编译安装，并生成tar协议了，但我运行后没生成cmakelist.txt。
https://github.com/TarsCloud/Tars/issues/691
3、（图）在调用服务接口的时候，出现错误，客户端日志说是链接被关闭了，但是服务端是收到了这个请求的，请问是怎么回事呢？
https://github.com/TarsCloud/Tars/issues/711
4、（图）如备注图所示这种报错的原因是什么？
https://github.com/TarsCloud/Tars/issues/692
5、全局开启了go mod，执行备注图1的划线处提示bash: cd: C:\Users\Admin\go/src/github.com/TarsCloud/TarsGo/tars/tools/tars2go: No such file or directory，全局开启go mod是什么原因呢？
https://github.com/TarsCloud/Tars/issues/694




20210308

1、运行linux_install.sh安装TARS框架出错，日志输出mysql访问失败？是否是bug？使用的是MariaDB10.4.13
https://github.com/TarsCloud/Tars/issues/793
2、restart server, stop error:EM_TARS_UNKNOWN_ERR 这种错误怎么排查问题？
https://github.com/TarsCloud/Tars/issues/798
3、chmod a+x linux-install.sh./linux-install.sh MYSQL_HOST MYSQL_PASSWORD INET REBUILD(false[default]/true) SLAVE(false[default]/true) MYSQL_USER MYSQL_PORT，mysql 必须设置密码吗？
https://github.com/TarsCloud/Tars/issues/799
4、如图这个问题有可能是什么导致的？
https://github.com/TarsCloud/Tars/issues/803
5、（图）swoole扩展已经安装过了,，模板里的php执行路径也改过了，出现如图2所示的提示是怎么回事？
https://github.com/TarsCloud/Tars/issues/802


20210309

1、TARS_CLIENT_LOGGER 可以在哪里配置关闭吗？
https://github.com/TarsCloud/Tars/issues/795
2、如图所示，框架部署的时候已经启动了TarNode,还需要TarNode部署这一步单独安装TarNode吗？
https://github.com/TarsCloud/Tars/issues/751
3、节点正常启动，和framework的docker在同一台机器上，但是后台看不到新加入的节点？这可能是什么原因呢？
https://github.com/TarsCloud/Tars/issues/750
4、同一个TARS集群下，TarLog、TasPatch是不是和tarsAdminRegisrty一样，只能有一个实例？
https://github.com/TarsCloud/Tars/issues/747
5、一个疑问：我有个服务A，线上环境，我有多个地区部署，配置不一样，这块是怎么搞呢？
https://github.com/TarsCloud/Tars/issues/741


20210310

1、TARS 部署在本地，如何规避IP是 DHCP来的会经常变化的问题？
https://github.com/TarsCloud/Tars/issues/589
2、（图）一个关于servant的小问题。直连node节点的时候，服务名字不重要吗？
https://github.com/TarsCloud/Tars/issues/707
3、在对TARS支持负载均衡进行测试时遇到问题。
https://github.com/TarsCloud/Tars/issues/587
4、erp系统，在删除基础部门信息时，要检查是否被其他表引用，有什么比较好的解决方案？
https://github.com/TarsCloud/Tars/issues/647
5、java 已经安装配置了，服务提示启动成功， 但是状态还是off，服务的端口未被占用，是因为什么呢？
https://github.com/TarsCloud/Tars/issues/648


20210312

1、脱离TARS环境调用微服务，setLocator怎么传参？
https://github.com/TarsCloud/Tars/issues/649
2、阿里云VPS，有内网IP，自动安装节点成功，节点管理里找不到怎么办？
https://github.com/TarsCloud/Tars/issues/650
3、A服务器上部署了Tars环境，如果将一些服务部署在B服务器上，是否需要在B服务器上也部署一套Tars环境？
https://github.com/TarsCloud/Tars/issues/655
4、新增部署节点时，可以填写我的节点地址吗？我想添加一个有公网ip的服务器地址，要如何操作？
https://github.com/TarsCloud/Tars/issues/656
5、从web控制端 配置了配置文件，go代码里我该怎么获取？
https://github.com/TarsCloud/Tars/issues/653

20210313

1、(图)这里的客服端报错是什么原因呢？
https://github.com/TarsCloud/Tars/issues/701
2、TARS使用k8s部署遇到的一个问题。
https://github.com/TarsCloud/Tars/issues/596
3、make_pair(TC_Mysql::DB_INT, 100); 100会变成d，这个如何编译？
https://github.com/TarsCloud/Tars/issues/642
4、如果要部署到生产环境，framework 和 tars-node建议用什么方式部署呢？
https://github.com/TarsCloud/Tars/issues/643
5、（图）时间不准，怎样同步成为最新的时间？mysql是通过docker安装的。
https://github.com/TarsCloud/Tars/issues/706


20210315

1、提示 “bash: cd: C:\Users\Admin\go/src/github.com/TarsCloud/TarsGo/tars/tools/tars2go: No such file or directory” 是什么原因呢？
https://github.com/TarsCloud/Tars/issues/694
2、（图）主控和节点目前在一台机器，部署的服务启动不了，我看了下启动脚本，执行路径里多一个空格有可能是什么原因？
https://github.com/TarsCloud/Tars/issues/695
3、（图）标注处所示windows挂载不上去，因为没有这个目录，这是什么原因呢？
https://github.com/TarsCloud/Tars/issues/696
4、（图）图中的错误是跟什么插件版本有关？
https://github.com/TarsCloud/Tars/issues/697
5、（图）第一次登录，在修改密码时提示“系统内部错误”，检查日志提示表只读，可能是什么原因呢？
https://github.com/TarsCloud/Tars/issues/698

20210316


1、（图）这是framwork的3001吗？什么是管理端口？
https://github.com/TarsCloud/Tars/issues/715
2、例如3000这个端口已经改成我自定义的8000端口，但是访问web控制台的时候，第一步index.html中会自动跳转到:3001的端口， 这个3001端口又在哪里改呢？
https://github.com/TarsCloud/Tars/issues/609
3、（图）这个镜像在哪里可以看到对应的TarsFramework代码的版本号？
https://github.com/TarsCloud/Tars/issues/716
4、（图）请问图中-cp后面的目录是怎么加进来的？程序中没有这部分地址要怎么处理呢？
https://github.com/TarsCloud/Tars/issues/717
5、（图）请问HelloPrx和HelloPrxCallback这两个类是自动生成的吗？
https://github.com/TarsCloud/Tars/issues/718


20210317

1、（图）这是framwork的3001吗？什么是管理端口？
https://github.com/TarsCloud/Tars/issues/715
2、例如3000这个端口已经改成我自定义的8000端口，但是访问web控制台的时候，第一步index.html中会自动跳转到:3001的端口， 这个3001端口又在哪里改呢？
https://github.com/TarsCloud/Tars/issues/609
3、（图）这个镜像在哪里可以看到对应的TarsFramework代码的版本号？
https://github.com/TarsCloud/Tars/issues/716
4、（图）请问图中-cp后面的目录是怎么加进来的？程序中没有这部分地址要怎么处理呢？
https://github.com/TarsCloud/Tars/issues/717
5、（图）请问HelloPrx和HelloPrxCallback这两个类是自动生成的吗？
https://github.com/TarsCloud/Tars/issues/718

20210319

1、(图)tars+Yii远程下载，不在exit的话就这种情况。
https://github.com/TarsCloud/Tars/issues/730
2、如果都是TARS协议，可以在A中调用A::async_response_函数，但在http协议中如何做呢？
https://github.com/TarsCloud/Tars/issues/732
3、(图)虚拟机发生了断电，机器重启后TARS的应用服务都启动不了了，运行 /usr/local/app/tars/check.sh，显示这样的内容，这是什么情况？
https://github.com/TarsCloud/Tars/issues/733
4、关于tars http server的三个疑问。
https://github.com/TarsCloud/Tars/issues/736
5、TARS有没有对连接做保活？
https://github.com/TarsCloud/Tars/issues/737


20210320
1、TARS有分布式事务解决方案吗？
https://github.com/TarsCloud/Tars/issues/631
2、TarsNode和framework docker都安装在一台宿主机上，会有什么冲突吗？
https://github.com/TarsCloud/Tars/issues/632
3、本地开发服务端总报错但是客户端可正常调用是什么原因？
https://github.com/TarsCloud/Tars/issues/633
4、（图）cache在哪里会更新？
https://github.com/TarsCloud/Tars/issues/634
5、代码管理与代码编译是怎样在TARS中使用的？
https://github.com/TarsCloud/Tars/issues/635

20210321

1、 首次搭建TARS，搭建环境，cmake时报错怎么处理？
https://github.com/TarsCloud/Tars/issues/791
2、重启后 pm2 list 下面没有 web 和 system 是什么原因？
https://github.com/TarsCloud/Tars/issues/792
3、运行linux_install.sh安装TARS框架出错，日志输出mysql访问失败？是否是bug？
https://github.com/TarsCloud/Tars/issues/793 
4、为什么服务打包发布后后无法运行？报错“down，server is inactive”.
https://github.com/TarsCloud/Tars/issues/579 
5、TARS_CLIENT_LOGGER 可以在哪里配置关闭吗？
https://github.com/TarsCloud/Tars/issues/795

20210322

1、这两个TARS服务间调用时报的错误。
https://github.com/TarsCloud/Tars/issues/710
2、（图）文档上说的cmake_tars_server.sh是自动生成cmakelists.txt的工具。TarsCpp库已经正常编译安装，并生成tar协议了，但我运行后没生成cmakelist.txt。
https://github.com/TarsCloud/Tars/issues/691
3、（图）在调用服务接口的时候，出现错误，客户端日志说是链接被关闭了，但是服务端是收到了这个请求的，请问是怎么回事呢？
https://github.com/TarsCloud/Tars/issues/711
4、（图）如备注图所示这种报错的原因是什么？
https://github.com/TarsCloud/Tars/issues/692
5、全局开启了go mod，执行备注图1的划线处提示bash: cd: C:\Users\Admin\go/src/github.com/TarsCloud/TarsGo/tars/tools/tars2go: No such file or directory，全局开启go mod是什么原因呢？
https://github.com/TarsCloud/Tars/issues/694


20210323

1、业务初始化接口里是不是不能进行耗时操作？
https://github.com/TarsCloud/Tars/issues/637
2、tc_mysql的库是线程安全的吗？
https://github.com/TarsCloud/Tars/issues/575
3、如图所示的数据库问题，为什么显示没有数据？
https://github.com/TarsCloud/Tars/issues/580
4、如备注如图所示，提示tars not find adapter 是什么原因？
https://github.com/TarsCloud/Tars/issues/658
5、（图）请问这个是添加在模板配置文件里吗？
https://github.com/TarsCloud/Tars/issues/712

















