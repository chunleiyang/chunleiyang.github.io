---
layout: post
title: "visual studio 2010 msdn 更改路径"
category: Tools
excerpt: "
&emsp;&emsp;visual studio 2010的msdn默认是安装在C:/ProgramData/Microsoft/HelpLibrary，而且是不能更改的，如果将msdn装齐的话会很大，想要安装到其他盘符下可以通过以下几步完成，比如改到E:/HelpLibrary：  
"
tags: [windows, msdn]
---


&emsp;&emsp;visual studio 2010的msdn默认是安装在C:/ProgramData/Microsoft/HelpLibrary，而且是不能更改的，如果将msdn装齐的话会很大，想要安装到其他盘符下可以通过以下几步完成，比如改到E:/HelpLibrary：  

1. 找到C:/ProgramData/Microsoft/HelpLibrary，把HelpLibrary文件夹下的所有内容复制到E:/HelpLibrary
2. 打开注册表，找到HKEY_LOCAL_MACHINE/SOFTWARE/Microsoft/Help/v1.0，此处有一个LocalStore的值 .数据就是之前默认保存的路径了.先备份一下,然后替换成自己想要的文件夹路径E:/HelpLibrary
3. 找到文件C:/Program Files/HelpLibrary/manifest/queryManifest.3.xml，进行编辑：找到下面两行：
    - <catalogPath>C:/ProgramData/Microsoft/HelpLibrary/catalogs/VS/100/EN-US</catalogPath>
    - <contentPath>C:/ProgramData/Microsoft/HelpLibrary/content</contentPath>
4. 将C:/ProgramData/Microsoft改为E:即可

