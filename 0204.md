#02.04 Composer

如今的PHP社区，Composer已经替代了过往所有的模块/包管理、安装系统。


##获得Composer

要获得Composer，需要在命令行输入如下命令：

```
curl -sS https://getcomposer.org/installer | php
```

**NOTE:** 这个命令需要`curl`的支持。

执行上述命令后，一个名为`composer.phar`的文件就会下载到当前目录。

##安装/更新应用所需的包

`composer.phar`需要一个`composer.json`文件配合，来查找、安装、更新一个应用所需要的包。

一个最简单的`composer.json`文件可以只有这么几行：

```
{
	"require": {
		"silex/silex": "~1.2",
		"twig/twig": ">=1.8, <2.0-dev",
		"doctrine/dbal": "2.2.*",
		"symfony/twig-bridge": "~2.3",
		"symfony/form": "~2.3",
		"symfony/config": "~2.3",
		"symfony/translation": "~2.3",
		"symfony/locale": "~2.3"
	}
}
```

这是一个典型的使用[Silex](http://silex.sensiolabs.org/)框架（另一个由SF2开发者开发的轻量级PHP框架）的应用的包依赖关系描述。

创建好这个文件后，我们可以用：`php composer.phar update`这个命令开始安装、更新我们这个应用使用到的包。

使用Composer的更多细节，可以参考[官方文档](https://getcomposer.org/doc/)。

