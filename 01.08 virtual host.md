# 1.4 安装PHP 

本书所讨论的Symfony框架最低需要PHP 5.3的支持。一般而言，在我们刚[安装好的Ubuntu盒子](01.03 install ubuntu.md)中，其提供的PHP安装版本已经可以满足这个要求，但可能不是最新的，一般会是在5.4或者5.5这个版本。

如果你需要安装最新版本的PHP（5.6.x），那么需要对apt源进行一些修改。

完整的步骤可以参见[这个帖子](http://phpave.com/upgrade-to-php-56-on-ubuntu-1404-lts/)。

简单地说，有如下四个步骤：

1. `sudo apt-get update && sudo apt-get install python-software-properties`，安装`python-software-properties`这个应用。
2. `sudo add-apt-repository ppa:ondrej/php5-5.6`，添加PPA来源。
3. `sudo apt-get update && sudo apt-get upgrade`，重新更新apt。
4. `sudo apt-get install php5`。如果之前你没有安装过PHP，就执行这个命令。

如果一切顺利，在终端中执行`php -v`会提示如下信息：

~~~
PHP 5.6.4-1+deb.sury.org~trusty+1 (cli) (built: Dec 21 2014 19:28:16)
Copyright (c) 1997-2014 The PHP Group
Zend Engine v2.6.0, Copyright (c) 1998-2014 Zend Technologies
    with Zend OPcache v7.0.4-dev, Copyright (c) 1999-2014, by Zend Technologies
~~~

至此，PHP安装告一段落。我们将在稍后的章节继续配置。