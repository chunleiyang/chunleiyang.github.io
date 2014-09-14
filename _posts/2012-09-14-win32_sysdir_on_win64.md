---
layout: post
title: "win32程序在64位机器上读取system目录下文件失败"
category: Programming
excerpt: "
&emsp;&emsp;今天做项目的时候碰到一个问题，就是要去遍历系统的所有进程，然后获取每个进程的镜像全路径，进而对其做一些非破坏性的处理，然后在用`_tfopen_s`以二进制读模式打开的时候，发现在64位vista和64位win7上某些文件访问不到
"
tags: [windows, cpp]
---

&emsp;&emsp;今天做项目的时候碰到一个问题，就是要去遍历系统的所有进程，然后获取每个进程的镜像全路径，进而对其做一些非破坏性的处理，然后在用`_tfopen_s`以二进制读模式打开的时候，发现在64位vista和64位win7上某些文件访问不到，比如“C:\Windows\System32\smss.exe”，还有好多，这里就不在一一列举了。然后我就很郁闷是不是库函数在64位下出了什么问题，然后改用系统函数`CreateFile`以共享读模式打开文件，结果还是一样，没有任何改变，再`GetLastError`了一下，返回值为2，也就是找不到指定的文件，但是该文件确实存在啊，怎么会这样呢，然后就是各种查查查，后来看到`Wow64DisableWow64FsRedirection`，试了一下就好使了，具体用法如下：  

{% highlight cpp %}
typedef BOOL (WINAPI * WDWFD)(_Out_  PVOID);
    
WDWFD pWdwfd = (WDWFD)GetProcAddress(GetModuleHandle(_T("Kernel32.dll")),"Wow64DisableWow64FsRedirection");

if (pWdwfd!=NULL)
{
    pWdwfd(NULL);
}
{% endhighlight %}

&emsp;&emsp;在你需要打开文件的前面加上上述代码，OK，不管是32位机器还是64位机器就都好使了。
