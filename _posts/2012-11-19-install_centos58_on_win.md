---
layout: post
title: "Win7下安装CentOS 5.8双系统"
category: Linux
excerpt: "
&emsp;&emsp;上周末看到自己的机器跑的也慢了，再加上不想在虚拟机下学linux了，因此，想装个双系统，看到鸟哥的私房菜是在CentOS 5.X下进行演示，索性就装个CentOS吧，在这里装的是CentOS的5.8版本CentOS 5.8，下载 *CentOS-5.8-i386-bin-DVD-1of2.iso* 和 *CentOS-5.8-i386-bin-DVD-1of2.iso* 这个两个镜像。  
"
tags: [centos, double system]
---


&emsp;&emsp;上周末看到自己的机器跑的也慢了，再加上不想在虚拟机下学linux了，因此，想装个双系统，看到鸟哥的私房菜是在CentOS 5.X下进行演示，索性就装个CentOS吧，在这里装的是CentOS的5.8版本CentOS 5.8，下载 *CentOS-5.8-i386-bin-DVD-1of2.iso* 和 *CentOS-5.8-i386-bin-DVD-1of2.iso* 这个两个镜像。  
  
&emsp;&emsp;在这里先插入一段小插曲，就是安装win7的时候出现了让人郁闷的事情，我是分了四个区，win7安装在C盘，装完之后，发现所有分区都是动态类型的，如果只用win7一个系统那么没什么关系的其实，但是要装双系统就不能是动态类型，然后就重装了下win7，但是到分区那一步的时候发现居然不能操作，无法删除也无法格式化，然后查了下资料，方法就是，在分区那一步，按 `shift+F10` 调出dos命令提示符窗口，分别执行以下操作：  

1. 键入 `diskpart` 命令，进入diskpart环境;
2. 键入 `list disk` 命令，列出本机所有磁盘;
3. 键入 `select disk 0` 命令，选择第一个磁盘;
4. 键入 `clean` 命令，清除分区。

&emsp;&emsp;到这一步其实事情已经解决了，你在返回分区界面刷新一下就能看到效果了，在这里如果想灵活的分出主分区和逻辑分区，在dos diskpart命令环境下很方便的，比如 `create primary partition size=51200` 创建一个大小为50G的主分区，然后用 `active` 命令激活，再比如 `create partition extended` 命令创建扩展分区等。在这里我我分了四个分区，一个主分区，三个逻辑分区，最后一个分区F盘大小为60G准备安装我的CentOS。好了，插曲就到这里，下面看看CentOS的安装方法：  

- 首先准备启动盘

&emsp;&emsp;启动盘可以是U盘、硬盘和光盘，用U盘和光盘制作启动镜像来安装应该是最简单的方式，但是本人手边木有那么大的U盘也没有一张光盘，就选择用硬盘安装。首先用分区工具 Acronis Disk Director Suite 从E盘分出5G空间用来存放CentOS安装文件，为方便起见，在这里称其为”安装分区“，但是linux只识别ext2/ext3/ext4或者fat32这样的文件系统，因此不能划分完就完事了，我们知道windows也识别fat32文件系统，因此将”安装分区“格式化为fat32系统，其实这里还有个问题，那就是fat32系统只识别4G以下的文件，如果你的CentOS镜像大于4G了那么就不能放在fat32文件系统下面，你要将其格式化ext3等linux识别的文件系统，再用Ext2Fs这样的工具去把安装镜像放到里面，如果不用这个工具的话windows是不识别ext文件系统的，更别说把文件拷进去了。再说说这里为什么可以用fat32文件系统，我们看到我们下载下来的安装文件有两个iso，第一个是3G多不到4G，第二个不到1G，但是加一起就大于4G了，但是没关系，单个文件小于4G就OK。再用UltraISO将 *CentOS-5.8-i386-bin-DVD-1of2.iso*中的*isolinux/vmlinuz* 和 *isolinux/initrd.img* 以及*imgs* 放到我们刚刚划分好的"安装分区"里面，最后"安装分区"里面应该有5个文件，分别是 *CentOS-5.8-i386-bin-DVD-1of2.iso*、*CentOS-5.8-i386-bin-DVD-2of2.iso*、*vmlinuz*、*initrd.img*和*imgs*，如果只放*DVD1*可能会让你在最后快完成安装的时候喷血，会提示镜像找不到的错误。OK，到这里我们的启动盘准备完成。

- 添加启动菜单

&emsp;&emsp;下载并安装*EasyBCD*来为我们刚刚做的启动盘添加一个启动项以便进入安装环节。打*开EasyBCD*，*AddNewEntry->Install->Config*，这时会弹出一个*txt*文件，这个文件其实就是*menulist*文件，在其中键入如下值：  

> title CentOS 5.8  
> kernel  (hd0,6)/vmlinuz  
> initrd  (hd0,6)/initrd.img  

&emsp;&emsp;保存并退出。  
  
&emsp;&emsp;在这里，解释一下*menulist*中的这些东西。title后面的内容只是一个标题，写错写对无伤大雅，下面的kernel和initrd后面其实是vmlinuz和initrd.img的路径，不管你的本的磁盘接口是IDE的还是SATA的都是以hd来标记，比如hd0表示你的第一个磁盘，括号内的第二个数字表示对应的磁盘上的分区号，在这里要写对应的”安装分区“的分区号，我的本上的分区是这样的一个主分区C盘，两个逻辑分区D盘和E盘，后面是一个刚刚从C盘剥离出来的”安装分区“以及最后的一个F盘，由于分区号0~3是给主分区或者可扩展分区的，因此这里C盘的分区号就是0，D、E、”安装分区“、F分别是4、5、6、7。这个别写错了，否则在安装的时候会因为找不见vmlinuz和initrd.img文件而失败。OK，到这里我们已经给我们的启动盘添加了启动项，接下来重启选择第二个菜单进入安装。  

- 安装CentOS5.8

&emsp;&emsp;这个环节基本上就是一路”next“的节奏，稍微注意一下的是在选择安装文件的时候要选择sd7否则会找不着安装文件，其实这个没什么，实在不知道自己的安装文件丢到那个分区里了就挨个试一试，总有一个会成功的。再就是最重要的分区，在这里选择”建立自定义的分区结构“，点”确定“，这时候我们会在上面区域看到我们的磁盘分区情况，我是想装在F盘，但是这个分区是在装win7的时候分出来的，是NTFS文件系统，因此我们在这里首先要删除这个分区，这时候会看到Free后面跟一个代表大小的数字，OK，我们就从这个free里面为我们的linux分区。在这里分四个区，分别是/、/boot、/home、swap区，由于我预先留出来的空间比较大有60G，因此在这里给/根区分20G，给/boot分200M，给swap分4G，剩下的全分给/home。好了，这些步骤都完成后，我就分好区了，接下来还有一个一定要注意的地方，那就是安装引导装载程序grub，我们要把它安装在MBR上才能进行引导，其实默认也是安装在MBR里头的，/dev/hda就代表了那块硬盘的MBR，下面有个列表区域，就是你开机时候出现的菜单。  

- 关于引导的一些问题

&emsp;&emsp;安装完后重启，默认引导是由grub来引导的，默认的系统是CentOS，但是如果我们在大部分时间内用的是windows的话这样很不方便，因此建议进入CentOS系统后，切换到root用户，然后gedit /etc/grub.conf，然后将default=0改为default=1就会默认启动windows。至此，已经大功告成了，开始你的CentOS吧。  

- 其他

&emsp;&emsp;到这一步，按理应该是在玩windows或者CentOS才对，可是我却干了一件麻烦事。切换到win7的时候我们会看到我们一开始制作的那个”安装分区“，就5G摆在那里总觉得别扭，然后我手闲闲的将其和邻近的E盘合并了，一切看起来似乎很完美，但当我再次重启想进CentOS时发现没能正常启动grub，出现的是一个grub命令提示符，其实这个原因是因为你把一个分区与另一个分区合并之后，那个被合并的分区之后的所有分区的编号都减一了，而你在/etc/grub.conf的root (hd0,7)就不在适用了，应该改为(hd0,6) 才对，这里的root命令用来设定/boot分区的，因为没合并那两个分区前，有C、D、E、”安装分区“、/boot、/、/home、/swap，因此，/boot就是(hd0,7)，但是合并了E盘和”安装分区“后，/boot的编号应该为(hd0,6)才对。OK，在grub命令提示符下键入以下命令：  

1. setup (hd0)  //重新安装grub在MBR上
2. reboot  //重启

&emsp;&emsp;这时候还是会出现grub命令提示符，这是肯定的，因为我们并没有修改gurb.conf文件，现在就来做这件事：  

1. root (hd0,6)  //设定/boot分区
2. kernel /vmlinuz-2.6.18.308.e15 ro root=LABEL=/  rhgb quiet  //你可以用tab键补全
3. initrd /initrd-2.6.18.308.e15.img  //你可以用tab键补全
4. boot

&emsp;&emsp;OK，我们会看到我们的CentOS又可以顺利的启动了！进入之后，记得把/boot/grub/grub.conf进行修改，主要就是把/boot的分区号给对应上，我这里就是把(hd0,7)改为(hd0,6)。

- 最后，还要感谢我可爱的老婆陪着我 ^_^ 。
