# Tars的cookie功能

## 概念说明

tars cookie是在服务调用链上传播的信息，业务可以在调用链的上游设置cookie，并在调用链的下游获取cookie

## 使用场景

### 透传命令字优先级

接入Svr在cookie中设置请求命令字id和请求命令字优先级，透传到后端，后端可以根据这些信息定制自己的过载保护策略（譬如优先拒绝低优先级请求）

### 透传msgno和uid

接入Svr在cookie中设置消息序列号(msgno)和用户id(uid), 后端服务可以取出cookie，并将msgno和uid打印在日志头中（这一步可以通过封装日志宏来自动处理）；
这样定位问题时，可以通过uid或者msgno来快速查找日志（譬如A调用B的场景，看到A的错误日志，则可以复制出msgno，前往B服务快捷查找到该请求的日志）；
业务也可以把日志上报到elk等检索平台，便于通过msgno查找到所有日志

## 和染色关系
1. cookie和染色类似，都可以在调用链上传播
2. 染色从概念上来说，一般只针对少量请求；而cookie针对全量请求
3. 染色会打印一份集中染色日志，而cookie不会
4. cookie和染色可以同时使用，既针对全量请求设置cookie，针对小部分需要特殊处理的请求进行染色（并且如果日志中有打印cookie中msgno和uid的话，在集中染色日志查找一个请求的完整日志也会很方便）
5. 使用全量请求染色也可以实现类似cookie的功能，但是这样相当于丢失了染色功能，只有cookie功能，而且此时磁盘I/O和使用容量会翻倍
6. 设置/读取cookie的函数使用map参数，方便业务定制各种信息，使用起来更加友好

## 使用示例
### 设置cookie
```text
#include "servant/Cookie.h"
CookieOp cookieOp;
map<string, string> cookie;
cookie["msgno"] = "12345";
cookie["uid"] = "67890";
cookieOp.setCookie(cookie);
```
### 获取cookie
#### 从线程私有数据中获取cookie
```text
#include "servant/Cookie.h"
map<string, string> & cookie = CookieOp::getCookie();
TLOGDEBUG("cookie:" << TC_Common::tostr(cookie.begin(), cookie.end()) << endl);
TLOGDEBUG("msgno:" << cookie["msgno"] << endl);
```
#### 从current对象中获取cookie
```text
map<string, string> & cookie = current->getCookie();
TLOGDEBUG("current cookie:" << TC_Common::tostr(cookie.begin(), cookie.end()) << endl);
TLOGDEBUG("msgno:" << cookie["msgno"] << endl);
```


## 具体应用
### 接入服务设置cookie
接入服务在RPC调用之前，在cookie中设置msgno和uid
```text
#include "servant/Cookie.h"

CookieOp cookieOp;
map<string, string> cookie;
cookie["msgno"] = genMsgNo();
cookie["uid"] = uid;
cookieOp.setCookie(cookie);
UserInfoServantPrx->async_getUserInfo(cb, ...); 
```


### 后端服务打印日志
后端服务使用如下定制的宏来打印日志，日志头会自动带上msgno和uid
```text
LOG_ERROR("call DBProxy.getUserInfo failed, ret:" << ret);
```

LOG_ERROR宏定义如下
```text
#define LOG_ERROR(msg) \
    TLOGERROR(__FILENAME__ << ":" << __LINE__ << ":" << __FUNCTION__ << "|" << CookieOp::getCookie()["msgno"] << "|" << CookieOp::getCookie()["uid"] << "|" << msg << endl);
```


### 查看日志
查看每个后端服务的日志，可以看到每条日志上都带上了msgno和uid, 同一个请求在所有服务中的日志，msgno和uid相同，其中af4686091585191682152580023和10001分别为msgno和uid
#### UserInfoSvr的日志
```text
2020-03-26 11:01:22|24209|DEBUG|UserInfoServantImp.cpp:1019:getUserInfo|af4686091585191682152580023|10001|call DBProxy.getUserInfo failed, ret:-1
```

#### DBProxySvr的日志
```text
2020-03-26 11:01:22|12359|DEBUG|DBProxyServantImp.cpp:720:getUserInfo|af4686091585191682152580023|10001|sql is: select * from t_user wehre uid = '10001'
2020-03-26 11:01:22|12359|ERROR|DBProxyServantImp.cpp:723:getUserInfo|af4686091585191682152580023|10001|access db timeout
```

