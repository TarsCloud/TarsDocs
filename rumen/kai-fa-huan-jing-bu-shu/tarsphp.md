# TarsPHP

## 依赖环境

建议安装：

```text
php7+
swoole2+
```

## 安装PHP

```text
项目地址 https://github.com/php/php-src 
```

### 安装swoole

```text
项目地址 https://github.com/swoole/swoole-src
编译安装 https://wiki.swoole.com/wiki/page/6.html
PECL一键下载安装
    pecl install swoole
```

### 安装tarsphp扩展

项目地址 [https://github.com/TarsPHP/tars-extension](https://github.com/TarsPHP/tars-extension)

```text
git clone https://github.com/TarsPHP/tars-extension.git
cd tars-extension
sudo phpize 
sudo ./configure
sudo make 
sudo make install
```

在php.ini里面加入extension=phptars.so





