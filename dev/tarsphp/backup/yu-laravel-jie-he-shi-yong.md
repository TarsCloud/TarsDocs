# 与Laravel结合使用

## 简介

Laravel集成微服务治理框架Tars

* 支持服务打包
* 支持Laravel/Lumen原生开发
* 支持TarsConfig
* 支持TarsLog
* 支持网关注册下线
* 支持请求开始\(laravel.tars.requesting\)、请求结束\(laravel.tars.requested\)事件
* 支持echo输出内容
* 支持http和tars协议
* 支持zipkin分布式追踪\(已移除，可以使用[laravel-zipkin扩展包](https://github.com/luoxiaojun1992/laravel-zipkin)\)
* 受限于框架本身的设计，不支持协程

## 相关项目

* LaravelTars \([https://github.com/luoxiaojun1992/laravel-tars](https://github.com/luoxiaojun1992/laravel-tars)\)
* LaravelTarsDemo \([https://github.com/luoxiaojun1992/laravel-tars-demo](https://github.com/luoxiaojun1992/laravel-tars-demo)\)
* LumenTarsDemo \([https://github.com/luoxiaojun1992/lumen-tars-demo](https://github.com/luoxiaojun1992/lumen-tars-demo)\)
* Laravel5.x \([https://github.com/laravel/framework](https://github.com/laravel/framework)\)
* Lumen5.x \([https://github.com/laravel/lumen](https://github.com/laravel/lumen)\)
* TARSPHP \([https://github.com/TarsPHP](https://github.com/TarsPHP)\)
* TARSPHP DOCKER \([https://github.com/tangramor/docker-tars](https://github.com/tangramor/docker-tars)\)

## 使用

1. 创建项目

   创建Tars项目目录结构\(scripts、src、tars\)，Laravel/Lumen项目放在src目录下

2. 安装Laravel Tars包

   更新Composer依赖

   ```text
   composer require "luoxiaojun1992/laravel-tars:*"
   ```

   或添加 requirement 到 composer.json

   ```javascript
   {
     "require": {
       "luoxiaojun1992/laravel-tars": "*"
     }
   }
   ```

   添加ServiceProvider，编辑src/bootstrap/app.php

   ```php
   $app->register(\Lxj\Laravel\Tars\ServiceProvider::class);
   ```

   初始化Laravel Tars

   ```text
   php artisan vendor:publish --tag=tars
   ```

3. 修改配置文件src/config/tars.php文件proto字段，替换appName、serverName、objName
4. 如果使用http协议，且需要自动注册到网关\(目前仅支持Kong\)，修改配置文件src/config/tars.php

   ```php
   'registries' => [
        [
            'type' => 'kong',
            'url' => 'http://kong:8001/upstreams/tars_mysql8/targets', //根据实际情况填写
        ]
   ]
   ```

5. 配置中心\(TarsConfig\)或者日志服务\(TarsLog\)

   服务启动时会自动拉取配置，如果需要记录日志，可以使用类似`Log::info('test log');`

   如果需要指定TarsLog记录的最低日志级别，修改配置文件src/config/tars.php

   ```php
   'log_level' => \Monolog\Logger::INFO
   ```

6. 如果使用http协议，按框架原生方式编写代码，路由前缀必须为/Laravel/route

   ```php
   $router->group(['prefix' => '/Laravel/route'], function () use ($router) {
    $router->get('/test', function () {
        \Illuminate\Support\Facades\Log::info('laravel tars test log');
        return 'Laravel Tars Test Success';
    });
   });
   ```

7. 如果使用tars协议

   在tars目录下编写tars接口描述文件，修改配置文件src/config/tars文件proto字段，新增tarsFiles

   在scripts目录执行编译脚本生成接口代码

   ```text
   /bin/bash tars2php.sh
   ```

   在src/app/Tars/impl目录下创建接口实现类，编写业务逻辑代码

   修改src/config/tars.php文件services字段，替换接口和接口实现命名空间

8. 搭建Tars-PHP开发环境

   如果使用http协议，请参考[TARS-PHP-HTTP服务端与客户端开发](https://tangramor.gitlab.io/tars-docker-guide/3.TARS-PHP-HTTP%E6%9C%8D%E5%8A%A1%E7%AB%AF%E4%B8%8E%E5%AE%A2%E6%88%B7%E7%AB%AF%E5%BC%80%E5%8F%91/)

   如果使用tars协议，请参考[TARS-PHP-TCP服务端与客户端开发](https://tangramor.gitlab.io/tars-docker-guide/2.TARS-PHP-TCP%E6%9C%8D%E5%8A%A1%E7%AB%AF%E4%B8%8E%E5%AE%A2%E6%88%B7%E7%AB%AF%E5%BC%80%E5%8F%91/)

9. 在Tars-PHP开发环境下打包项目\(在src目录下执行`php artisan tars:deploy`\)
10. 在Tars管理后台发布项目，请参考[TARS-PHP-TCP服务端与客户端开发](https://tangramor.gitlab.io/tars-docker-guide/2.TARS-PHP-TCP%E6%9C%8D%E5%8A%A1%E7%AB%AF%E4%B8%8E%E5%AE%A2%E6%88%B7%E7%AB%AF%E5%BC%80%E5%8F%91/)\)，测试`curl 'http://{ip}:{port}/Laravel/route/{api_route}'`

## 持续集成

Jenkins Pipeline 配置示例\(根据实际情况修改\)

Laravel:

```text
pipeline {
    agent {
        node {
            label 'phpenv'
        }
    }
    parameters {
        string(defaultValue: 'upload_from_jenkins', name: 'TAG_DESC', description: '发布版本描述' )
        string(defaultValue: 'master', name: 'BRANCH_NAME', description: 'git分支，如：develop,master  默认: master')
    }
    environment {
        def JENKINS_HOME = "/root/jenkins"
        def PROJECT_ROOT = "$JENKINS_HOME/workspace/laravel-tars-demo"
        def APP_NAME = "PHPTest"
        def SERVER_NAME = "LaravelTars"
    }
    stages {
        stage('代码拉取与编译'){
            steps {
                echo "checkout from git"
                git credentialsId:'2', url: 'https://gitee.com/lb002/laravel-tars-demo', branch: "${env.BRANCH_NAME}"
                script {
                    dir("$PROJECT_ROOT/src") {
                        echo "Composer Install"
                        sh "composer clear-cache"
                        sh "composer install -vvv"
                        sh "cp .env.example .env"
                        sh "php artisan config:clear"
                        sh "php artisan config:cache"
                    }
                }
            }
        }
        stage('单元测试') {
            steps {
                script {
                    dir("$PROJECT_ROOT/src") {
                        echo "phpunit 测试"
                        sh "vendor/bin/phpunit tests/"
                        echo "valgrind 测试"
                    }
                }
            }
        }
        stage('覆盖率测试') {
            steps {
                echo "LCOV 覆盖率测试"
            }
        }
        stage('打包与发布') {
            steps {
                script {
                    dir("$PROJECT_ROOT/src") {
                        echo "打包"
                        sh "php artisan tars:deploy"
                        echo "发布"
                        sh "ls *.tar.gz > tmp.log"
                        echo "上传build包"
                        def packageDeploy = sh(script: "head -n 1 tmp.log", returnStdout: true).trim()
                        sh "curl -H 'Host:172.18.0.3:3000' -F 'suse=@./${packageDeploy}' -F 'application=${APP_NAME}' -F 'module_name=${SERVER_NAME}' -F 'comment=${env.TAG_DESC}' http://172.18.0.3:3000/pages/server/api/upload_patch_package > curl.log"
                        echo "发布build包"
                        def packageVer = sh(script: "jq '.data.id' curl.log", returnStdout: true).trim()
                        def postJson = '{"serial":true,"items":[{"server_id":"34","command":"patch_tars","parameters":{"patch_id":' + packageVer + ',"bak_flag":false,"update_text":"${env.TAG_DESC}"}}]}'
                        echo postJson
                        sh "curl -H 'Host:172.18.0.3:3000' -H 'Content-Type:application/json' -X POST --data '${postJson}' http://172.18.0.3:3000/pages/server/api/add_task"
                    }
                }
            }
        }
    }
    post {
        success {
            emailext (
                subject: "[jenkins]构建通知：${env.JOB_NAME} 分支: ${env.BRANCH_NAME} - Build# ${env.BUILD_NUMBER} 成功  !",
                body: '${SCRIPT, template="groovy-html.template"}',
                mimeType: 'text/html',
                to: "luoxiaojun1992@sina.cn",
            )
            cleanWs()
        }
        failure {
            emailext (
                subject: "[jenkins]构建通知：${env.JOB_NAME} 分支: ${env.BRANCH_NAME} - Build# ${env.BUILD_NUMBER} 失败 !",
                body: '${SCRIPT, template="groovy-html.template"}',
                mimeType: 'text/html',
                to: "luoxiaojun1992@sina.cn",
            )
            cleanWs()
        }
    }
}
```

Lumen:

```text
pipeline {
    agent {
        node {
            label 'phpenv'
        }
    }
    parameters {
        string(defaultValue: 'upload_from_jenkins', name: 'TAG_DESC', description: '发布版本描述' )
        string(defaultValue: 'master', name: 'BRANCH_NAME', description: 'git分支，如：develop,master  默认: master')
    }
    environment {
        def JENKINS_HOME = "/root/jenkins"
        def PROJECT_ROOT = "$JENKINS_HOME/workspace/lumen-tars-demo"
        def APP_NAME = "PHPTest"
        def SERVER_NAME = "LumenTars"
    }
    stages {
        stage('代码拉取与编译'){
            steps {
                echo "checkout from git"
                git credentialsId:'2', url: 'https://gitee.com/lb002/lumen-tars-demo', branch: "${env.BRANCH_NAME}"
                script {
                    dir("$PROJECT_ROOT/src") {
                        echo "Composer Install"
                        sh "composer clear-cache"
                        sh "composer install -vvv"
                        sh "cp .env.example .env"
                    }
                }
            }
        }
        stage('单元测试') {
            steps {
                script {
                    dir("$PROJECT_ROOT/src") {
                        echo "phpunit 测试"
                        sh "vendor/bin/phpunit tests/"
                        echo "valgrind 测试"
                    }
                }
            }
        }
        stage('覆盖率测试') {
            steps {
                echo "LCOV 覆盖率测试"
            }
        }
        stage('打包与发布') {
            steps {
                script {
                    dir("$PROJECT_ROOT/src") {
                        echo "打包"
                        sh "php artisan tars:deploy"
                        echo "发布"
                        sh "ls *.tar.gz > tmp.log"
                        echo "上传build包"
                        def packageDeploy = sh(script: "head -n 1 tmp.log", returnStdout: true).trim()
                        sh "curl -H 'Host:172.18.0.3:3000' -F 'suse=@./${packageDeploy}' -F 'application=${APP_NAME}' -F 'module_name=${SERVER_NAME}' -F 'comment=${env.TAG_DESC}' http://172.18.0.3:3000/pages/server/api/upload_patch_package > curl.log"
                        echo "发布build包"
                        def packageVer = sh(script: "jq '.data.id' curl.log", returnStdout: true).trim()
                        def postJson = '{"serial":true,"items":[{"server_id":"33","command":"patch_tars","parameters":{"patch_id":' + packageVer + ',"bak_flag":false,"update_text":"${env.TAG_DESC}"}}]}'
                        echo postJson
                        sh "curl -H 'Host:172.18.0.3:3000' -H 'Content-Type:application/json' -X POST --data '${postJson}' http://172.18.0.3:3000/pages/server/api/add_task"
                    }
                }
            }
        }
    }
    post {
        success {
            emailext (
                subject: "[jenkins]构建通知：${env.JOB_NAME} 分支: ${env.BRANCH_NAME} - Build# ${env.BUILD_NUMBER} 成功  !",
                body: '${SCRIPT, template="groovy-html.template"}',
                mimeType: 'text/html',
                to: "luoxiaojun1992@sina.cn",
            )
            cleanWs()
        }
        failure {
            emailext (
                subject: "[jenkins]构建通知：${env.JOB_NAME} 分支: ${env.BRANCH_NAME} - Build# ${env.BUILD_NUMBER} 失败 !",
                body: '${SCRIPT, template="groovy-html.template"}',
                mimeType: 'text/html',
                to: "luoxiaojun1992@sina.cn",
            )
            cleanWs()
        }
    }
}
```

