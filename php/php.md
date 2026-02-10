# 原理

解释型语言

与其说是变成语言不如说是脚本命令，类似于shell

php运行于服务器，通常配合网页三件套使用，用于动态修改网页资源（服务端动态修改html/css/js代码文件，然后返还给客户端）

php和js不要搞混

js运行于客户端，通过客户端浏览器内核解释运行

php运行于服务端，通过php引擎解释执行（类似于shell脚本）；php可以和三件套混合使用，即php中包含html代码或者js代码，但是服务端并不会解析html/js，只会执行<php>标签下的代码，即其他代码当作普通文本，会进行忽略，只关注php代码段



# 安装

官网：[PHP For Windows: Binaries and sources Releases](https://windows.php.net/download#php-8.4)

步骤：

1.下载压缩包解压

2.配置PATH环境变量



## 拓展开启

1.查找安装目录下的`php.ini-xxxx`文件改名为`php.ini`

2.编辑文件

3.解注释所需插件，例如开启mysqli插件：查找mysqli，去掉注释符号`;`

4.所提示插件加载失败，则修改`ext`字段，可以设置为安装目录+ext（一般插件都安装在ext目录下）



# 调试

需要额外安装xdebug插件（https://xdebug.org/）

注意：

1.需要找到对应版本的插件，否则会启动失败

2.需要手动改名为`php-xdebug.dll`，或者配置xdebug相关字段，手动设置动态库文件名

例如：在ini文件中新增配置

```ini
[xdebug]
zend_extension="D:\phpstudy_pro\Extensions\php\php7.3.9nts\ext\php_xdebug.dll"
xdebug.mode = debug 
xdebug.start_with_request = yes 
xdebug.client_port = 9003
xdebug.client_host=127.0.0.1 
xdebug.remote_handler=dbgp 
xdebug.idekey=PHPSTORM 
```

3.然后启动ide，配置xdebug端口就可以开始调试了

参考文章：

[xdebug下载与配置，有这一篇就够了_php xdebug下载-CSDN博客](https://blog.csdn.net/weixin_54515836/article/details/119490850)

# 执行

```shell
php xxx.php
```

示例：(输出)

```php
<?php
echo "Hello from VS Code!\n";
?>
```

