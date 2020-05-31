# Tars Nodejs 脚手架及自动化发布工具  
此工具提供Tars Nodejs 脚手架、自动打包发布的功能。  
通过此工具，可以通过简单的3~4个步骤来开发、发布一个tars服务。  
## 1. 安装  
`npm install -g @tars/nodetools-cli`
## 2. 使用
### 创建服务  
参考tars的[Hello World文档](https://github.com/TarsCloud/TarsDocs/tree/master/hello-world)创建一个tars服务。  
注意选择正确的协议类型， tars rpc服务选择`tars协议`， http服务选择`非tars协议`。  

### 初始化  
`nodetools init`  
根据需要设置选项，其中application、server、obj，必须和创建服务的步骤中一致。  

- 语言支持：javascript、typescript
- 协议支持：http、tars
- Http框架支持：koa、express、Nodejs原生http模块  


### 开发  
在初始化项目生成的目录下，http协议服务可以直接`npm run dev` 启动服务，开发业务逻辑；  

tars协议服务首先需要编写协议文件、转换为js/ts代码，按代码中提示配置好相应的`imp对象`，再`npm run dev`启动服务。

### 打包发布  

此工具提供tars nodejs服务自动打包发布的功能，根据提示输入tars平台地址、tars用户中心中创建的token，即可自动打包、发布服务。其中token可根据实际情况选择保存或者不保存到package.json中，非敏感项目或者环境可选择保存。

http协议服务，项目初始化完毕后可直接运行 `npm run upload` 将服务发布至Tars平台，浏览器中访问 `/hello` 路径即可看到测试输出。  

tars协议服务，需要先转换tars协议文件，实现业务逻辑后运行  `npm run upload` 发布至Tars平台。  

非本工具脚手架创建的项目，可在根目录下运行 `nodetools upload` 来自动打包发布，也可以自行编辑package.json中的script来使用此工具。  