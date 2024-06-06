## TarsPython 使用说明

TarsPython 提供了`whl的安装包`以及`tars2py`的工具, 方便业务开发者使用python开发Tars相关的服务.

### 源码编译

如果你是通过源码编译得到TarsPython, 则需要做如下步骤:
- 首先编译tarscpp, 注意编译tarscpp时, 必须如下: `cmake .. -DTARS_STD_SHARED_PTR=ON && make && make install`
- 注意这里的: `TARS_STD_SHARED_PTR` 必须启用
- 再配置好python, 注意编译的whl包和当前python环境的版本匹配, 也只能运行在这个版本的python环境下
- 建议你用conda来管理python虚拟环境, 当然源码中其实可以使用docker来一次性编译多个linux下的python环境的whl包
- 编译TarsPython源码: `cmake .. && make -j4 && make install`
- 会在当前编译目录, release/content/下得到 `whl`安装包, 比如: tars-1.0.0-cp311-none-manylinux2014_x86_64.whl  

### 安装whl包

```sh
#安装tars包
pip install tars-1.0.0-cp311-none-manylinux2014_x86_64.whl 

#验证是否安装成功,如果输出当前时间则安装正常，否则失败（请确认包的版本与系统是否匹配）
python -c 'from tars.kernel  import TC_Common;print(TC_Common.now2msstr())' 

2024-03-23 12:53:54.910

#安装pyinstaller用来后续打包python程序使用
pip install pyinstaller

```

### 创建自己的服务


```c++
#工程目录
mkdir workspace
cd workspace

#使用提供好的工具创建 demo 工程, 如果找不到create_tars_svr.py, 则设置PATH, 如果是linux则为: $HOME/.local/bin/
create_tars_svr.py --app Test --server HelloServer --servant Hello


#会自动生成以下文件
HelloServer
    ├── HelloClient.py		#客户端文件
    ├── Hello.tars			#tars文件
    ├── HelloServer.conf	#业务配置文件
    ├── HelloServer.py		#服务启动入口文件
    ├── Test
    │   ├── Hello.py		#tars生成的协议文件
    │   └── __init__.py		
    └── Test.HelloServer.config.conf	#服务servant配置，用于本地调试
```

说明: 
- 接口逻辑都在`HelloServer.py`中, 通常你需要修改这个文件
- 接口实现可以参考`HelloServer.py`中的test, 同步回包和异步回包方式, 注意生成代码中将同步回包方式注释了, 同步和异步方式只需要选择一种即可.
- 你如果要添加接口, 则修改`Hello.tars`, 然后执行: `tars2py Hello.tars --dir=Test`,  注意`--dir`制定了生成的协议文件的位置, 你需要根据自己的服务具体情况指定
- 添加接口并生成代码后, 自己修改`HelloServer.py`, 增加接口的实现

### 启动服务端

```sh
#进入工程目录
cd HelloServer
#启动服务端程序
python3 HelloServer.py --config=Test.HelloServer.config.conf

#输出
...
[initialize server] .............................. [Done]
in app init........... Test HelloServer

 
#运行测试客户端
python3 HelloClient.py 

#输出
...
rpc before: 1652925700.5920298
callback_test: 0 {'c': 300}

#如果没有抛出异常，则运行正确
```


### 发布服务

代码开发完成后，需要将代码打包成一个单独的可执行程序，来通过tars平台发布

打包依赖 pyinstaller，需要先安装pyinstaller : `pip install pyinstaller`

- 进入工程目录
```sh
cd HelloServer
```    
- 打包服务
```
create_tar.py HelloServer.py 
```

会生成HelloServer.tgz 文件, 发布这个文件的tars平台即可, 注意模板选择: tars_python

- 运行

你也可以直接执行可执行程序来启动服务端，如果启动正常，则打包成功，否则需要确认python环境和pyinstaller是否正确安装
./HelloServer --config=Test.HelloServer.config.conf

- 依赖

打包生成HelloServer.tgz过程中, pyinstaller会分析依赖, 将python环境以及相关依赖都打包进了HelloServer.tgz, 因此正常情况下, 服务器上不需要额外安装python, 但是pyinstaller有时候会分析不准确, 打包时缺少依赖, 这个时候你需要自己配置依赖, 请参考pyinstaller相关文档.


### 其他说明

python版本的服务框架, 底层使用c++来实现, 因此它具备以下特性:
- 原则上c++具备的能力, python版本的服务都具备;
- 由于python全局锁的存在, python版本程序在业务层(python层面), 即使有多个线程存在, 也是在串行执行;
- 关于tars框架中, 配置获取, 日志, 同步/异步rpc等特性, 请参考HelloServer.py/HelloClient.py中的实现, 如果不理解, 请参考c++语言的文档充分理解tars服务框架;


