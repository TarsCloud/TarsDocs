# Tars 的 cookie 功能

## 概念说明

tars cookie 是在服务调用链上传播的信息，业务可以在调用链的上游设置 cookie，并在调用链的下游获取 cookie

## 使用场景

### 透传命令字优先级

接入 Svr 在 cookie 中设置请求命令字 id 和请求命令字优先级，透传到后端，后端可以根据这些信息定制自己的过载保护策略（譬如优先拒绝低优先级请求）

### 透传 msgno 和 uid

接入 Svr 在 cookie 中设置消息序列号(msgno)和用户 id(uid), 后端服务可以取出 cookie，并将 msgno 和 uid 打印在日志头中（这一步可以通过封装日志宏来自动处理）；
这样定位问题时，可以通过 uid 或者 msgno 来快速查找日志（譬如 A 调用 B 的场景，看到 A 的错误日志，则可以复制出 msgno，前往 B 服务快捷查找到该请求的日志）；
业务也可以把日志上报到 elk 等检索平台，便于通过 msgno 查找到所有日志

## 和染色关系

1. cookie 和染色类似，都可以在调用链上传播
2. 染色从概念上来说，一般只针对少量请求；而 cookie 针对全量请求
3. 染色会打印一份集中染色日志，而 cookie 不会
4. cookie 和染色可以同时使用，既针对全量请求设置 cookie，针对小部分需要特殊处理的请求进行染色（并且如果日志中有打印 cookie 中 msgno 和 uid 的话，在集中染色日志查找一个请求的完整日志也会很方便）
5. 使用全量请求染色也可以实现类似 cookie 的功能，但是这样相当于丢失了染色功能，只有 cookie 功能，而且此时磁盘 I/O 和使用容量会翻倍
6. 设置/读取 cookie 的函数使用 map 参数，方便业务定制各种信息，使用起来更加友好

## 使用示例

### 设置 cookie

```cpp
#include "servant/Cookie.h"
CookieOp cookieOp;
map<string, string> cookie;
cookie["msgno"] = "12345";
cookie["uid"] = "67890";
cookieOp.setCookie(cookie);
```

### 获取 cookie

#### 从线程私有数据中获取 cookie

```cpp
#include "servant/Cookie.h"
map<string, string> & cookie = CookieOp::getCookie();
TLOGDEBUG("cookie:" << TC_Common::tostr(cookie.begin(), cookie.end()) << endl);
TLOGDEBUG("msgno:" << cookie["msgno"] << endl);
```

#### 从 current 对象中获取 cookie

```cpp
map<string, string> & cookie = current->getCookie();
TLOGDEBUG("current cookie:" << TC_Common::tostr(cookie.begin(), cookie.end()) << endl);
TLOGDEBUG("msgno:" << cookie["msgno"] << endl);
```

## 具体应用

### 接入服务设置 cookie

接入服务在 RPC 调用之前，在 cookie 中设置 msgno 和 uid

```cpp
#include "servant/Cookie.h"

CookieOp cookieOp;
map<string, string> cookie;
cookie["msgno"] = genMsgNo();
cookie["uid"] = uid;
cookieOp.setCookie(cookie);
UserInfoServantPrx->async_getUserInfo(cb, ...);
```

### 后端服务打印日志

后端服务使用如下定制的宏来打印日志，日志头会自动带上 msgno 和 uid

```cpp
LOG_ERROR("call DBProxy.getUserInfo failed, ret:" << ret);
```

LOG_ERROR 宏定义如下

```cpp
#define LOG_ERROR(msg) \
    TLOGERROR(__FILENAME__ << ":" << __LINE__ << ":" << __FUNCTION__ << "|" << CookieOp::getCookie()["msgno"] << "|" << CookieOp::getCookie()["uid"] << "|" << msg << endl);
```

### 查看日志

查看每个后端服务的日志，可以看到每条日志上都带上了 msgno 和 uid, 同一个请求在所有服务中的日志，msgno 和 uid 相同，其中 af4686091585191682152580023 和 10001 分别为 msgno 和 uid

#### UserInfoSvr 的日志

```text
2020-03-26 11:01:22|24209|DEBUG|UserInfoServantImp.cpp:1019:getUserInfo|af4686091585191682152580023|10001|call DBProxy.getUserInfo failed, ret:-1
```

#### DBProxySvr 的日志

```text
2020-03-26 11:01:22|12359|DEBUG|DBProxyServantImp.cpp:720:getUserInfo|af4686091585191682152580023|10001|sql is: select * from t_user wehre uid = '10001'
2020-03-26 11:01:22|12359|ERROR|DBProxyServantImp.cpp:723:getUserInfo|af4686091585191682152580023|10001|access db timeout
```
