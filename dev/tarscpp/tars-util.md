# 工具类说明(C++)

该文档描述了基础工具类。这些工具类能够快速方便的帮你完成开发, 强烈推荐你使用!

**关于线程安全：**

- 所有类的静态函数都是线程安全的；
- 除了TC_Thread开头的类是线程安全外，其他类都不是线程安全的(不能两个线程同时操作一个对象)；
- 如果只是只读，则是线程安全的；

## 异常类

### TC_Exception

异常基类，所有的异常都直接或者间接继承与它；

构造函数中，提供了一个可以传入errno的构造函数，异常抛出时直接获取strerror(errno)的错误信息；

建议用:TC_Exception::getSystemCode() 获取错误码, windows下是GetLastError(),
linux/mac下是errno, 用TC_Exception::parseError(TC_Exception::getSystemCode())获取错误信息.

## 帮助类

### TC_Common

**基础工具类，提供了一些非常基本的函数使用。**

这些函数都是以静态函数提供。

包括以下几种函数：

- Trim类函数：trim,trimLeft,trimRight；

- 大小写转换函数：lower、upper；

- 分隔字符串函数：sepstr （直接分隔字符串，数字等）

- 时间相关函数：str2tm、tm2str、now2str、tm2GMTstr、now2GMTstr、now2us；

- 字符串转换函数：tostr、strto；

- 二进制字符串互转函数：bin2str、str2bin；

- 替换字符串函数：replace；

- Ip匹配函数：matchPeriod；

- 其他常用的函数

### TC_File

**文件相关的操作。**

这些函数都是以静态函数提供。

- 获取文件大小：getFileSize

- 是否是绝对路径：isAbsolute

- 文件是否存在：isFileExist，isFileExistEx

- 规则化目录：simplifyDirectory

- 创建目录：makeDir、makeDirRecuresive

- 设置文件可执行：setExecutable(windows下没有)、canExecutable

- 删除文件：removeFile

- 读取小文件到内存(string)：load2str

- 写内存(string)到文件：save2file

- 获取文件可执行路径：getExePath

- 分析文件名称：extraceFileName、extractFilePath、extractFileExt、excludeFileExt、replaceFileExt、extractUrlFilePath

- 遍历目录：scanDir、listDirectory

- 拷贝文件：copyFile

## 编解码、加密类

### TC_Des

- des的加密/解密函数；
- 3des加密/解密函数；
- 函数以静态形式提供，线程安全；
- des/3des加密解密源码, 不依赖任何库
- 网上流行的d3des.h d3des.c修改完成；
- Key必须是null结束的字符串.

**对于des加密：**

- 8位密钥,不足8位的右补0x00;
- 多余8位,只取左8位有效;
- 加密内容8位补齐,补齐方式为:少1位补一个0x01,少2位补两个0x02,...
- 本身已8位对齐的,后面补八个0x08

**对于3des加解密:**

- 只支持3des-ecb加密方式；
- 24位密钥,不足24位的右补0x00;
- 多余24位,只取左24位有效;
- 加密内容8位补齐，补齐方式为：少1位补一个0x01,少2位补两个0x02,...
- 本身已8位对齐的，后面补八个0x08;

```cpp  
#define KEY  "dRa93seg"
#define KEY3 " dRa93segdRa93seg dRa93seg"

string n = "abde";
{
    string s = n;
    cout << "des encrypt:" << s << endl;
    string v = taf::TC_Des::encrypt(KEY, s.c_str(), s.length());
    cout << "encrypt:" << TC_Common::bin2str(v) << endl;
    string s1 = TC_Des::decrypt(KEY, v.c_str(), v.length());
    cout << "des decrypt:" << s1 << endl;
}

{
    string s = n;
    cout << "encrypt:" << s << endl;
    string v = taf::TC_Des::encrypt3(KEY3, s.c_str(), s.length());
    cout << "encrypt:" << TC_Common::bin2str(v.c_str()) << endl;
    string s1 =TC_Des::decrypt3(KEY3, v.c_str(), v.length());
    cout << "decrypt:" << s1 << endl;
}
```

### TC_Base64

标准base64的编解码函数，通过静态函数提供。

```cpp
string src = “abc”;
//编码
string dest =TC_Base64::encode(src);
//解码
string src1 = TC_Base64::decode(dest);
assert(src == src1);
```

### TC_MD5

Md5的散列算法，通过静态函数提供。

```cpp
string src = “test”;
string hs = TC_MD5::md5str(src);
cout << hs << endl;
```

### TC_Encoder

Gbk到utf8之间的相互转码函数，通过静态函数提供。

```cpp
string s = "我们";
string v = TC_Encoder::gbk2utf8(s);
cout << s << ":" << v << ":" << TC_Encoder::utf82gbk(v) << endl;
```

该类也提供了**transTo/transFrom**函数用于将string的\\n替换掉，或从替换的数据恢复源数据，主要用于将string记录在一行，通常用于写**bin-log**的地方。

### TC_Pack

- 二进制的组包解包类，通过`<<、>>`操作符实现；
- 对string，采用了压缩格式：长度`<255: 1个字节长度, 内容；长度>`=255,
  4个字节(长度), 内容；
- 所有整形/长度值都采用字节序；
- **非线程安全；**

### TC_Parsepara

- 提供name=value&name1=value1形式的解析函数；
- 可以和map互相转换；
- 不是标准的cgi参数的解析（标准cgi参数解析会把空格和+互转）；
- **非线程安全；**

## 锁类

注意:对于线程锁,TAF5.0可以直接使用c++11的锁了!这些类的存在是为了兼容TAF低版本!

### TC_SemMutex

- 进程锁, 可以跨进程加锁
- Linux/Mac下IPC信号量锁，进程、线程间锁；
- Windows使用的Mutex/Event实现
- 提供了读写锁功能；

### TC_LockT

- 模板锁类，和其他具体锁配合使用。
- 构造时候加锁，析够的时候解锁；
- TC_TryLockT类似，用于锁尝试操作；
- TC_EmptyMutex：空锁，不提供任何操作；
- 其他具体锁的加解锁函数一样，保证能够和TC_LockT配合使用；

```cpp

TC_SemMutex sm;
sm.init(8888);
TC_LockT<TC_SemMutex> sync(sm);

TC_ThreadMutex tm;
TC_LockT< TC_ThreadMutex > sync(tm);

```

### TC_ThreadMutex

- 线程锁，不可重复加锁，即同一个线程不可以重复加锁;
- 通常不直接使用，和TC_Monitor配合使用，即TC_ThreadLock;

### TC_ThreadRecMutex

- 线程锁，可重复加锁，即同一个线程可以重复加锁;
- 通常不直接使用，和TC_Monitor配合使用，即TC_ThreadRecLock;

### TC_ThreadRWLock

- 线程级别的读写锁, 采用c++11 mutex封装实现

### TC_ThreadCond

- 线程信号条件类，和TC_ThreadMutex、TC_ThreadRecMutex配合使用；
- 通常不直接使用，而是使用TC_ThreadLock/TC_ThreadRecLock;

### TC_Monitor

- 线程锁监控模板类。
- 通常线程锁，都通过该类来使用，而不是直接用TC_ThreadMutex、TC_ThreadRecMutex。
- 该类将TC_ThreadMutex/TC_ThreadRecMutex 与TC_ThreadCond结合起来；

定义了两个线程锁的模板：

```cpp
/**
 * 普通线程锁
 */
typedef TC_Monitor<TC_ThreadMutex, TC_ThreadCond> TC_ThreadLock;

/**
 * 循环锁(一个线程可以加多次锁)
 */
typedef TC_Monitor<TC_ThreadRecMutex, TC_ThreadCond> TC_ThreadRecLock;

```

在使用中，强烈建议使用：TC_ThreadLock、TC_ThreadRecLock；

关键成员函数说明：

- wait：当前调用线程在锁上等待，直到事件通知；
- timedWait：当前调用线程在锁上等待，直到超时或有事件通知；
- notify：通知等待在该锁上某一个线程醒过来，注意调用该函数时必须已经获得锁；
- notifyAll：通知等待在该锁上的所有线程醒过来，注意调用该函数时必须已经获得锁；

## 内存结构、文件映射类、数据结构类

### TC_Bitmap

- 内存bitmap，每个整数1位；
- **可以支持多位，即几个整数多位；**
- 操作过程不加锁，如果有需要在外面调用的时候加，通常采用群锁策略；
- 注意群锁策略应该/8，然后按照尾号分群锁

```cpp
TC_Shm      g_shm;
TC_BitMap   g_bmap;
//计算4字节整型的bitmap需要多大内存
size_t iMemSize    = TC_BitMap::calcMemSize(uin32_t(-1));
//初始化共享内存
g_shm.init(iMemSize, 9999);
//创建bitmap
if (g_shm.iscreate())
{
    g_bmap.create(g_shm.getPointer(), g_shm.size());
}
else
{
    g_bmap.connect(g_shm.getPointer(), g_shm.size());
}
//获取某一个整型对应的一位，超出bitmap则返回－1
cout << "get:" << uin << "=" << g_bmap.get(uin) << endl;
//设置整型
cout << "set:" << uin << "=" << g_bmap.set(uin) << endl;
//清除整型
cout << "clear:" << uin << "=" << g_bmap.clear(uin) << endl;
//从dump到文件
int ret = g_bmap.dump2file(file);
//从文件load
int ret = g_bmap.load5file(file);
```

### TC_Hashmap(注意, 不建议使用了!!!!)

- **内存hashmap，不要直接使用该类，通过jmem组件来使用，具体请参见11.3**
- 该hashmap通过TC_MemMutilChunkAllocator来分配空间，支持不同大小的内存块的分配；
- 支持内存和共享内存；
- 对接口的所有操作都需要加锁；
- 内部有脏数据链，支持数据缓写；
- 当数据超过一个数据块时，则会拼接多个数据块；
- Set时当数据块用完，自动淘汰最长时间没有访问的数据，也可以不淘汰，直接返回错误；
- 支持dump到文件，或从文件load；

### TC_HashmapCompact

- **紧凑性hashmap，使用方式同TC_Hashmap，但是64位系统下更节约额外的管理空间；**
- **最大每个value的长度只能是64K(两个字节), 所以需要需要保存更大的数据,
  建议使用TC_Hashmap**

### TC_RBTree

- **内存rbtree，不要直接使用该类，通过jmem组件来使用，具体请参见11.4**
- 该红黑树通过TC_MemMutilChunkAllocator来分配空间，支持不同大小的内存块的分配；
- 分配器分配的是内存索引，减少自身消耗的空间（尤其是64位OS下面）；
- 支持内存和共享内存；
- 对接口的所有操作都需要加锁；
- 内部有脏数据链，支持数据缓写；
- 当数据超过一个数据块时，则会拼接多个数据块；
- Set时当数据块用完，自动淘汰最长时间没有访问的数据，也可以不淘汰，直接返回错误；
- 支持dump到文件，或从文件load；

### TC_MemQueue

内存循环队列，建议不要直接使用该类，通过jmem组件来使用，具体参见11.2

```cpp
TC_Shm shm;
shm.init(8000, 1024);
TC_SemMutex  semLock;
semLock.init(8000);

TC_MemQueue memQueue;
//创建队列
if(shm.iscreate())
{
    memQueue.create(shm.getPointer(), 1024);
}
else
{
    memQueue.connect(shm.getPointer());
}

string sTest = “abc”;
//放入到队列尾部
memQueue.push(sTest);
//弹出数据
memQueue.pop(sTest);
```

### TC_MemVector

- 基于内存的随机访问数据快模板类；
- 每个块大小相同；
- 模板对象只支持简单的数据类型，即类型支持bit-copy语义；

```cpp
struct tagTest
{
	char i;
	int n;
	float a[10];
} __attribute__((packed));
//__attribute__((packed))表示采用紧缩内存结构

TC_MemVector<tagTest> vt;
//计算总计内存空间
size_t is = TC_MemVector<tagTest>::calcMemSize(10);
char *buffer = new char[is];
//创建到对象
vt.create(buffer, is);
//链接到对象, vt.connect(buffer);
//获取对象0的对象
vt[0].i = ‘c’;
vt[0].n = 10;
delete []buffer;

```

### TC_Mmap

- **文件内存映射类**；
- **linux/mac下使用mmap实现**
- **windows下使用文件映射内存实现**
- 封装了mmap的操作；

```cpp
TC_Mmap mmap;
//映射到文件，1024个字节
mmap.mmap("mmap.dat", 1024);
string s = “abc”;
//向文件copy数据，和内存操作完全一致；
memcpy(mmap.getPointer(), s.c_str(), s.length());
```

## 内存分配

### TC_MemChunk

- 相同大小内存块类；
- 支持内存和共享内存；
- 每块内存至少4个字节；

### TC_MemChunkAllocator

- 内存块分配器，提供分配和释放的功能；
- 只能分配相同大小的内存块；
- 每个内存块用TC_MemChunk来管理；

### TC_MemMultiChunkAllocator

- 内存块分配器，提供分配和释放的功能；
- 支持分配不同大小的内存块；
- 内部每种块用TC_MemChunkAllocator来分配；
- 每种大小不同块的个数是相同的；

内存块分配的策略如下：

- 确定需要分配多大内存，假设需要分配A字节的内存；
- 分配大小大于\>=A的内存块，优先分配大小最接近的；
- 如果都没有合适内存块，则分配大小<A的内存块，优先分配大小最接近的；
- 如果仍然没有合适内存块，则返回NULL；

```cpp
char *buffer = new char[50240];
//定义分配器对象
TC_MemMultiChunkAllocator alloc;
//初始化，10：最小块大小，100：最大块大小，1.2：增长因子，表示块之间大小的增长比值		
alloc.create(buffer, 50240, 10, 100, 1.2);
vector<void*> v;
//定义需要分配的大小
size_t n = 10;
while(true)
{
    size_t iAllocSize;
    //分配空间，iAllocSize：真正分配的块大小
    void *p = alloc.allocate(n, iAllocSize);
    if(!p)
    {
        break;
    }
    v.push_back(p);
    n += 2;
}
//释放空间
for(size_t i = 0; i < v.size(); i++)
{
    alloc.deallocate(v[i]);
}
delete []buffer;
```

### TC_Shm

- 共享内存管理类；
- 提供分配共享内存、连接共享内存、删除共享内存的功能；
- Linux/mac下用IPC的shm实现
- Windows下使用文件映射内存实现

```cpp
TC_Shm      g_shm;
size_t iMemSize = 10240;
g_shm.init(iMemSize, 9999);
if (g_shm.iscreate())
{
    //共享内存是创建的
        ;
}
else
{
    //共享内存是链接上的
;
}
//删除共享内存，类似ipcrm -M
g_shm.del();
```

### TC_ThreadQueue

- 基于内存、STL容器的线程安全队列；
- 采用TC_ThreadLock加解锁；

```cpp
//定义队列
TC_ThreadQueue<string> _queue;
//循环获取数据
while(true)
{
	//没有数据则等待10000ms
    if(_queue.pop_front(s, 10000))
    {
        cout << s << endl;
    }
    sleep(1);
}
while(true)
{
	//放入数据
    _queue.push_back(s);
    sleep(1);
}
//当程序退出时，如果线程仍然等待在pop_front上，则线程锁析够的时会有问题，此时需要用notifyT通知在pop_front上等待的线程醒过来
_queue.notifyT();
```

### TC_TimeoutQueue

- 基于内存、STL容器的线程安全超时队列；
- 在如下场景中使用:
  - 需要当成map, 可以快速读写key-value数据;
  - 需要能够设置超时时间, 到点淘汰数据;

主要用于: 超时队列使用, 框架中也大量使用这个类

## 数据库类

### TC_Mysql

- 提供mysql的操作类；
- **非线程安全，通常一个线程一个TC_Mysql对象；**
- **对于insert/update可以有更好的函数封装，保证SQL注入；**
- TC_Mysql::DB_INT表示组装sql语句时，不加””和转义；
- TC_Mysql::DB_STR表示组装sql语句时，加””并转义；

```cpp
TC_Mysql mysql;
//初始化mysql，init时不链接，请求时自动建立链接；
//数据库可以为空；
//端口默认为3306
mysql.init("10.1.36.39", "pc", "pc@sn", "db_dmqq_system");
//获取链接的字符集
cout << mysql.getVariables("character_set_client") << endl;
//获取数据
TC_Mysql::MysqlData data;
data = mysql.queryRecord("select * from t_app_users");
for(size_t i = 0; i < data.size(); i++)
{
    //如果不存在ID字段，则抛出异常
    cout << data[i]["ID"] << endl;
}

//插入数据，指定数据的类型：数值 或 字符串，对于字符串会自动转义
map<string, pair<TC_Mysql::FT, string> > m;
m["ID"]     = make_pair(TC_Mysql::DB_INT, "2334");
m["USERID"] = make_pair(TC_Mysql::DB_STR, "abcttt");
m["APP"]    = make_pair(TC_Mysql::DB_STR, "abcapbbp");
m["LASTTIME"]    = make_pair(TC_Mysql::DB_INT, "now()");

//   mysql.insertRecord("t_user_logs", m);
mysql.replaceRecord("t_user_logs", m);

```

## 网络类

### TC_Socket

- 提供socket的操作类；
- 支持tcp/udp socket；
- 支持本地域套接字；

```cpp
//获取本地的所有ip地址
vector<string> v = TC_Socket::getLocalHosts();
cout << TC_Common::tostr(v.begin(), v.end()) << endl;

```

### TC_Epoller

提供网络epoll的操作类。

### TC_Transceiver

提供网络连接的操作类, 具体使用请参考头文件。

### TC_CoroutineScheduler

协程调度器.
### TC_CoroutineQueue

协程调度队列.

用于跨线程的协程的数据交互, 队列没有数据时, 协程会阻塞在epoller上, 当有网络事件或者其他协程调度时会被唤醒处理其他事件!
### TC_ClientSocket

提供客户端同步请求的socket类，用于发送数据包，支持tcp/udp，支持**大句柄**；

- 在TC_Socket基础上实现；
- **多线程使用的时候，不用多线程同时send/recv，小心串包；**
- TC_TCPClient：tcp的客户端类，socket句柄自动重连；
- TC_UDPClient：udp的客户端类；
- TC_TCPClient/TC_UDPClient的发送或接收数据的返回值请参见TC_ClientSocket::EM_等枚举值；
- 初始化时指定：ip，port，超时时间（毫秒），注意对连接超时也处理了；
- 支持本地套接字，如果端口port是0表示是本地套接字，此时ip表示本地套接字的文件路径；

### TC_TCPClient

- **多线程使用的时候，不用多线程同时send/recv，小心串包；**
- send：发送数据
- recv：接收数据
- recvLine：接收一行，即直到\\r\\n位置；
- recvAll：接收所有数据，直到服务器关闭链接；
- recvLength：接收指定长度的数据；
- sendRecv：发送并接收数据；
- sendRecvLine：发送并接收一行数据；
- sendRecvAll：发送并接收所有数据（直到服务器关闭链接）；

socket被关闭，会自动重新链接；

### TC_UDPClient

- **多线程使用的时候，不用多线程同时send/recv，小心串包；**
- send：发送数据
- recv：接收数据
- recv：接收数据并返回远程的ip和端口
- sendRecv：发送并接收数据；
- sendRecv：发送并接收数据，同时返回远程ip和端口；

## 线程类

### TC_Thread

线程基类，所有自定义线程继承于该类，同时实现run接口即可。可以通过TC_ThreadContorl管理线程。

线程最大的问题就是如何正确的退出。通常一个线程在运行过程中，会做一些工作（比如从队列中获取数据执行计算），如果队列为空，就休息一会儿再执行，这里如果采用sleep来休息，则退出过程中，恰好在sleep，则必须等这时间之后才能退出。下面例子给出了如何正常退出一个线程的思路：

```cpp
class MyThread : public TC_Thread, public TC_ThreadLock
{
public:
    MyThread()
    {
        bTerminate = false;
    }
    /**
     * 结束线程
     */
    void terminate()
    {
		//先将退出标识设置为true
        bTerminate = true;
		
        {
			//加锁并通知线程醒过来
            TC_ThreadLock::Lock sync(*this);
            notifyAll();
        }
    }

    void doSomething()
    {
        cout << "doSomething" << endl;
    }
    /**
     * 运行
     */
protected:
    virtual void run() 
    {
        while(!bTerminate)
        {
             //TODO: your business
            doSomething();

			//做完工作以后，等待一下，注意在锁上等待，通知线程醒来的时候也是在锁上通知
            {
                TC_ThreadLock::Lock sync(*this);
                timedWait(1000);
            }
        }
    }

protected:
    bool bTerminate;
};

int main(int argc, char *argv[])
{
    try
    {
        MyThread mt;
        mt.start();

        sleep(5);
		
		//通知线程结束，并等待线程完全退出
        mt.terminate();
        mt.getThreadControl().join();
    }
    catch(exception &ex)
    {
        cout << ex.what() << endl;
    }

    return 0;
}
```

**说明：关键的思路就是采用线程锁来完成通知，保证线程正确、安全、及时的退出。**

### TC_ThreadPool

- 线程池类；
- 需要和std::bind配合使用；
- 方便的多线程执行一个具体操作；


## 日志类

### TC_Logger

- 日志模板类；
- 支持同步写、异步写；
- 线程安全的；
- 定义了TC_DayLogger, TC_RollLogger,通常直接使用这两个类即可；

## CGI类

### TC_Cgi

- CGI解析类；
- 支持标准的HTTP解析；
- 支持Cookies；
- 支持多个文件的上传；

### TC_Http

- Http协议解析类；
- 包括TC_HttpRequest、TC_HttpResponse两个类；
- 支持GET和POST，其他HTTP方法不支持；
- 通过TC_HttpRequest::checkRequest判断http请求是否收完；
- 与TC_ClientSocket配合，支持同步发送http请求，且支持http的chunk编码；
- 发送http请求时，非线程安全；

```cpp
 //定期http request对象
TC_HttpRequest stHttpReq;
stHttpReq.setCacheControl("no-cache");
//设置agent
stHttpReq.setUserAgent("TestAgent");
//设置请求地址和方式(GET方式请求)
stHttpReq.setRequest("http://www.qq.com", TC_HttpRequest::REQUEST_GET);
TC_HttpResponse stHttpRep;
//发送请求，并等待完整的HTTP响应，超时时间为10s
int iRet = stHttpReq.doRequest(stHttpRep, 10000);
if(iRet != 0)
{
    cout << iRet << endl;
}
//HTTP响应包的内容
cout << stHttpRep.getContent();
```

### TC_HttpAsync

- Http协议异步请求类
- 支持ssl

### TC_UUIDGenerator
- 单例
- 生成唯一的UUID

## 命令解析、配置文件类

### TC_Config

- 配置文件解析类
- 支持从string中解析配置文件；
- **支持生成配置文件**；
- 解析出错抛出异常；
- 采用[]获取配置，如果无配置则抛出异常；
- 采用get获取配置，不存在则返回空；
- **读取配置文件是线程安全的，insert域等函数非线程安全；**

例如：

```cpp
TC_Config conf;
//解析配置文件
conf.parseFile("./config.conf");
//输出配置文件格式
cout << conf.tostr() << endl;
//配置文件读入到内存
string s = TC_File::load2str("./config.conf");
//解析字符串
conf.parseString(s);
//读取root域下root字段，不存在则抛出异常
cout << conf["/root<root>"] << endl;
//读取root域下root字段，不存在则返回abc
cout << conf.get("/root<root>", “abc”) << endl;
//root/insert下插入insert子域，false：表示root/insert不存在时不创建
conf.insertDomain("/root/insert", "insert", false);		
map<string, string> m;
m["abc"] = "def";
m["ttt"] = "yyy";
//root/insert/insert下创建参数值对，false 表示/root/insert/insert不存在，则不创建
conf.insertDomainParam("/root/insert/insert", m, false);
//root/insert/insert下创建参数值对，true：表示自动创建
conf.insertDomainParam("/root/insert/insert", m, true);
cout << conf.tostr() << endl;
```

### TC_Option

- 命令解析类；
- 通常用于解析命令行参数；
- 只支持双—的参数形式

分析main的输入参数，支持以下形式的参数：

./main.exe --name=value --param1 param2 param3

```cpp
 TC_Option op;
//解析命令行
op.decode(argc, argv);
//获取成对的参数，即获取 - - 表示的所有参数对
map<string, string> mp = op.getMulti();
//表示非 – 的参数：即 param2, param3
vector<string> d = op.getSingle();
```

如果value，param有空格或者--，用引号括起来就可以了。

## 智能指针类

### TC_AutoPtr

- 智能指针模板类；
- 通过它定义智能指针；
- 该智能指针通过引用计数实现，可以放在容器中传递；
- 使用方式请参见：TC_HandleBase
- 其实可以用c++11中shared_ptr代替!!

### TC_HandleBase

- 智能指针基类；
- 采用TC_AutoPtr模板化的对象，必须都继承于该类；
- 内部采用引用计数std::atomic实现；
- 对象可以放在容器中；

例如：

```cpp
class TestAuto : public TC_HandleBase
{
    
};

Typedef TC_AutoPtr<TestAuto> TestAutoPtr;
```

## 其他类

### TC_GZip

- Gzip压缩解压类, 需要连接zlib
- 仅仅支持linux/mac

### TC_TimeProvider

- 时间获取类,获取毫秒/秒
- 不直接使用时间获取函数得到
- 背后有一个常驻线程, 通过计算cpu的频率来获取, 减少时间函数调用的用户内核态切换
- 建议需要获取时间的函数都通过这个类来得到

### TC_Singleton(单例)

### TC_Json

- Json解析类, 通常不直接使用
- Jce结构体都会生成json的对应函数
- 必要的时候可以使用

### TC_Timer

- 定时器类
- 可以设置定事情, 并执行回调
- 可以设置回调的线程数