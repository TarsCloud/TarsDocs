# 一键部署

## 在机器上安装mysql, 并修改文件comm.properties中的db信息.

建议使用Mysql `5.6.26`, 以上的版本也是可以的.

```text
[tarscomm]
mysql.host=127.0.0.1
mysql.port=3306
mysql.root.password=tars12345
```

大于5.6版本的Mysql增加了密码验证插件， 导致太简单的密码是无法创建的, 可以通过以下命令修改数据库密码验证配置来创建简单密码:

```text
set global validate_password_policy=0;
set global validate_password_length=1;
```

同时确保mysql路径是正确的, 请看到 [注意事项 6](yi-jian-bu-shu.md#zhu-yi-shi-xiang)

## 使用脚本部署

可以使用以下命令运行脚本部署项目

```text
cd /data
git clone https://github.com/TarsCloud/Tars.git --recursive
cd /data/Tars/deploy
python ./deploy.py all
```

## 参数配置

```text
python ./deploy.py [option]

[option]
    all:
        check()     //检查需要的工具包是否已经安装
        build()     //编译并构建框架
        deploy()    //初始化数据库并部署框架和web管理系统
        test()      //测试所有服务是否已经成功启动
    check:
        check()
    build:
        build()
    deploy:
        deploy()
    test:
        test()
```

## 注意事项

1. 安装mysql而不是mariadb （centos7 命令 `yum install mysql` 会自动安装mariadb\)
2. 为了能够成功构建, 确保机器内存RAM &gt;= 2G, 虚拟内存也是支持的.
3. 支持的 python 版本为 2.7
4. 确保网络无障碍
5. 你需要安装: gcc,gcc-c++, cmake, yasm, glibc-devel, flex, bison, ncurses-devel, zlib-devel, autoconf, 如果未安装, 脚本会自动安装, but 但有可能会失败.
6. Tars 使用 `/usr/local/mysql/` 作为默认路径. 如果你的机器mysql路径不同, 需要在编译之前编辑文件 CMakeLists.txt \(`framework/tarscpp/CMakeLists.txt`, `framework/CMakeLists.txt`\).
7. 一部分CentOS7的网卡名字不是 `eth0` 而是 `ens33`, 需要更改以下文件中的网卡名: `Tars/deploy/comm/tarsUtil.py :line 24: getAddress()`
8. `Error: npm command not found`, 删除 `source /etc/profile` 位于 `Tars/ deploy/comm/tarsDeploy.py: line 70: deployWeb()` 并重试.
9. `Error: tarsDeploy.py: line 51 :shutil.copy: no such file or directory`, 可能是由于build失败导致的, 重新看注意事项 `2`.
10. 安装后为了提供更好的安全防护，强烈建议开启[用户体系鉴权登陆模块](https://github.com/TarsCloud/TarsWeb/blob/master/docs/TARS%20%E7%94%A8%E6%88%B7%E4%BD%93%E7%B3%BB%E6%A8%A1%E5%9D%97%2B%E8%B5%84%E6%BA%90%E6%A8%A1%E5%9D%97%E4%BD%BF%E7%94%A8%E6%8C%87%E5%BC%95.md)。
