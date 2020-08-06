# 用户体系模块使用指引


TARS管理平台提供了与用户体系（包括单点登录系统和权限系统）对接的能力，若用户本身无相应的系统，TARS可提供了一个简易的用户体系模块，供用户选装。用户体系模块提供了单点登录和注册的功能，以及到服务层级的权限控制能力。用户也可以选择只使用其中一个功能。

## tars-web >= v2.4.8

tars-web可以直接对接到LDAP服务完成用户的登录鉴权, 你只需要在用户中心 => 设置中, 编辑LDAP参数, 设置好LDAP的地址, 并开启LDAP即可.

这样, 如果你的公司和企业统一采用LDAP来完成鉴权, 你可以让tars web连接到你的LDAP完成统一的鉴权.

## tars-web < v2.4.7

tars-web < v2.4.7的版本你可以实现自己独立的用户鉴权模块, 但是操作起来相对复杂, 建议使用>=v2.4.8的版本, 直接对接到LDAP即可

1）默认登录模块：提供基础的注册和登录页面，对外提供获取用户信息的接口getUidByTicket和校验是否登录的接口validate， getUidByTicket接口接收一个ticket参数，返回uid validate接口接收ticket参数和uid参数，返回result（true或false）； 

2）默认权限模块：权限模块数据库表中有三个字段，为flag，role和uid，分别对应标志（在TARS平台中为“应用+服务”表示一个标志），角色和用户。

权限模块对外提供6个接口和一个页面：

```text
    /auth/addAuth： 批量新增权限接口，入参为[{flag: “”，role: “”，uid: “”}],
    /auth/deleteAuth：删除权限接口，入参为flag，删除flag下所有权限信息
    /auth/updateAuth：更新权限接口，入参为flag，role，uid，其中，uid为用户列表，表示更新某个flag和role下的所有用户信息。
    /auth/getAuthListByUid：获取某用户具有的全部权限列表，入参为uid
    /auth/getAuth：判断用户是否具有权限，入参为flag，role，uid。
    /auth/getAuthListByFlag：获取有某个flag权限的用户信息，入参为flag
	权限模块还提供了一个管理页面：
	/auth.html：用于对权限进行增删改查。
```

注意：默认的权限模块，为保证系统安全性，以上的列的6个接口，必须采用白名单的方式访问，不允许被其他人随意调用，管理页面Auth.html 需系统管理员才可使用。白名单和管理员的相关配置，可在/config/authConf.js中配置。

### **TARS对接登录模块能力** 

TARS 通过配置文件/config/loginConf.js 与 第三方登录体系，或默认用户体系登录模块关联，提供允许用户登录的能力。 其中，登录配置文件详细信息如下：

```text
 {
    enableLogin: true,                  //是否启用登录验证
    defaultLoginUid: 'admin',             //若不启用登录验证，默认用户为admin
    loginUrl: 'http://localhost/login.html',   //跳转到的登录url
    redirectUrlParamName: 'service',        //跳转到登录url的时带原url的参数名，如：***/login?service=\*\*\*，默认是service
    logoutUrl: '',                    //登出的url
    logoutredirectUrlParamName: 'url',      //跳转到登录url的时带原url的参数名，与登录的参数功能一致
    ticketCookieName: 'ticket',           //cookie中保存ticket信息的cookie名
    uidCookieName: 'uid',               //cookie中保存用户名信息的cookie名
    cookieDomain: 'localhost',           //cookie值对应的域，若为空，则为当前管理平台所在域
    ticketParamName: 'ticket',           //第三方登录服务登录成功回调时候，url中表示ticket的参数名
    getUidByTicket: 'http://localhost/getUidByTicket',  
	//此处可以为一个url，或一个函数方法，通过ticket从登录服务端校验和获取用户基本信息的url 或 获取用户基本信息的方法。若是函数方法，则以下getUidByTicketParamName和uidKey参数无效，方法直接返回用户名即可
    getUidByTicketParamName: 'ticket',      //调用获取用户信息接口时候ticket的参数名
    uidKey: 'data.uid',                //结果JSON里面取出用户名的位置，取到该用户名才认为成功,可以多层，示例中表示从返回值的data对象中取uid。
    validate: 'http://example.com/api/validate',  
	//通过token和用户名到登录服务端校验key和用户名是否匹配的url或方法，若是函数方法，则以下validateTicketParamName,validateUidParamName和validateMatch参数无效，方法直接返回true或false表示是否通过登录即可。

    validateTicketParamName: 'ticket',      //校验接口传入ticket参数名
    validateUidParamName: 'uid',          //校验接口传入用户参数名
    validateMatch: [
        ['data.result', true]
    ],                           //校验通过匹配条件，可以从多层结果，多个情况进行匹配判断
    ignore: ['/static', '/tarsnode.tar.gz'],  //不需要登录校验的路径段
    ignoreIps: [],                   //访问ip白名单，在白名单内的ip，可直接跳过登录校验
    apiPrefix: ['/pages/server/api'],      //接口相应的路径前缀，此类前缀请求，若无登录，不跳转到登录页面，而是提示未登录
    apiNotLoginMes: '您尚未登录',        //接口无登录时的提示语
}
```

### **TARS对接权限模块能力** 

TARS 通过配置文件/config/authConf.js 与 第三方权限系统，或默认用户体系权限模块关联，提供权限控制的能力。 其中，权限配置文件详细信息如下（接口入参和出参与上述第1点用户体系权限模块一致）：

```text
{

    /**
     * 是否启用自定义权限模块
     */
    enableAuth: false,

    
    /**
     * addAuthUrl             新增权限url
     * TARS平台会提供的参数
     * @param   {Array}    auth         权限对象列表，格式如 {"flag": "app-server", "role": "operator", "uid": "username"}
     */
    /**
     * 接口需要返回的参数
     * @param   {Number}    ret_code            返回码，200表示成功
     * @param   {String}    err_msg             错误信息
     */
    addAuthUrl: 'http://localhost/api/auth/addAuth',

    /**
     * deleteAuthUrl             删除权限url，用于服务下线时候删除权限
     * TARS平台会提供的参数
     * @param   {String}    flag                权限单位，在tars中为“应用-服务”
     */
    /**
     * 接口需要返回的参数
     * @param   {Number}    ret_code            返回码，200表示成功
     * @param   {String}    err_msg             错误信息
     */
    deleteAuthUrl: 'http://localhost/api/auth/deleteAuth',

    /**
     * updateAuthUrl             更新权限url
     * TARS平台会提供的参数
     * @param   {String}    flag                权限单位，在tars中为“应用-服务”
     * @param   {String}    role                角色，在tars中为operator或developer
     * @param   {String}    uid                 用户名
     */
    /**
     * 接口需要返回的参数
     * @param   {Number}    ret_code            返回码，200表示成功
     * @param   {String}    err_msg             错误信息
     */
    updateAuthUrl: 'http://localhost/api/auth/updateAuth',

    /**
     * getAuthListByUidUrl             通过用户名获取权限列表url
     * TARS平台会提供的参数
     * @param   {String}    uid                 用户名
     */
    /**
     * 接口需要返回的参数
     * @param   {Array}     data                服务列表，内容如下
     *        @param   {String}    flag                权限单位，在tars中为“应用-服务”
     *        @param   {String}    role                角色，在tars中为operator或developer
     *        @param   {String}    uid                 用户名
     * @param   {Number}    ret_code            返回码，200表示成功
     * @param   {String}    err_msg             错误信息
     */
    getAuthListByUidUrl: 'http://localhost/api/auth/getAuthListByUid',

    /**
     * getAuthListByFlagUrl             通过应用名+服务名获取用户列表url
     * TARS平台会提供的参数
     * @param   {String}    flag                 应用+服务名
     */
    /**
     * 接口需要返回的参数
     * @param   {Array}     data                服务列表，内容如下
     *        @param   {String}    flag                权限单位，在tars中为“应用-服务”
     *        @param   {String}    role                角色，在tars中为operator或developer
     *        @param   {String}    uid                 用户名
     * @param   {Number}    ret_code            返回码，200表示成功
     * @param   {String}    err_msg             错误信息
     */
    getAuthListByFlagUrl: 'http://localhost/api/auth/getAuthListByFlag',

    /**
     * getAuthUrl             判断用户是否有相应角色的操作权限
     * TARS平台会提供的参数
     * @param   {String}    flag                权限单位，在tars中为“应用-服务”
     * @param   {String}    role                角色，在tars中为operator或developer
     * @param   {String}    uid                 用户名
     */
    /**
     * 接口需要返回的参数
     * @param   {Object}     data                服务列表，内容如下
     *        @param   {Boolean}    result              是否有操作权限
     * @param   {Number}    ret_code            返回码，200表示成功
     * @param   {String}    err_msg             错误信息
     */
    getAuthUrl: 'http://localhost/api/auth/getAuth'
}
```

