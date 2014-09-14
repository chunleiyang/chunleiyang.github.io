---
layout: post
title: "PHP编译扩展时解析器出错"
category: Programming
excerpt: "
&emsp;&emsp;编译PHP扩展时出现出错，操作步骤如下：  

1. cd path/to/php-src/ext
2. ./ext_skel --extname=example
3. cd ..
4. ./buildconf
5. ./configure --with-example
6. make

&emsp;&emsp;ok，就在编译的时候解析器报错：  
"
tags: [php, extension]
---

&emsp;&emsp;编译PHP扩展时出现出错，操作步骤如下：  

1. cd path/to/php-src/ext
2. ./ext_skel --extname=example
3. cd ..
4. ./buildconf
5. ./configure --with-example
6. make

&emsp;&emsp;ok，就在编译的时候解析器报错：  

    path/to/php-src/Zend/zend_language_parser.y:50.1-5: invalid directive: `%code'
    path/to/php-src/Zend/zend_language_parser.y:50.7-14: syntax error, unexpected "identifier"

&emsp;&emsp;原因是 `%coe` 是 `bison2.3+` 之后才引入的，我的机器上 `bison` 的版本是 `1.875c` ，因此不支持，解决方法是：  

1. 下载高版本的 `bison` 并安装；
2. 或者修改 `path/to/php-src/Zend/zend_language_parser.y` ，由于我没有 `root` 权限，`bison` 是安装在 `/usr/bin/` 的，因此采用方案2

&emsp;&emsp;具体如下：  

    将%code requires { 修改为%{
    将} 修改为%}
    
    make clean && make

&emsp;&emsp;Ok.

