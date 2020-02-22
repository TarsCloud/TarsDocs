# tars-deploy说明文档

## 简介
tars-deploy用来打包项目，方便在tars管理平台发布的工具组件。

## 使用说明
1. 依赖
    - Phar扩展
2. 保证项目下面的./tars/tars.proto.php 文件的存在，并且包含下面内容
    ```php
        return array(
            'appName' => 'APPNAME', //app名称 如tars.tarsconfig中的tars
            'serverName' => 'SERVERNAME', //服务名 如tars.tarsconfig中的tarsconfig
            ...
        );
    ```
3. 引入tars-deploy到项目的 composer.json 里面。
4. 在项目composer.json里面加入下面内容：
    ```
        "scripts": {
            "deploy": "\\Tars\\deploy\\Deploy::run"
        }
    ```
5. 在项目目录执行下面命令，生成待上传的项目压缩包。
    ```
        composer run-script deploy
    ```