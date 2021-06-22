r# 管理平台 API
> * [Auth](#auth)
> * [业务树](#tree)
> * [服务管理](#server)
>- * [部署服务](#deploy-server)
>- * [修改服务](#modify-server)
>- * [取服务](#get-server)
>- * [取服务列表](#get-server-list)
>- * [取设置状态为inactive的服务列表](#get-inactive)
>- * [取服务notify日志列表](#get-notify-server)
>- * [取服务实时状态](#get-server-status)
>- * [加载服务](#load-server)
>- * [预扩容](#pre-expand)
>- * [扩容](#expand)
>- * [新增Adapter](#add-adapter)
>- * [删除Adapter](#delete-adapter)
>- * [修改Adapter](#modify-adapter)
>- * [取Adapter](#get-adapter)
>- * [取Adapter列表](#get-adapter-list)
> * [服务配置](#config)
>- * [新增配置文件](#add-config)
>- * [删除配置文件](#del-config)
>- * [修改配置文件](#modify-config)
>- * [取配置文件](#get-config)
>- * [取配置文件列表](#get-config-list)
>- * [取节点配置文件列表](#get-node-config-list)
>- * [取配置文件修改记录](#get-config-modify-history)
>- * [取配置文件修改记录列表](#get-config-modify-list)
>- * [新增引用](#add-ref)
>- * [删除引用](#del-ref)
>- * [引用列表](#ref-list)
>- * [合并后节点配置](#merge-node-config)
>- * [下发节点配置](#push-config)
> * [任务管理](#task)
>- * [新增任务](#add-task)
>- * [取任务及子任务详细信息](#get-task)
>- * [取任务列表](#get-task-list)
> * [发布包](#publish)
>- * [上传发布包](#upload-publish)
>- * [上传并发布](#upload-and-publish)
>- * [取发布版本列表](#get-publish-list)
> * [模板](#template)
>- * [新增模板](#add-template)
>- * [删除模板](#del-template)
>- * [修改模板](#modify-template)
>- * [取模板](#get-template)
>- * [查询模板](#query-template)
> * [监控](#monitor)
>- * [取tarsstat监控数据](#get-tarsstat)
>- * [取tarsproperty监控数据](#get-tarspropery)
> * [字典](#dict)
>- * [取服务类型列表](#get-server-type)
>- * [取模板列表](#get-template-list)
>- * [级联选择服务](#choose-server)
> * [资源](#resource)
>- * [安装Tars Node](#install-tarsnode)
>- * [卸载Tars Node](#uninstall-tarsnode)
> * [其他](#other)
>- * [发送自定义命令](#send-command)
>- * [自动获取未被占用的端口](#get-port)

## <span id="auth"></span>权限说明

请使用web >= 2.0.0 版本

在用户中心中新建token, 获取token后方可使用web api.

web api使用方式如下:
```
http://xx.xx.xx.xx/api/[command]?ticket=[token]

```

ticket: web平台中生成的token

## <span id="tree"></span>业务树

/api/tree

取业务树

#### 参数

无

#### 返回值

```text
{
    "id": "",                  // 节点ID
    "name": "",                // 节点名称
    "pid": "",                 // 父节点ID
    "open": true,              // 是否展开
    "is_parent": true,         // 是否父节点
    "children": []             // 子节点
}
```

## <span id="server"></span>服务管理

### <span id="deploy-server"></span>部署服务

/api/deploy\_server

#### 参数

```text
{                                  
    "application": "",             // 应用
    "server_name": "",             // 服务
    "node_name": "",               // 节点
    "server_type": "",             // 服务类型
    "template_name": "",           // 模板名
    "enable_set": true,            // 是否启用Set
    "set_name": "",                // Set名
    "set_area": "",                // Set区
    "set_group": "",               // Set组
    "adapters": [{ 
        "obj_name": "",            // OJB名称
        "bind_ip": "",             // Obj绑定IP
        "port": "",                // 端口
		"port_type": "tcp",        // 端口类型
		"protocol": "tars",        // 协议
        "thread_num": 0,           // 线程数
        "max_connections": 0,      // 最大连接数
        "queuecap": 0,             // 队列最大长度
        "queuetimeout": 0          // 队列超时时间
    }]                             
}
```

#### 返回值

```text
{
	"server_conf": {          //新上线服务信息                        
		"id": 0,                   // 服务ID
		"application": "",         // 应用
		"server_name": "",         // 服务
		"node_name": "",           // 节点
		"server_type": "",         // 服务类型
		"enable_set": true,        // 是否启用Set
		"set_name": "",            // Set名
		"set_area": "",            // Set区
		"set_group": "",           // Set组
		"setting_state": "",       // 设置状态
		"present_state": "",       // 当前状态
		"bak_flag": true,          // 是否备机
		"template_name": "",       // 模板名称
		"profile": "",             // 私有模板
		"async_thread_num": 0,     // 异步线程数
		"base_path": "",           // 缺省路径
		"exe_path": "",            // EXE路径
		"start_script_path": "",   // 启动脚本
		"stop_script_path": "",    // 停止脚本
		"monitor_script_path": "", // 监控脚本
		"patch_time": "",          // 发布时间
		"patch_version", "",       // 发布版本
		"process_id": "",          // 进程ID
		"posttime": ""             // 更新时间
	},
	"tars_node_rst":[   //tarsnode安装结果，若不启用资源模块，则此数据为空
		{
			"ip":"1.1.1.3",  //机器IP
			"rst":false,//安装tarsnode结果
			"msg":"未找到机器配置" //安装结果信息
		}
	]
}
```

#### 备注

只支持POST方式，Header中指定Content-Type:application/json

### <span id="modify-server"></span>修改服务

/api/update\_server

#### 参数

```text
{
	"id": 0,                  // 服务ID
	"isBak": true,            // 是否备机
	"template_name": "",      // 模板名称
	"server_type": "",        // 服务类型
	"enable_set": "",         // 是否启用Set
	"set_name": "",           // Set名
	"set_area": "",           // Set区
	"set_group": "",          // Set组
	"async_thread_num": 0,    // 异步线程数
	"base_path": "",          // 缺省路径
	"exe_path": "",           // EXE路径
	"start_script_path": "",  // 启动脚本
	"stop_script_path": "",   // 停止脚本
	"monitor_script_path": "",// 监控脚本
	"profile": ""             // 私有模板
}
```

#### 返回值

```text
{
    "id": 0,                   // 服务ID
    "application": "",         // 应用
    "server_name": "",         // 服务
    "node_name": "",           // 节点
    "server_type": "",         // 服务类型
    "enable_set": true,        // 是否启用Set
    "set_name": "",            // Set名
    "set_area": "",            // Set区
    "set_group": "",           // Set组
    "setting_state": "",       // 设置状态
    "present_state": "",       // 当前状态
    "bak_flag": true,          // 是否备机
    "template_name": "",       // 模板名称
    "profile": "",             // 私有模板
    "async_thread_num": 0,     // 异步线程数
    "base_path": "",           // 缺省路径
    "exe_path": "",            // EXE路径
    "start_script_path": "",   // 启动脚本
    "stop_script_path": "",    // 停止脚本
    "monitor_script_path": "", // 监控脚本
    "patch_time": "",          // 发布时间
    "patch_version", "",       // 发布版本
    "process_id": "",          // 进程ID
    "posttime": ""             // 更新时间
}
```

#### 备注

只支持POST方式，Header中指定Content-Type:application/json

### <span id="get-server"></span>取服务

/api/server

#### 参数

```text
id // 服务ID
```

#### 返回值

```text
{
    "id": 0,                   // 服务ID
    "application": "",         // 应用
    "server_name": "",         // 服务
    "node_name": "",           // 节点
    "server_type": "",         // 服务类型
    "enable_set": true,        // 是否启用Set
    "set_name": "",            // Set名
    "set_area": "",            // Set区
    "set_group": "",           // Set组
    "setting_state": "",       // 设置状态
    "present_state": "",       // 当前状态
    "bak_flag": true,          // 是否备机
    "template_name": "",       // 模板名称
    "profile": "",             // 私有模板
    "async_thread_num": 0,     // 异步线程数
    "base_path": "",           // 缺省路径
    "exe_path": "",            // EXE路径
    "start_script_path": "",   // 启动脚本
    "stop_script_path": "",    // 停止脚本
    "monitor_script_path": "", // 监控脚本
    "patch_time": "",          // 发布时间
    "patch_version", "",       // 发布版本
    "process_id": "",          // 进程ID
    "posttime": ""             // 更新时间
}
```

### <span id="get-server-list"></span>取服务列表

/api/server\_list

#### 参数

```text
tree_node_id // 树节点ID
```

#### 返回值

```text
[{
    "id": 0,                   // 服务ID
    "application": "",         // 应用
    "server_name": "",         // 服务
    "node_name": "",           // 节点
    "server_type": "",         // 服务类型
    "enable_set": true,        // 是否启用Set
    "set_name": "",            // Set名
    "set_area": "",            // Set区
    "set_group": "",           // Set组
    "setting_state": "",       // 设置状态
    "present_state": "",       // 当前状态
    "bak_flag": true,          // 是否备机
    "template_name": "",       // 模板名称
    "profile": "",             // 私有模板
    "async_thread_num": 0,     // 异步线程数
    "base_path": "",           // 缺省路径
    "exe_path": "",            // EXE路径
    "start_script_path": "",   // 启动脚本
    "stop_script_path": "",    // 停止脚本
    "monitor_script_path": "", // 监控脚本
    "patch_time": "",          // 发布时间
    "patch_version": "",       // 发布版本
    "process_id": "",          // 进程ID
    "posttime": ""             // 更新时间
}]
```

### <span id="get-inactive"></span>取设置状态为inactive的服务列表

/api/inactive\_server\_list

#### 参数

```text
application // 应用
server_name // 服务
node_name   // 节点
```

#### 返回值

```text
[{
    "id": 0,                   // 服务ID
    "application": "",         // 应用
    "server_name": "",         // 服务
    "node_name": "",           // 节点
    "server_type": "",         // 服务类型
    "enable_set": true,        // 是否启用Set
    "set_name": "",            // Set名
    "set_area": "",            // Set区
    "set_group": "",           // Set组
    "setting_state": "",       // 设置状态
    "present_state": "",       // 当前状态
    "bak_flag": true,          // 是否备机
    "template_name": "",       // 模板名称
    "profile": "",             // 私有模板
    "async_thread_num": 0,     // 异步线程数
    "base_path": "",           // 缺省路径
    "exe_path": "",            // EXE路径
    "start_script_path": "",   // 启动脚本
    "stop_script_path": "",    // 停止脚本
    "monitor_script_path": "", // 监控脚本
    "patch_time": "",          // 发布时间
    "patch_version": "",       // 发布版本
    "process_id": "",          // 进程ID
    "posttime": ""             // 更新时间
}]
```

### <span id="get-notify-server"></span>取服务notify日志列表

/api/server\_notify\_list

#### 参数

```text
tree_node_id // 树节点ID
```

#### 返回值

```text
{
    "count": 0,
    "rows":[{
        "notifytime": "",          // 时间
        "server_id": "",           // 服务ID
        "thread_id": "",           // 线程ID
        "command": "",             // 命令
        "result": ""               // 结果
    }]
}
```

#### 是否支持分页

是


### <span id="get-server-status"></span>取服务实时状态

/api/get\_realtime\_state

#### 参数

```text
id // 服务ID
```

#### 返回值

```text
{
    "realtime_state": ""       // 实时状态
}
```

### <span id="load-server"></span>加载服务

/api/load\_server

#### 参数

```text
application  // 应用
server_name  // 服务
node_name    // 节点
```

#### 返回值

```text
"" // 执行结果
```

### <span id="pre-expand"></span>预扩容

/api/expand\_server\_preview

#### 参数

```text
{
    "application": "",        // 应用
    "server_name": "",        // 服务
    "set": "",                // Set
    "node_name": "",          // 节点
    "expand_nodes": [""],     // 扩容节点
    "enable_set": true,       // 是否启用Set
    "set_name": "",           // Set名
    "set_area": "",           // Set区    
    "set_group": "",          // Set组
    "copy_node_config": true  // node_name非空时是否复制节点配置
}
```

#### 返回值

```text
[{
    "application": "",        // 应用
    "server_name": "",        // 服务
    "set": "",                // Set
    "obj_name": "",           // OBJ名称
    "node_name": "",          // 节点
    "bind_ip": "",            // Obj绑定IP
    "port": 0,                // 端口
    "template_name": "",      // 模板名
    "status": "",             // 状态
}]
```

#### 备注

只支持POST方式，Header中指定Content-Type:application/json

### <span id="expand"></span>扩容

/api/expand\_server

#### 参数

```text
{
    "application": "",            // 应用
    "server_name": "",            // 服务
    "set": "",                    // Set
    "node_name": "",              // 节点
    "copy_node_config": true，    // node_name非空时是否复制节点配置
    "expand_preview_servers": [{
        "node_name": "",          // 节点
        "set": "",                // Set
        "obj_name": "",           // OBJ名称
        "bind_ip": "",            // Obj绑定IP
        "port": 0                 // 端口
    }]
}
```

#### 返回值

```text
{
		"server_conf": {          //新上线服务信息  
			[{                                  
				"id": 0,                   // 服务ID
				"application": "",         // 应用
				"server_name": "",         // 服务
				"node_name": "",           // 节点
				"server_type": "",         // 服务类型
				"enable_set": true,        // 是否启用Set
				"set_name": "",            // Set名
				"set_area": "",            // Set区
				"set_group": "",           // Set组
				"setting_state": "",       // 设置状态
				"present_state": "",       // 当前状态
				"bak_flag": true,          // 是否备机
				"template_name": "",       // 模板名称
				"profile": "",             // 私有模板
				"async_thread_num": 0,     // 异步线程数
				"base_path": "",           // 缺省路径
				"exe_path": "",            // EXE路径
				"start_script_path": "",   // 启动脚本
				"stop_script_path": "",    // 停止脚本
				"monitor_script_path": "", // 监控脚本
				"patch_time": "",          // 发布时间
				"patch_version", "",       // 发布版本
				"process_id": "",          // 进程ID
				"posttime": ""             // 更新时间
			}]
		},
		"tars_node_rst":[   //tarsnode安装结果，若不启用资源模块，则此数据为空
			{
				"ip":"1.1.1.3",  //机器IP
				"rst":false,//安装tarsnode结果
				"msg":"未找到机器配置" //安装结果信息
			}
		]
}
```

#### 备注

只支持POST方式，Header中指定Content-Type:application/json

### <span id="add-adapter"></span>新增Adapter

/api/add\_adapter\_conf

#### 参数

```text
{
	"application": "",         // 应用
	"server_name": "",         // 服务
	"node_name": "",           // 节点
	"thread_num": 1,           // 线程数
	"endpoint": "",            // EndPoint
	"max_connections": 0,      // 最大连接数
	"allow_ip": "",            // 允许IP
	"servant": "",             // Servant
	"queuecap": 0,             // 队列长度
	"queuetimeout": 0,         // 队列超时时间
	"protocol": "",            // 协议
	"handlegroup": ""          // 处理组
}
```

#### 返回值

```text
{
	"id": 0,                   // Adapter ID
	"application": "",         // 应用
	"server_name": "",         // 服务
	"node_name": "",           // 节点
	"adapter_name": "",        // Adapter名
	"thread_num": 1,           // 线程数
	"endpoint": "",            // EndPoint
	"max_connections": 0,      // 最大连接数
	"allow_ip": "",            // 允许IP
	"servant": "",             // Servant
	"queuecap": 0,             // 队列长度
	"queuetimeout": 0,         // 队列超时时间
	"posttime": "",            // 更新时间
	"protocol": "",            // 协议
	"handlegroup": ""          // 处理组
}
```

#### 备注

只支持POST方式，Header中指定Content-Type:application/json

### <span id="delete-adapter"></span>删除Adapter

/api/delete\_adapter\_conf

#### 参数

```text
id // Adapter ID
```

#### 返回值

```text
[0] // 删除的Adapter ID
```

### <span id="modify-adapter"></span>修改Adapter

/api/update\_adapter\_conf

#### 参数

```text
{
	"id": 0,                   // Adapter ID
	"thread_num": 1,           // 线程数
	"endpoint": "",            // EndPoint
	"max_connections": 0,      // 最大连接数
	"allow_ip": "",            // 允许IP
	"servant": "",             // Servant
	"queuecap": 0,             // 队列长度
	"queuetimeout": 0,         // 队列超时时间
	"protocol": "",            // 协议
	"handlegroup": ""          // 处理组
}
```

#### 返回值

```text
{
    "id": 0,                   // Adapter ID
    "application": "",         // 应用
    "server_name": "",         // 服务
    "node_name": "",           // 节点
    "adapter_name": "",        // Adapter名
    "thread_num": 1,           // 线程数
    "endpoint": "",            // EndPoint
    "max_connections": 0,      // 最大连接数
    "allow_ip": "",            // 允许IP
    "servant": "",             // Servant
    "queuecap": 0,             // 队列长度
    "queuetimeout": 0,         // 队列超时时间
    "posttime": "",            // 更新时间
    "protocol": "",            // 协议
    "handlegroup": ""          // 处理组
}
```

#### 备注

只支持POST方式，Header中指定Content-Type:application/json

### <span id="get-adapter"></span>取Adapter

/api/adapter\_conf

#### 参数

```text
id // Adapter ID
```

#### 返回值

```text
{
    "id": 0,                   // Adapter ID
    "application": "",         // 应用
    "server_name": "",         // 服务
    "node_name": "",           // 节点
    "adapter_name": "",        // Adapter名
    "thread_num": 1,           // 线程数
    "endpoint": "",            // EndPoint
    "max_connections": 0,      // 最大连接数
    "allow_ip": "",            // 允许IP
    "servant": "",             // Servant
    "queuecap": 0,             // 队列长度
    "queuetimeout": 0,         // 队列超时时间
    "posttime": "",            // 更新时间
    "protocol": "",            // 协议
    "handlegroup": ""          // 处理组
}
```


### <span id="get-adapter-list"></span>取Adapter列表

/api/adapter\_conf\_list

#### 参数

```text
id // 服务ID
```

#### 返回值

```text
[{
    "id": 0,                   // Adapter ID
    "application": "",         // 应用
    "server_name": "",         // 服务
    "node_name": "",           // 节点
    "adapter_name": "",        // Adapter名
    "thread_num": 1,           // 线程数
    "endpoint": "",            // EndPoint
    "max_connections": 0,      // 最大连接数
    "allow_ip": "",            // 允许IP
    "servant": "",             // Servant
    "queuecap": 0,             // 队列长度
    "queuetimeout": 0,         // 队列超时时间
    "posttime": "",            // 更新时间
    "protocol": "",            // 协议
    "handlegroup": ""          // 处理组
}]
```

## <span id="config"></span>服务配置

### <span id="add-config"></span>新增配置文件

/api/add\_config\_file

#### 参数

```text
{
    "level": 1,         // 层级
    "application": "",  // 应用
    "server_name": "",  // 服务
    "node_name": "",    // 节点
    "set_name": "",     // Set名
    "set_area": "",     // Set取
    "set_group": "",    // Set组
    "filename": "",     // 文件名
    "config": ""        // 文件内容
}
```

#### 返回值

```text
{
    "id": 0,            // 配置文件ID
    "server_name": "",  // 服务
    "node_name": "",    // 节点
    "set_name": "",     // Set名
    "set_area": "",     // Set取
    "set_group": "",    // Set组
    "filename": "",     // 文件名
    "config": "",       // 文件内容
    "level": 1,         // 层级，1：应用或Set，2：服务，3：节点
    "posttime": "",     // 更新时间
}
```

### <span id="del-config"></span>删除配置文件

/api/delete\_config\_file

#### 参数

```text
id // 配置文件ID
```

#### 返回值

```text
[0] // 删除的配置文件ID
```

### <span id="modify-config"></span>修改配置文件

/api/update\_config\_file

#### 参数

```text
{
    "id": 0,            // 配置文件ID
    "config": "",       // 文件内容
    "reason": ""        // 备注
}
```

#### 返回值

```text
{
    "id": 0,            // 配置文件ID
    "server_name": "",  // 服务
    "node_name": "",    // 节点
    "set_name": "",     // Set名
    "set_area": "",     // Set取
    "set_group": "",    // Set组
    "filename": "",     // 文件名
    "config": "",       // 文件内容
    "level": 1,         // 层级，1：应用或Set，2：服务，3：节点
    "posttime": "",     // 更新时间
}
```

#### 备注

只支持POST方式，Header中指定Content-Type:application/json

### <span id="get-config"></span>取配置文件

/api/config\_file

#### 参数

```text
id // 配置文件ID
```

#### 返回值

```text
{
    "id": 0,            // 配置文件ID
    "server_name": "",  // 服务
    "node_name": "",    // 节点
    "set_name": "",     // Set名
    "set_area": "",     // Set取
    "set_group": "",    // Set组
    "filename": "",     // 文件名
    "config": "",       // 文件内容
    "level": 1,         // 层级，1：应用或Set，2：服务，3：节点
    "posttime": "",     // 更新时间
}
```

### <span id="get-config-list"></span>取配置文件列表

/api/config\_file\_list

#### 参数

```text
level       // 层级,1:应用，2：Set名，3：Set区，4：Set组，5：服务
application // 应用
server_name // 服务
set_name    // Set名
set_area    // Set区
set_group   // Set组
```

#### 返回值

```text
[{
    "id": 0,            // 配置文件ID
    "server_name": "",  // 服务
    "node_name": "",    // 节点
    "set_name": "",     // Set名
    "set_area": "",     // Set取
    "set_group": "",    // Set组
    "filename": "",     // 文件名
    "config": "",       // 文件内容
    "level": 1,         // 层级，1：应用或Set，2：服务，3：节点
    "posttime": "",     // 更新时间
}]
```

### <span id="get-node-config-list"></span>取节点配置文件列表

/api/node\_config\_file\_list

#### 参数

```text
application // 应用
server_name // 服务
set_name    // Set名
set_area    // Set区
set_group   // Set组
config_id   // 配置文件ID
```

#### 返回值

```text
[{
    "id": 0,            // 配置文件ID
    "server_name": "",  // 服务
    "node_name": "",    // 节点
    "set_name": "",     // Set名
    "set_area": "",     // Set取
    "set_group": "",    // Set组
    "filename": "",     // 文件名
    "config": "",       // 文件内容
    "level": 1,         // 层级，1：应用或Set，2：服务，3：节点
    "posttime": "",     // 更新时间
}]
```

#### 备注

只支持POST方式，Header中指定Content-Type:application/json

### <span id="get-config-modify-history"></span>取配置文件修改记录

server/api/config\_file\_history

#### 参数

```text
id  // 变更记录ID
```

#### 返回值

```text
{
    "id": "",        // 变更记录ID
    "config_id": "", // 配置文件ID
    "reason": "",    // 备注
    "content": "",   // 变更内容
    "posttime": "",  // 更新时间
}
```

### <span id="get-config-modify-list"></span>取配置文件修改记录列表

server/api/config\_file\_history\_list

#### 参数

```text
config_id // 配置文件ID
```

#### 返回值

```text
{
    "count":0,
    "rows":[{
        "id": "",        // 变更记录ID
        "config_id": "", // 配置文件ID
        "reason": "",    // 备注
        "content": "",   // 变更内容
        "posttime": "",  // 更新时间
    }]
}
```

#### 是否支持分页

是

### <span id="add-ref"></span>新增引用

server/api/add\_config\_ref

#### 参数

```text
config_id    // 配置文件ID
reference_id // 引用配置文件ID
```

#### 返回值

```text
{
    "id": "",           // 引用ID
    "config_id": "",    // 配置文件ID
    "reference_id": ""  // 引用配置文件ID
}
```

### <span id="del-ref"></span>删除引用

/api/delete\_config\_ref

#### 参数

```text
id // 引用ID
```

#### 返回值

```text
[0] // 删除的引用ID
```

### <span id="ref-list"></span>引用列表

/api/config\_ref\_list

#### 参数

```text
config_id // 配置文件ID
```

#### 返回值

```text
[{
    "id": 0,                // 引用ID
    "config_id": 0,         // 配置文件ID
    "reference": {
        "id": 0,            // 配置文件ID
        "server_name": "",  // 服务
        "node_name": "",    // 节点
        "set_name": "",     // Set名
        "set_area": "",     // Set取
        "set_group": "",    // Set组
        "filename": "",     // 文件名
        "config": "",       // 文件内容
        "level": 1,         // 层级，1：应用或Set，2：服务，3：节点
        "posttime": "",     // 更新时间
    }
}]
```

### <span id="merge-node-config"></span>合并后节点配置

/api/merged\_node\_config

#### 参数

```text
id // 配置文件ID
```

#### 返回值

```text
"" // 配置文件内容
```

### <span id="push-config"></span>下发节点配置

/api/push\_config\_file

#### 参数

```text
ids // 配置文件ID，用;分隔
```

#### 返回值

```text
[{
    "application": "",    // 应用
    "server_name": "",    // 服务
    "node_name": "",      // 节点
    "ret_code": 0,        // 执行结果，0成功
    "err_msg": ""         // 错误信息
}]
```

## <span id="task"></span>任务管理

> 涵盖启动、停止、发布、下线

### <span id="add-task"></span>新增任务

/api/add\_task

#### 参数

```text
{
    "serial": true, // 是否串行
    "items": [{
        "server_id": "",    // 服务
        "command": "",     // 命令字
        "parameters": {     // 参数
        }
    }]
}

命令字包括restart，stop，undeploy_tars，patch_tars
当command!=patch_tars时，parameter为空
当command=patch_tars时，parameter格式为
{
    patch_id: "0",    // 版本号
    update_text: "",  // 备注
    bak_flag: true    // 备机标识，true：备机，false：主机
}
```

#### 返回值

```text
""  // 任务ID
```

#### 备注

只支持POST方式，Header中指定Content-Type:application/json

### <span id="get-task"></span>取任务及子任务详细信息

/api/task

#### 参数

```text
task_no // 任务ID
```

#### 返回值

```text
{
    "task_no": "",               // 任务ID
    "serial": true,              // 是否串行
    "status": 0,                 // 任务状态
    "items":[{
        "task_no": "",
        "item_no": "",           // 子任务ID
        "application": "",       // 应用
        "server_name": "",       // 服务
        "node_name": "",         // 节点
        "command": "",           // 命令
        "parameters": {},        // 参数
        "start_time": "",        // 开始时间
        "end_time": "",          // 结束时间
        "status": "",            // 子任务状态
        "status_info": "",       // 状态信息
        "execute_info": ""       // 执行信息
    }]
}
// 如果用了kafka，当任务还在排队时只会返回{status:0}
```

#### 

### <span id="get-task-list"></span>取任务列表

/api/task\_list

#### 参数

```text
application // 应用
server_name // 服务
command     // 命令
from        // 开始日期
to          // 结束日期
```

#### 返回值

```text
[{
    "task_no": "",               // 任务ID
    "serial": true,              // 是否串行
    "status": 0,                 // 任务状态
    "items":[{
        "task_no": "",
        "item_no": "",           // 子任务ID
        "application": "",       // 应用
        "server_name": "",       // 服务
        "node_name": "",         // 节点
        "command": "",           // 命令
        "parameters": {},        // 参数
        "start_time": "",        // 开始时间
        "end_time": "",          // 结束时间
        "status": "",            // 子任务状态
        "status_info": "",       // 状态信息
        "execute_info": ""       // 执行信息
    }]
}]
```

## <span id="publish"></span>发布包

### <span id="upload-publish"></span>上传发布包

/api/upload\_patch\_package

#### 参数

```text
application // 应用
module_name // 模块名
comment     // 备注
suse        // 发布包上传组件名称
task_id     // 任务ID（可用时间戳）
md5         // 发布包的md5值（不填则不校验）
```

#### 返回值

```text
{
    "id": 0,        // 发布包ID
    "server": "",   // 服务，应用+模块名
    "tgz": "",      // 发布包名称
    "comment": "",  // 备注
    "posttime": ""  // 更新时间
}
```

### <span id="upload-and-publish"></span>上传并发布

/api/upload\_and\_publish

#### 参数

```text
application // 应用
module_name // 模块名
comment     // 备注
suse        // 发布包上传组件名称
```

#### 返回值

```text
msg
```

#### 示例(使用curl)

```
curl http://${TARS_WEB_HOST}/api/upload_and_publish?ticket=${TARS_TOKEN} -Fsuse=@${TARGET}.tgz -Fapplication=${APP} -Fmodule_name=${ServerName} -Fcomment=developer-auto-upload
```

### <span id="get-publish-list"></span>取发布版本列表

/api/server\_patch\_list

#### 参数

```text
application // 应用
module_name // 模块名
```

#### 返回值

```text
{
    "count":0,
    "rows":[{
        "id": 0,        // 发布包ID
        "server": "",   // 服务，应用+模块名
        "tgz": "",      // 发布包名称
        "comment": "",  // 备注
        "posttime": ""  // 更新时间
    }]
}
```

#### 是否支持分页

是

## <span id="template"></span>模板

### <span id="get-template"></span>新增模板

server/api/add\_profile\_template

#### 参数

```text
{
	"template_name": "",      // 必填，模板名称
	"parents_name": "",       // 必填，父模板
	"profile": ""             // 必填，模板内容
}
```

#### 返回值

```text
{
	"id": 0,                  // 模板ID
	"template_name": "",      // 模板名称
	"parents_name": "",       // 父模板
	"profile": "",            // 模板内容
	"posttime": ""            // 更新时间
}
```

### <span id="del-template"></span>删除模板

/api/delete\_profile\_template

#### 参数

```text
id  // 模板ID
```

#### 返回值

```text
[0] // 删除的模板ID
```

### <span id="modify-template"></span>修改模板

server/api/update\_profile\_template

#### 参数

```text
{
	"id": "",                 // 必填，模板ID
	"template_name": "",      // 必填，模板名称
	"parents_name": "",       // 必填，父模板
	"profile": ""             // 必填，模板内容
}
```

#### 返回值

```text
{
	"id": 0,                  // 模板ID
	"template_name": "",      // 模板名称
	"parents_name": "",       // 父模板
	"profile": "",            // 模板内容
	"posttime": ""            // 更新时间
}
```

### <span id="get-template"></span>取模板

/api/profile\_template

#### 参数

```text
template_name // 模板名称
```

#### 返回值

```text
{
	"id": 0,                  // 模板ID
	"template_name": "",      // 模板名称
	"parents_name": "",       // 父模板
	"profile": "",            // 模板内容
	"posttime": ""            // 更新时间
}
```

### <span id="query-template"></span>查询模板

/api/query\_profile\_template

#### 参数

```text
template_name  // 模板名称
parents_name   // 父模板名称
```

#### 返回值

```text
[{
	"id": 0,                  // 模板ID
	"template_name": "",      // 模板名称
	"parents_name": "",       // 父模板
	"profile": "",            // 模板内容
	"posttime": ""            // 更新时间
}]
```

## <span id="monitor"></span>监控

### <span id="get-tarsstat"></span>取tarsstat监控数据

/api/tarsstat\_monitor\_data

#### 参数

```text
thedate         // 显示日期
predate         // 对比日期
startshowtime   // 开始时间
endshowtime     // 结束时间
master_name     // 主调
slave_name      // 被调
interface_name  // 接口名
master_ip       // 主调IP
slave_ip        // 被调IP
group_by        // 分组
```

#### 返回值

```text
[{
    "show_date": "",          // 日期
    "show_time": "",          // 时间点
    "master_name": "",        // 主调
    "slave_name": "",         // 被调
    "interface_name": "",     // 接口名
    "master_ip": "",          // 主调IP
    "slave_ip": "",           // 被调IP
    "the_total_count": "",    // 当日总流量
    "pre_total_count": "",    // 对比日总流量
    "total_count_wave": "",   // 流量同比波动
    "the_avg_time": "",       // 当日平均耗时
    "pre_avg_time": "",       // 对比日平均耗时
    "the_fail_rate": "",      // 当日失败率
    "pre_fail_rate": "",      // 对比日失败率
    "the_timeout_rate": "",   // 当日失败率
    "pre_timeout_rate": ""    // 对比日失败率
}]
```

### <span id="get-tarsproperty"></span>取tarsproperty监控数据

/api/tarsproperty\_monitor\_data

#### 参数

```text
thedate         // 显示日期
predate         // 对比日期
startshowtime   // 开始时间
endshowtime     // 结束时间
master_name     // 服务名
master_ip       // IP
property_name   // 特性
policy          // 策略
```

#### 返回值

```text
[{
    "show_date": "",          // 日期
    "show_time": "",          // 时间点
    "master_name": "",        // 服务名
    "master_ip": "",          // IP
    "property_name": "",      // 特性
    "policy": "",             // 策略
    "the_value": "",          // 当日特征值
    "pre_value": ""           // 对比日特征值
}]
```

## <span id="dick"></span>字典

### <span id="get-server-type"></span>取服务类型列表

/api/server\_type\_list

#### 参数

无

#### 返回值

```text
["tars_cpp"]
```

### <span id="get-template-list"></span>取模板列表

/api/template\_name\_list

#### 参数

无

#### 返回值

```text
["tars.default"]
```

### <span id="choose-server"></span>级联选择服务

/api/cascade\_select\_server

#### 参数

```text
level        // 层级，1：应用，2：服务，3：Set，4：节点
application  // 应用，level>1时必填
server_name  // 服务，level>2时必填
set          // Set，level>3时必填，格式：Set名.Set区.Set组
```

#### 返回值

```text
[""] 对应层级数据
```

## <span id="resource"></span>资源

### <span id="install-tarsnode"></span>安装Tars Node

/server/api/install\_tars\_node

#### 参数

```text
ips	//需要安装tarsnode的机器IP
```

#### 返回值

```text
[
	{
        "ip": "",  //机器IP
        "rst": true, //安装结果
		"msg":"" //安装结果信息
    }
]
```

### <span id="uninstall-tarsnode"></span>卸载Tars Node

/api/uninstall\_tars\_node

#### 参数

```text
ips	//需要卸载tarsnode的机器IP
```

#### 返回值

```text
[
	{
        "ip": "",  //机器IP
        "rst": true, //卸载结果
		"msg":"" //卸载结果信息
    }
]
```

## <span id="other"></span>其他

### <span id="send-command"></span>发送自定义命令
 
/api/send\_command

#### 参数

```text
server_ids // 服务ID
command    // 命令
```

#### 返回值

```text
[{
	"application": "", // 应用
	"server_name": "", // 服务
	"node_name": "",   // 节点
	"ret_code": "",    // 返回值
	"err_msg": ""      // 错误消息
}]
```

### <span id="get-port"></span>自动获取未被占用的端口

/api/auto\_port

#### 参数

```text
node_name // 机器IP，用；隔开
```

#### 返回值

```text
[{
	"node_name": "", //机器IP
	"port": "", // 端口
}]
```

