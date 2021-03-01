# 目录
> * [介绍](#chapter-1)
> * [安装PHP](#chapter-2)
> * [安装swoole](#chapter-3)
> * [安装tarsphp扩展](#chapter-4)


# <span id="chapter-1"></span>介绍

php语言相关 建议安装php7+swoole2

# <span id="chapter-2"></span>安装PHP

```text
项目地址 https://github.com/php/php-src 
```

# <span id="chapter-3"></span>安装swoole

```text
项目地址 https://github.com/swoole/swoole-src
编译安装 https://wiki.swoole.com/wiki/page/6.html
PECL一键下载安装
    pecl install swoole
```

php需要安装扩展，请在 [https://github.com/TarsPHP/tars-extension](https://github.com/TarsPHP/tars-extension) 源码目录进行编译，然后将扩展加入到php.ini中

# <span id="chapter-4"></span>安装tarsphp扩展

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





