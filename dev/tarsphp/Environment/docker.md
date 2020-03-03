# 快速安装运行docker-tars

* 参考tars-docker https://github.com/tangramor/docker-tars
* 先运行mysql容器，再运行tars容器

```
    mkdir -p /data/tars/mysql_data
    mkdir -p /data/tars/tars_data
    docker run --name mysql -e MYSQL_ROOT_PASSWORD=数据库密码 -d -p 3306:3306 -v /data/tars/mysql_data:/var/lib/mysql mysql:5.6 --innodb_use_native_aio=0
    docker run -d -it --name tars --link mysql --env MOUNT_DATA=false --env DBIP=mysql --env DBPort=3306 --env DBUser=root --env DBPassword=数据库密码 -p 3000:3000 -v /data/tars/tars_data:/data tarscloud/tars:php
```

