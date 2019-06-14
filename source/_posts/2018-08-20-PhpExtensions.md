---
title: "PHP-扩展(Extensions)"
date: 2018-08-20
comments: true
categories: PHP
tags:
    - PHP
    - 内核
---

# PHP资料

>http://www.phpinternalsbook.com/


## PHP下载
* 官网下载:https://secure.php.net/downloads.php
* GITHUB: http://github.com/php/php-src

 <!-- more -->

## 构建依赖程序
在继续之前,您可能应该与包管理器一起安装一些基本构建依赖项(默认情况下,您可能已经安装了前三个):
* gcc或其他一些编译器套件.
* libc-dev,它提供C标准库,包括头文件.
* make,这是PHP使用的构建管理工具.
* autoconf(2.59或更高版本),用于生成配置脚本.
* automake(1.4或更高),它生成 Makefile.in文件.
* libtool帮助管理共享库.
* bison(2.4或更高版本),用于生成PHP解析器.
* re2c(可选),用于生成PHP词法分析器.由于git存储库已经包含一个生成的词法分析器,所以只需要修改它就需要re2c.

在Debian/Ubuntu上,您可以使用以下命令安装所有这些:
`sudo apt-get install build-essential autoconf automake libtool bison re2c`

## 配置选项讲解:`./configure --help | less`
使用`--enable-NAME`和`--disable-NAME`开关编译哪些扩展和SAPI .

如果扩展或SAPI具有外部依赖关系,则需要使用`--with-NAME`和`--without-NAME`.

如果NAME所需的库 不在默认位置(例如,因为您自己编译),则可以使用`--with-NAME = DIR`指定其位置.

如果选项是`--enable-NAME`或`--with-NAME`说明该选项默认是关闭的.如果是`--disable-NAME`或`--without-NAME`说明该选项默认是开启的.

## 我们要进行的配置和编译命令:
    ./configure --disable-all --enable-cli --enable-debug
        (--enable-debug启用调试模式,具有多重效果:
        编译将使用 -g运行以生成包括行号、变量的类型和作用域、函数名字、函数参数和函数的作用域等源文件特性的调试信息.
        另外使用-O0,会让gcc编译时不对代码优化.
        此外,调试模式定义了 ZEND_DEBUG宏,它将启动引擎中的各种调试助手.除其他事项外,还将报告内存泄漏以及某些数据结构的不正确使用.)
    make -jN
        (N为CPU数量,作用:make --help查看)

## PHP内核源码目录结构
    php-744.1.4
    ├── build   //源码编译相关文件
    └── ext     //官方扩展目录,包括了绝大多数PHP的函数的定义和实现
    └── main    //PHP核心基本文件,这里和Zend引擎不一样,Zend引擎主要实现语言最核心的语言运行环境.
    └── pear    //“PHP 扩展与应用仓库”,包含PEAR的核心文件.
    └── sapi    //包含了各种服务器抽象层的代码,例如apache的mod_php,cgi,fastcgi以及fpm等等接口.
    └── tests   //PHP的测试脚本集合,包含PHP各项功能的测试文件
    └── TSRM    //PHP的线程安全是构建在TSRM库之上的,PHP实现中常见的*G宏通常是对TSRM的封装,TSRM(Thread Safe Resource Manager)线程安全资源管理器.
    └── win32   //Windows平台相关的一些实现,比如sokcet的实现在Windows下和*Nix平台就不太一样,同时也包括了Windows下编译PHP相关的脚本.
    └── Zend    //Zend引擎的实现目录,比如脚本的词法语法解析,opcode的执行以及扩展机制的实现等等.
    └── .gdbinit            //gdb命令编写脚本   (gdb) source /home/laruence/package/php-5.2.14/.gdbinit       (gdb) zbacktrace
    └── CODING_STANDARDS    //PHP编码标准
    └── config.guess        //由automake产生,两个用于目标平台检测的脚本
    └── config.log          //configure执行时生成的日志文件
    └── config.nice         //configure执行时生成,记录了上次执行configure时带的详细参数
    └── config.status       //configure执行时生成,实际调用编译工具构建软件的shell脚本
    └── config.sub          //由automake产生,两个用于目标平台检测的脚本
    └── configure           //配置并生成makefile
    └── configure.in        //autoreconf创建,开发者维护,用于生成configure
    └── CREDITS             //开发人员名单
    └── EXTENSIONS          //扩展说明(维护状态,维护人员,版本,适用系统..)
    └── LICENSE             //发布协议
    └── php.ini-development //PHP开发环境示例配置文件
    └── php.ini-production  //PHP生产环境示例配置文件
    └── README.EXT_SKEL     //构建扩展脚本说明
    └── README.GIT-RULES    //GIT提交时的规则
    └── README.namespaces   //命名空间说明
    └── README.PARAMETER_PARSING_API    //新的参数解析函数说明
    └── README.REDIST.BINS              //PHP中引用到的其它程序协议说明
    └── README.RELEASE_PROCESS          //PHP发布过程说明
    └── README.SELF-CONTAINED-EXTENSIONS//创建一个内建的PHP扩展
    └── README.STREAMS                  //PHP Streams(流概念) 说明
    └── README.SUBMITTING_PATCH         //介绍如何提交PHP的增强功能或修补程序
    └── README.TESTING                  //测试说明(run-tests.php)
    └── README.TESTING2                 //测试说明(server-tests.php)
    └── README.UNIX-BUILD-SYSTEM        //PHP编译系统V5概述
    └── README.WIN32-BUILD-SYSTEM       //WIN32编译说明
    └── run-test.php                    //测试脚本
    └── server-test.php                 //测试脚本
    └── sesrver-test-config.php         //测试脚本
    └── UPGRADING                       //版本更新说明
    └── UPGRADING.INTERNALS             //内部更新说明

## PHP扩展分类
PHP中的扩展分为两类:PHP扩展、Zend扩展,对内核而言这两个分别称之为:模块(module)、扩展(extension),我们主要介绍是PHP扩展,也就是模块.

### 加载区别:
* PHP扩展(又名PHP“模块”)使用“extension = test.so”行加载到INI文件中
* Zend扩展使用“zend_extension = test.so”行加载到INI文件中

Zend扩展比PHP扩展更复杂,因为它们有更多的钩子,而且更接近Zend引擎及其虚拟机(整个PHP源代码中最复杂的部分).
Zend扩展例子如:OPCache,XDebug,phpdbg,Zend扩展通常用来处理两种任务:调试器和剖析器.如果您的目标是“只是”向PHP 添加一些新概念(函数,类,常量等),那么您将使用PHP扩展,但如果需要更改PHP的当前行为,可能Zend扩展将会更好.