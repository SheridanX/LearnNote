[TOC]

 

------



# 第一章 驱动应该怎么学

## 5.1.0 章节概要

**5.1.1.什么是驱动1**

​    本节首先讲解驱动部分整个课程规划，然后讲述第1个课程的整体规划。

**5.1.2.什么是驱动2**

​    本节从4个层面解释驱动的本质含义，然后详细介绍了linux内核驱动在整个分层框架中的位置。

**5.1.3.模块化设计**

​    本节首先讲解微内核和宏内核的概念，然后讲解linux内核的静态和动态模块化设计。

**5.1.4.linux设备驱动分类**

​    本节讲述linux内核中三种主要驱动类型的特点和差异，其中重点讲述的是字符设备驱动特征及其与块设备驱动的差异。

**5.1.5.驱动程序的安全性要求**

​    本节讲述驱动程序和内核的关联，从而说明驱动程序对安全性的特殊要求

**5.1.6.驱动应该这么学**

## 5.1.1_2.什么是驱动1_2

### 5.1.1.1、理解驱动的概念

(1)驱动一词的字面意思

(2)物理上的驱动：给了物体一个力，就驱动了它。

(3)硬件中的驱动：电源就是个驱动。

(4)linux内核驱动：软件层面的驱动广义上就是指：这一段代码操作控制了硬件去动，所以这一段代码就叫硬件的驱动程序。（本质上是电力提供了动力，而驱动程序提供了操作逻辑方法）

狭义上驱动程序就是专指操作系统中用来操控硬件的逻辑方法部分代码。

### 5.1.1.2、linux体系架构

(1)分层思想

(2)驱动的上面是系统调用API

(3)驱动的下面是硬件

(4)驱动自己本身也是分层的

 

 

## 5.1.3.模块化设计

### 5.1.3.1、微内核和宏内核

(1)宏内核（又称为单内核）：将内核从整体上作为一个大过程实现，并同时运行在一个单独的地址空间。所有的内核服务都在一个地址空间运行，相互之间直接调用函数，简单高效。

(2)微内核：功能被划分成独立的过程，过程间通过IPC进行通信。模块化程度高，一个服务失效不会影响另外一个服务。典型如windows

(3)linux：本质上是宏内核，但是又吸收了微内核的模块化特性，提现在2个层面

### 5.1.3.2、静态模块化

在编译时实现可裁剪，特征是想要功能裁剪改变必须重新编译

### 5.1.3.3、动态模块化

zImage可以不重新编译烧录，甚至可以不关机重启就实现模块的安装和卸载

## 5.1.4.linux设备驱动分类

### 5.1.4.1、驱动分类

(1)分三类：字符设备驱动、块设备驱动、网络设备驱动

(2)分类原则：设备本身读写操作的特征差异

### 5.1.4.2、三类驱动程序详细对比分析

(1)**字符设备**：准确的说应该叫“字节设备”，软件操作设备时是以字节为单位进行的。典型的如LCD、串口、LED、蜂鸣器、触摸屏······

(2)**块设备**：块设备是相对于字符设备定义的，块设备被软件操作时是以块（多个字节构成的一个单位）为单位的。设备的块大小是设备本身设计时定义好的，软件是不能去更改的，不同设备的块大小可以不一样。常见的块设备都是存储类设备，如：硬盘、NandFlash、iNand、SD····

(3)**网络设备**：网络设备是专为网卡设计的驱动模型，linux中网络设备驱动主要目的是为了支持API中socket相关的那些函数工作。

### 5.1.4.3、为什么字符设备驱动最重要

(1)常见大量设备都属于字符设备

(2)举例说明非标准类型字符设备驱动

 

 

## 5.1.5.驱动程序的安全性要求

### 5.1.5.1、驱动是内核的一部分

(1)驱动已经成为内核中最庞大的组成部分

(2)内核会直接以函数调用的方式调用驱动代码

(3)驱动的动态安装和卸载都会“更改”内核

### 5.1.5.2、驱动对内核的影响

(1)驱动程序崩溃甚至会导致内核崩溃

(2)驱动的效率会影响内核的整体效率

(3)驱动的漏洞会造成内核安全漏洞

### 5.1.5.3、常见驱动安全性问题

(1)未初始化指针

(2)恶意用户程序

(3)缓冲区溢出

(4)竞争状态

 

 

## 5.1.6.驱动应该这么学

### 5.1.6.1、先学好C语言

### 5.1.6.2、掌握相关预备知识

(1)硬件操作方面：常见的硬件有一定的了解。

(2)应用层API   ：

### 5.1.6.3、驱动学习阶段

(1)注重实践，一步一步写驱动 

(2)框架思维，多考虑整体和上下层

(3)先通过简单设备学linux驱动框架

(4)学会总结、记录，这会有助于理解

 

# 第二章 字符设备驱动基础

## 5.2.0 章节概要

**5.2.1.开启驱动开发之路**

​    本节主要是讲述并且实践示范驱动开发的环境搭建、构建内核源码树、常用模块安装卸载命令等。

**5.2.2.最简单的模块源码分析1**

​    本节主要讲解模块安装和过程，及其和module_init宏的关联，并且重点讲解了模块安装时的安全性兼容性校验问题。

**5.2.3.最简单的模块源码分析2**

​    本节主要讲解了模块的卸载过程、MODULE_LICENSE等信息添加宏和__init、__exit宏。

**5.2.4.最简单的模块源码分析3**

​    本节主要分析了printk函数及其打印级别定义，以及模块编译的Makefile文件工作原理。

**5.2.5.用开发板来调试模块**

​    本节在开发板上搭建模块测试的环境，主要是内核源码树的编译、内核的下载、nfs挂载rootfs的设置，uboot中bootcmd和bootargs的设置等细节。

**5.2.6.字符设备驱动工作原理1**

​    本节开始讲解字符设备驱动的工作原理，重点是file_operations结构体及整个应用+驱动体系的框架。

**5.2.7.字符设备驱动工作原理2**

​    本节讲解了register_chrdev函数，并且重点讲解了内核内部对字符设备驱动的管理机制。

**5.2.8.字符设备驱动代码实践1**  

​    本节开始字符设备驱动的编码实践，首先把整体框架理顺，然后构建了file_operations结构体变量，并且填充了open和close方法。

**5.2.9.字符设备驱动代码实践2**

​    本节进行字符设备驱动的注册，以及编译和实际在开发板上的测试。

**5.2.10.应用程序如何调用驱动**

​    本节引入设备文件的概念，同时结合前面讲述的主次设备号，综合讲解应用程序对驱动程序的调用等整体流程。

**5.2.11.添加读写接口**

​    本节开始在我们的驱动中添加write和read方法，并且讲解了copy_from_user和copy_to_user这两个重要函数。

**5.2.12.读写接口实践**  

​    本节完成write和read函数的编写，并且在开发板上进行实践测试。

**5.2.13.驱动中如何操控硬件**

​    本节研究驱动中对硬件的操作和裸机中的不同，并且重点讲解了linux内核中虚拟地址映射的2种机制和原理。

**5.2.14.静态映射操作LED1**

​    本节讲解我们所使用的内核版本中和静态虚拟地址映射有关的文件，并且带大家实际分析这部分代码，以深入理解静态映射表的实质。

**5.2.15.静态映射操作LED2**

​    本节使用静态虚拟地址映射方法，参考裸机中的LED控制方法，在驱动中添加LED的操作方法，在对比中让大家理解驱动和裸机的区别。

**5.2.16.静态映射操作LED3**

​    本节结合应用程序和驱动程序，实现一整套控制LED亮灭的完整体系。让大家初步体会到应用和驱动的配合。

**5.2.17.动态映射操作LED**

​    本节介绍并使用动态映射的方式对寄存器进行映射，然后实现应用操作，主要目的是学习动态内核映射的原理和实践方法。

## 5.2.1.开启驱动开发之路

### 5.2.1.1、驱动开发的准备工作

(1)正常运行linux系统的开发板。要求开发板中的linux的zImage必须是自己编译的，不能是别人编译的。

(2)内核源码树，其实就是一个经过了配置编译之后的内核源码。

(3)nfs挂载的rootfs，主机ubuntu中必须搭建一个nfs服务器。

### 5.2.1.2、驱动开发的步骤

(1)驱动源码编写、Makefile编写、编译

(2)insmod装载模块、测试、rmmod卸载模块

### 5.2.1.3、实践

(1)copy原来提供的x210kernel.tar.bz2，找一个干净的目录（/root/driver），解压之，并且配置编译。编译完成后得到了：1、内核源码树。2、编译ok的zImage。

(2)fastboot将第1步中得到的zImage烧录到开发板中去启动（或者将zImage丢到tftp的共享目录，uboot启动时tftp下载启动），将来驱动编译好后，就可以在这个内核中去测试。因为这个zImage和内核源码树是一伙的，所以驱动安装时版本校验不会出错。

 

## 5.2.2.最简单的模块源码分析1

### 5.2.2.1、常用的模块操作命令

(1)**lsmod**(list module,将模块列表显示)：功能是打印出当前内核中已经安装的模块列表

(2)**insmod**（install module，安装模块）：功能是向当前内核中去安装一个模块，用法是insmod xxx.ko

(3)**modinfo**（module information，模块信息）：功能是打印出一个内核模块的自带信息。，用法是modinfo xxx.ko

(4)**rmmod**（remove module，卸载模块）：功能是从当前内核中卸载一个已经安装了的模块，用法是rmmod xxx（注意卸载模块时只需要输入模块名即可，不能加.ko后缀）

(5)剩下的后面再说，暂时用不到（如modprobe、depmod等）

### 5.2.2.2、模块的安装

(1)先lsmod再insmod看安装前后系统内模块记录。实践测试标明内核会将最新安装的模块放在lsmod显示的最前面。

 

```c
module_init(chrdev_init);

module_exit(chrdev_exit);
```

(2)insmod与module_init宏：模块源代码中用module_init宏声明了一个函数（在我们这个例子里是chrdev_init函数），作用就是指定chrdev_init这个函数和insmod命令绑定起来，也就是说当我们insmod module_test.ko时，insmod命令内部实际执行的操作就是帮我们调用chrdev_init函数。

照此分析，那insmod时就应该能看到chrdev_init中使用printk打印出来的一个chrdev_init字符串，但是实际没看到。原因是ubuntu中拦截了，要怎么才能看到呢？在ubuntu中使用**dmesg命令**就可以看到了。

(3)模块安装时insmod内部除了帮我们调用module_init宏所声明的函数外，实际还做了一些别的事（譬如lsmod能看到多了一个模块也是insmod帮我们在内部做了记录），但是我们就不用管了。

### 5.2.2.3、模块的版本信息

(1)使用modinfo查看模块的版本信息

(2)内核zImage中也有一个确定的版本信息

(3)insmod时模块的vermagic必须和内核的相同，否则不能安装，报错信息为：insmod: ERROR: could not insert module module_test.ko: Invalid module format

(4)模块的版本信息是为了保证模块和内核的兼容性，是一种安全措施

(5)如何保证模块的vermagic和内核的vermagic一致？编译模块的内核源码树就是我们编译正在运行的这个内核的那个内核源码树即可。说白了就是模块和内核要同出一门。

 

 

## 5.2.3.最简单的模块源码分析2

### 5.2.3.1、模块卸载

(1)module_exit和rmmod的对应关系

(2)lsmod查看rmmod前后系统的模块记录变化

### 5.2.3.2、模块中常用宏

// MODULE_xxx这种宏作用是用来添加模块描述信息

MODULE_LICENSE("GPL");       // 描述模块的许可证一般声明为GPL许可证，而且最好不要少，否则可能会出现莫名其妙的错误（譬如一些明显存在的函数提升找不到）。

MODULE_AUTHOR("aston");       // 描述模块的作者

MODULE_DESCRIPTION("module test"); // 描述模块的介绍信息

MODULE_ALIAS("alias xxx");     // 描述模块的别名信息

 

### 5.2.3.3、函数修饰符

// 模块安装函数

static int __init chrdev_init(void)

{  

  printk(KERN_INFO "chrdev_init helloworld init\n");

  return 0;

}

 

// 模块卸载函数

static void __exit chrdev_exit(void)

{

  printk(KERN_INFO "chrdev_exit helloworld exit\n");

}

​                                

(1)__init，本质上是个宏定义，在内核源代码中就有#define __init xxxx。这个__init的作用就是将被他修饰的函数放入.init.text段中去（本来默认情况下函数是被放入.text段中）。

整个内核中的所有的这类函数都会被链接器链接放入.init.text段中，所以所有的内核模块的__init修饰的函数其实是被统一放在一起的。内核启动时统一会加载.init.text段中的这些模块安装函数，加载完后就会把这个段给释放掉以节省内存。

(2)__exit

### 5.2.3.4、static

​    更改函数的作用域，变为只在本文件内作用

 

## 5.2.4.最简单的模块源码分析3

### 5.2.4.1、printk函数详解

(1)printk在内核源码中用来打印信息的函数，用法和printf非常相似。

(2)**printk**和printf最大的差别：printf是C库函数，是在应用层编程中使用的，不能在linux内核源代码中使用；printk是linux内核源代码中自己封装出来的一个打印函数，是内核源码中的一个普通函数，只能在内核源码范围内使用，不能在应用编程中使用。

 

printk(KERN_INFO "chrdev_init helloworld init\n");

(3)printk相比printf来说还多了个：**打印级别的设置**；printk的打印级别是用来控制printk打印的这条信息是否在终端上显示的。应用程序中的调试信息要么全部打开要么全部关闭，一般用条件编译来实现（DEBUG宏），但是在内核中，因为内核非常庞大，打印信息非常多，有时候整体调试内核时打印信息要么太多找不到想要的要么一个没有没法调试。所以才有了打印级别这个概念。

(4)操作系统的命令行中也有一个打印信息级别属性，值为0-7。当前操作系统中执行printk的时候会去对比printk中的打印级别和我的命令行中设置的打印级别，小于我的命令行设置级别的信息会被放行打印出来，大于的就被拦截的。譬如我的ubuntu中的打印级别默认是4，那么printk中设置的级别比4小的就能打印出来，比4大的就不能打印出来。查：cat/proc/sys/kernel/printk ,左边第一个数字就是打印级别：

 

(5)ubuntu中这个printk的打印级别控制没法实践，ubuntu中不管你把级别怎么设置都不能直接打印出来，必须dmesg命令去查看。

### 5.2.4.2、关于驱动模块中的头文件

\#include <linux/module.h>    // module_init module_exit

\#include <linux/init.h>     // __init  __exit

(1)驱动源代码中包含的头文件和原来应用编程程序中包含的头文件不是一回事。应用编程中包含的头文件是应用层的头文件，是应用程序的编译器带来的（譬如gcc的头文件路径在 /usr/include下，这些东西是和操作系统无关的）。驱动源码属于内核源码的一部分，驱动源码中的头文件其实就是内核源代码目录下的include目录下的头文件。

 

### 5.2.4.3、驱动编译的Makefile分析

\#ubuntu的内核源码树，如果要编译在ubuntu中安装的模块就打开这2个

KERN_VER = $(shell uname -r)

KERN_DIR = /lib/modules/$(KERN_VER)/build 

\# 开发板的linux内核的源码树目录

\#KERN_DIR = /usr/local/x210_learning/divers/kernel

obj-m  += module_test.o

all:

make -C $(KERN_DIR) M=`pwd` modules

.PHONY: clean  

clean:

  make -C $(KERN_DIR) `pwd` modules clean

 (1)KERN_DIR，变量的值就是我们用来编译这个模块的内核源码树的目录

(2)obj-m += module_test.o，这一行就表示我们要将module_test.c文件编译成一个模块。

(3)make -C $(KERN_DIR) M=`pwd` modules   这个命令用来实际编译模块，工作原理就是：利用 make -C进入到我们指定的内核源码树目录下，然后在源码目录树下借用内核源码中定义的模块编译规则去编译这个模块，编译完成后把生成的文件还拷贝到当前目录下，完成编译。

​    -C进入到我们指定的内核源码树目录下

M=`pwd`  打印当前路径赋值给M，用来进入内核源码变异完后回返当前路径的。

 

(4)make clean ，用来清除编译痕迹

总结：模块的makefile非常简单，本身并不能完成模块的编译，而是通过make -C进入到内核源码树下借用内核源码的体系来完成模块的编译链接的。这个Makefile本身是非常模式化的，3和4部分是永远不用动的，只有1和2需要动。1是内核源码树的目录，你必须根据自己的编译环境

 

 

## 5.2.5.用开发板来调试模块

### 5.2.5.1、设置bootcmd使开发板通过tftp下载自己建立的内核源码树编译得到的zImage

set bootcmd 'tftp 0x30008000 zImage;bootm 0x30008000'

 

### 5.2.5.2、设置bootargs使开发板从nfs去挂载rootfs（内核配置记得打开使能nfs形式的rootfs）

Setenvbootargsroot=/dev/nfsnfsroot=192.168.1.141:/usr/local/x210_learning/divers/rootfs/rootfs ip=192.168.1.20:192.168.1.141:192.168.1.1:255.255.255.0::ens33:off init=/linuxrc console=ttySAC2,115200 

 

烧录启动：bootargs=console=ttySAC2,115200 root=/dev/mmcblk0p2 rw init=/linuxrc rootfstype=ext3

### 5.2.5.3、修改Makefile中的KERN_DIR使其指向自己建立的内核源码树

### 5.2.5.4、将自己编译好的驱动.ko文件放入nfs共享目录下去

### 5.2.5.5、开发板启动后使用insmod、rmmod、lsmod等去进行模块实验

 

 

## 5.2.6.字符设备驱动工作原理1

### 5.2.6.1、系统整体工作原理

(1)应用层->API->设备驱动->硬件

(2)API：open、read、write、close等

(3)驱动源码中提供真正的open、read、write、close等函数实体

### 5.2.6.2、file_operations结构体

(1)元素主要是函数指针，用来挂接实体函数地址

(2)每个设备驱动都需要一个该结构体类型的变量

(3)设备驱动向内核注册时提供该结构体类型的变量

### 5.2.6.3、注册字符设备驱动

(1)为何要注册驱动

(2)谁去负责注册：驱动自己

(3)向谁注册：内核

(4)注册函数从哪里来：内核

(5)注册前怎样？注册后怎样？注册产生什么结果？

 

 

## 5.2.7.字符设备驱动工作原理2

### 5.2.7.1、register_chrdev详解(#include <linux/fs.h>)

static inline int register_chrdev(unsigned int major, const char *name,

​         const struct file_operations *fops)

{

  return __register_chrdev(major, 0, 256, name, fops);

}

​    major:主设备号，其实就是int类型的数字，类似于人的身份证号。这个设备号可以自己指定也可让内核自动分配

​    name：当前设备驱动的名字,主要是为了人好看好记忆，就像人的名字

​    fops :   结构体的指针，将会指向file_operations结构体，将这个结构体送到内核里注册。

(1)作用，驱动向内核注册自己的file_operations

(2)参数

(3)inline和static

​      函数是不能定义在头文件里的，如果定义在头文件里如果同时被两个C文件调用的会出现重复定义，加了inline后，编译链接时会将函数在原调用地展开，这样就不会在编译连接时重复定义了。

​      放inline的本质原理是函数非常短，调用开销比执行函数开销打，这样加inline，效率就变高了。

### 5.2.7.2、内核如何管理字符设备驱动

(1)内核中有一个数组用来存储注册的字符设备驱动；数组是固定，最大只能注册255个驱动。

(2)register_chrdev内部将我们要注册的驱动的信息（主要是 ）存储在数组中相应的位置

(3)cat /proc/devices查看内核中已经注册过的字符设备驱动（和块设备驱动）

(4)好好理解主设备号（major）的概念

### 5.2.7.3、回顾和展望

(1)回顾：inline、static等关键字

(2)回顾：/proc文件系统的作用

(3)展望：将来深入学习驱动时可以去跟register_chrdev到内部看，验证我们上面讲的原理

## 5.2.8.字符设备驱动代码实践1

### 5.2.8.1、思路和框架

(1)目的：给空模块添加驱动壳子

(2)核心工作量：file_operations及其元素填充、注册驱动

### 5.2.8.2、如何动手写驱动代码

(1)脑海里先有框架，知道自己要干嘛

(2)细节代码不需要一个字一个字敲，可以到内核中去寻找参考代码复制过来改

(3)写下的所有代码必须心里清楚明白，不能似懂非懂

### 5.2.8.3、开始动手

(1)先定义file_operations结构体变量

(2)open和close函数原型确定、内容填充

 

 

## 5.2.9.字符设备驱动代码实践2

### 5.2.9.1、注册驱动

(1)主设备号的选择

(2)返回值的检测

### 5.2.9.2、驱动测试

(1)编译等 make && make cp

(2)insmod并且查看设备注册的现象

(3)rmmod并且查看设备注销的现象

### 5.2.9.3、让内核自动分配主设备号

(1)为什么要让内核自动分配

(2)如何实现？

  mymajor = register_chrdev(0, MYNAME, &test_fops);

  major参数传0，就表示内核自动分配，内核如果成功分配就会返回分配的主设备号，如果分配失败了就会返回负数；

然后还需要定义一个全局变量int mymajor；因为我们注销的时候需要用到这个，unregister_chrdev(mymajor, MYNAME);

 

(3)测试

\#include <linux/module.h>    // module_init module_exit

\#include <linux/init.h>     // __init  __exit

\#include <linux/fs.h>

 

\#define MYMAJOR   200

\#define MYNAME   "testchar"

 

int mymajor;

 

static int test_chrdev_open(struct inode *inode, struct file *file)

{  

  //这个函数真正应该放置的是打开这个设备的硬件操作代码部分

  //先用prink打印测试学习

  printk(KERN_INFO "test_chrdev_open\n");

  return 0;

}

 

static int test_chardev_release(struct inode *inode, struct file *file)

{

  printk(KERN_INFO "test_chardev_release\n");

  return 0;

}

 

//自定义一个file_operations结构体变量，并且填充

static const struct file_operations test_fops = {

  .owner   = THIS_MODULE,     //管理，直接写

  .open    = test_chrdev_open,  //将来应用open打开这个设备时，

​                    //实际打开局势这个函数

  .release  = test_chardev_release,

};

// 模块安装函数

static int __init chrdev_init(void)

{  

  printk(KERN_INFO "chrdev_init helloworld init\n");

  //在modul_init宏调用函数中去注册字符设备驱动

  mymajor = register_chrdev(0, MYNAME, &test_fops);

  if(mymajor < 0){

​    printk(KERN_ERR "register_chrdev fail. ");

​    return -EINVAL;

  }

  printk(KERN_INFO "register_chrdev success.mymajor = %d\n", mymajor);

  return 0;

}

 

// 模块卸载函数

static void __exit chrdev_exit(void)

{

  printk(KERN_INFO "chrdev_exit helloworld exit\n");

  //在modul_exit调用函数中去注销字符设备驱动

  unregister_chrdev(mymajor, MYNAME);

}

 

module_init(chrdev_init);

module_exit(chrdev_exit);

 

// MODULE_xxx这种宏作用是用来添加模块描述信息

MODULE_LICENSE("GPL");       // 描述模块的许可证

MODULE_AUTHOR("aston");       // 描述模块的作者

MODULE_DESCRIPTION("module test"); // 描述模块的介绍信息

MODULE_ALIAS("alias xxx");     // 描述模块的别名信息

 

 

 

## 5.2.10.应用程序如何调用驱动

### 5.2.10.1、驱动设备文件的创建

(1)何为设备文件：驱动把自己包装成设备文件给应用层用。

(2)设备文件的关键信息是：设备号 = 主设备号 + 次设备号，使用ls -l去查看设备文件，就可以得到这个设备文件对应的主次设备号。

(3)次设备号：同样的一种设备有好几个，比如LED有4个，如果只有主设备号，每个LED都要分配一个主设备号，这样设备号就耗费很快。次设备号就是来解决这个问题的。主设备号确定你设备的种类，此设备号确定这种设备的第几个设备。

(4)使用**mknod**创建设备文件：mknod /dev/xxx c 主设备号 次设备号

​    示例： mknod /dev/test c 250 0

### 5.2.10.2、写应用来测试驱动

(1)还是原来的应用

(2)open、write、read、close等

(3)实验现象预测和验证

\#include <stdio.h>

\#include <sys/types.h>

\#include <sys/stat.h>

\#include <fcntl.h>

 

\#define FILE "/dev/test"    //刚才mknod创建的设备文件名

 

int main(void)

{

  int fd = -1;

  fd = open(FILE, O_RDWR);

  if(fd < 0){

​    printf("open %s error.\n",FILE);

​    return -1;

  }

  printf("opend %s success..\n",FILE);

  //读文件

 

  //写文件

  close(fd);

  return 0;

}

 

### 5.2.10.3、总结

(1)整体流程梳理、注意分层

(2)后续工作：添加读写接口

 

 

## 5.2.11.添加读写接口

### 5.2.11.1、在驱动中添加

static int test_chardev_release(struct inode *inode, struct file *file)

{

  printk(KERN_INFO "test_chardev_release\n");

  return 0;

}

 

static ssize_t test_chrdev_write(struct file *file, const char __user *buf, size_t count, loff_t *ppos)

{

  printk(KERN_INFO "est_chrdev_writet\n");

  return 0;

}

 

### 5.2.11.2、在应用中添加

  write(fd, "I LOVE linux!",13);

  //读文件

  read(fd, buf, 100);

### 5.2.11.3、测试

 

### 5.2.11.4、应用和驱动之间的数据交换

(1)**copy_from_user**，用来将数据从用户空间复制到内核空间

(2)**copy_to_user**

注意：复制是和mmap的映射相对应去区分的，mmap 是映射到同一个地址，而1,2这两个函数是真正从一个地址复制到另外一个地址，这样效率很低。

 

 

## 5.2.12.读写接口实践

### 5.2.12.1、完成write和read函数

(1)copy_from_user函数的返回值定义，和常规有点不同。返回值如果成功复制则返回0，如果不成功复制则返回尚未成功复制剩下的字节数。

static ssize_t test_chrdev_write(struct file *file, const char __user *ubuf, size_t count, loff_t *ppos)

{

  int ret = -1;

  printk(KERN_INFO "est_chrdev_writet\n");

  //使用该函数将引用层传过来的ubuf中的内容拷贝到驱动空间

  //memcpy(kbuf, ubuf);这样是不行的，因为两个buf不在一个层次中

  ret = copy_from_user(kbuf, ubuf, count);//第三一般用引用传过来的count

  if(ret){

​    printk(KERN_INFO "copy_from_usr fail\n");

​    return -EINVAL;

  }

  printk(KERN_INFO "copy_from_usr Success.\n");

 

  //真正的驱动中，数据从引用层复制到驱动中后，我们就要更具这个数据区写硬件，完成

  //硬件的操作，下面就是要操作硬件的代码了。

  return 0;

}

 

static ssize_t test_chrdev_read(struct file *file, char __user *ubuf, size_t count, loff_t *ppos)

{

  int ret = -1;

  printk(KERN_INFO "test_chrdev_read\n");

  ret = copy_to_user(ubuf, kbuf, count);//第三一般用引用传过来的count

  if(ret){

​    printk(KERN_INFO "copy_to_usr fail\n");

​    return -EINVAL;

  }

  printk(KERN_INFO "copy_from_usr Success.\n");

 

  //真正的驱动中，数据从引用层复制到驱动中后，我们就要更具这个数据区写硬件，完成

  //硬件的操作，下面就是要操作硬件的代码了。

 

  return 0;

}

 

### 5.2.12.2、读写回环测试

 

### 5.2.12.3、总结

(1)目前为止应用已经能够读写驱动（中的内存）

(2)后续工作：添加硬件操作代码

 

 

## 5.2.13.驱动中如何操控硬件

### 5.2.13.1、还是那个硬件

(1)硬件物理原理不变

(2)硬件操作接口（寄存器）不变

(3)硬件操作代码不变

### 5.2.13.2、哪里不同了？

(1)寄存器地址不同：原来是直接用物理地址，现在需要用该物理地址在内核虚拟地址空间相对应的虚拟地址：寄存器的物理地址是CPU设计时决定的，从datasheet中查找到的。

(2)编程方法不同：裸机中习惯直接用函数指针操作寄存器地址，而kernel中习惯用封装好的io读写函数来操作寄存器，以实现最大程度可移植性。

### 5.2.13.3、内核的虚拟地址映射方法

(1)为什么需要虚拟地址映射

(2)内核中有2套虚拟地址映射方法：动态和静态

(3)静态映射方法的特点：

内核移植时以代码的形式硬编码，如果要更改必须改源代码后重新编译内核

在内核启动时建立静态映射表，到内核关机时销毁，中间一直有效

对于移植好的内核，你用不用他都在那里

(4)动态映射方法的特点：

​    驱动程序根据需要随时动态的建立映射、使用、销毁映射

​    映射是短期临时的

### 5.2.13.4、如何选择虚拟地址映射方法

(1)2种映射并不排他，可以同时使用

(2)静态映射类似于C语言中全局变量，动态方式类似于C语言中malloc堆内存

(3)静态映射的好处是执行效率高，坏处是始终占用虚拟地址空间；动态映射的好处是按需使用虚拟地址空间，坏处是每次使用前后都需要代码去建立映射&销毁映射（还得学会使用那些内核函数的使用）

 

 

## 5.2.14.静态映射操作LED1

### 5.2.14.1、关于静态映射要说的

(1)不同版本内核中静态映射表位置、文件名可能不同

(2)不同SoC的静态映射表位置、文件名可能不同

(3)所谓映射表其实就是头文件中的宏定义

### 5.2.14.2、三星版本内核中的静态映射表

(1)主映射表位于：arch/arm/plat-s5p/include/plat/map-s5p.h

CPU在安排寄存器地址时不是随意乱序分布的，而是按照模块去区分的。每一个模块内部的很多个寄存器的地址是连续的。所以内核在定义寄存器地址时都是先找到基地址，然后再用基地址+偏移量来寻找具体的一个寄存器。

map-s5p.h中定义的就是要用到的几个模块的寄存器基地址。

map-s5p.h中定义的是模块的寄存器基地址的虚拟地址。

(2)虚拟地址基地址定义在：arch/arm/plat-samsung/include/plat/map-base.h

\#define S3C_ADDR_BASE  (0xFD000000)    // 三星移植时确定的静态映射表的基地址，表中的所有虚拟地址都是以这个地址+偏移量来指定的

(3)GPIO相关的主映射表位于：arch/arm/mach-s5pv210/include/mach/regs-gpio.h

表中是GPIO的各个端口的基地址的定义

(4)GPIO的具体寄存器定义位于：arch/arm/mach-s5pv210/include/mach/gpio-bank.h

(5)自己来分析怎么去找静态映射表：

​       这些映射表一般都在头文件里边，一般在arch/arm/plat-路径中；

​       一般头文件的名字都是map- 起头

 

## 5.2.15.静态映射操作LED2

### 5.2.15.1、参考裸机中的操作方法添加LED操作代码

(1)宏定义

(2)在init和exit函数中分别点亮和熄灭LED

### 5.2.15.2、实践测试

(1)insmod和rmmod时观察LED亮灭变化

(2)打印出寄存器的值和静态映射表中的分析相对比

### 5.2.15.3、将代码移动到open和close函数中去

\#include <mach/regs-gpio.h>   **//**注意头文件的包含的顺序，因为//S5PV210_GPJ0CON **这个宏是由** **<mach/regs-gpio.h>**头文件的宏组成的，所以这个头文件应该放在前面**   

\#include <mach/gpio-bank.h>

\#define GPJ0CON   S5PV210_GPJ0CON 

\#define GPJ0DAT   S5PV210_GPJ0DAT 

\#define rGPJ0CON  *((volatile unsigned int *)GPJ0CON)

\#define rGPJ0DAT  *((volatile unsigned int *)GPJ0DAT)

 

static int test_chrdev_open(struct inode *inode, struct file *file)

{  

  //这个函数真正应该放置的是打开这个设备的硬件操作代码部分

  //先用prink打印测试学习

  printk(KERN_INFO "test_chrdev_open\n");

​    //配置初始化

  rGPJ0CON = 0x11111111; 

​    // led亮

  rGPJ0DAT = ((0<<3) | (0<<4) | (0<<5));

  return 0;

}

 

static int test_chardev_release(struct inode *inode, struct file *file)

{

  printk(KERN_INFO "test_chardev_release\n");

  rGPJ0DAT = ((1<<3) | (1<<4) | (1<<5));

  return 0;

}

 

 

## 5.2.16.静态映射操作LED3

### 5.2.16.1、添加驱动中的写函数

(1)先定义好应用和驱动之间的控制接口，这个是由自己来定义的。譬如定义为：应用向驱动写"on"则驱动让LED亮，应用向驱动写"off"，驱动就让LED灭

(2)应用和驱动的接口定义做的尽量简单，譬如用1个字目来表示。譬如定义为：应用写"1"表示灯亮，写"0"表示让灯灭。

static ssize_t test_chrdev_write(struct file *file, const char __user *ubuf, size_t count, loff_t *ppos)

{

  int ret = -1;

  memset(kbuf, 0, sizeof(kbuf));

  printk(KERN_INFO "est_chrdev_writet\n");

  //使用该函数将引用层传过来的ubuf中的内容拷贝到驱动空间

  //memcpy(kbuf, ubuf);这样是不行的，因为两个buf不在一个层次中

  ret = copy_from_user(kbuf, ubuf, count);//第三一般用引用传过来的count

  if(ret){

​    printk(KERN_INFO "copy_from_usr fail\n");

​    return -EINVAL

  }

  printk(KERN_INFO "copy_from_usr Success.\n");

 

  //真正的驱动中，数据从引用层复制到驱动中后，我们就要更具这个数据区写硬件，完成

  //硬件的操作，下面就是要操作硬件的代码了。

/*

  if (!strcmp(kbuf, "on") ){

​    rGPJ0DAT = ((0<<3) | (0<<4) | (0<<5));

  }

  if (!strcmp(kbuf, "off") ){

​    rGPJ0DAT = ((1<<3) | (1<<4) | (1<<5));

  }

*/

  if (kbuf[0] == '1'){

​    rGPJ0DAT = ((0<<3) | (0<<4) | (0<<5));

  }

  if (kbuf[0] == '0'){

​    rGPJ0DAT = ((1<<3) | (1<<4) | (1<<5));

  }

  return 0;

}

 

 

### 5.2.16.2、写应用来测试写函数

### 5.2.16.3、驱动和应用中来添加读功能

## 5.2.17.动态映射操作LED

### 5.2.17.1、如何建立动态映射

(1)request_mem_region，向内核申请（报告）需要映射的内存资源。这块内存暂时批给你用了。

(2)ioremap，真正用来实现映射，传给他物理地址他给你映射返回一个虚拟地址

### 5.2.17.2、如何销毁动态映射

(1)iounmap

(2)release_mem_region

注意：映射建立时，是要先申请再映射；然后使用；使用完要解除映射时要先解除映射再释放申请。

### 5.2.17.3、代码实践

(1)2个寄存器分开独立映射

(2)2个寄存器在一起映射

 

 

 

 

 

 

 



 

 

 

 

 

# 第三章 字符设备驱动高级

## 5.3.0 章节概要

**5.3.1.注册字符设备驱动新接口1**

​    本节介绍内核中提供的字符设备驱动注册的新接口cdev，并且讲了相关的接口函数，最后实践编写代码。

**5.3.2.注册字符设备驱动新接口2**

​    本节对上节讲述的知识进行实践编程测试。

**5.3.3.注册字符设备驱动新接口3**

​    本节讲述新接口如何自动分配设备号，以及其他一些编程细节如错误的逐级处理技巧。

**5.3.4.注册字符设备驱动新接口4**

​    本节讲述cdev_alloc和cdev_init这两个接口，同时引申讲解了C语言如何以面向对象的编程方式来实现linux内核。

**5.3.5.字符设备驱动注册代码分析1**

​    本节带大家浏览分析内核源码中与字符设备驱动相关的接口，使用SourceInsight逐级追踪的方式进入内核源码中。

**5.3.6.字符设备驱动注册代码分析2**

​    本节继续上节分析字符设备驱动注册相关的接口函数，目的是教大家学习如何从源码中去学习。

**5.3.7.自动创建字符设备驱动的设备文件**

​    本节实践编程演示如何使用class_create和device_create这两个接口来让字符设备驱动借助设备类自动创建及删除设备文件。

**5.3.8.设备类相关代码分析1**

​    本节开始分析class_create和device_create内部的实现原理。

**5.3.9.设备类相关代码分析2**

​    本节接上节继续分析，通过分析让大家对sysfs有所了解，知晓内核如果通过sysfs和udev进行通信以实现设备文件的自动创建和删除。

**5.3.10.静态映射表建立过程分析**

​    本节分析内核源码中与虚拟地址静态映射建立有关的代码，通过分析大家可以进一步掌握静态映射的实现细节。

**5.3.11.动态映射结构体方式操作寄存器**

​    本节对5.2.17中使用动态映射方式得到多个寄存器虚拟地址的代码进行改进，使用结构体封装的方式让我们能够方便的映射多个寄存器。

**5.3.12.内核提供的读写寄存器接口**  

​    本节介绍内核提供的writel/readl和iowrite32/ioread32等读写寄存器的接口，并且对之前的驱动进行改进，和内核中典型的驱动程序进行对比学习。

## 5.3.1.注册字符设备驱动新接口1

### 5.3.1.1、新接口与老接口

(1)老接口：register_chrdev ：关键fops结构体；主次设备号

(2)新接口：register_chrdev_region ：指定一个设备号来注册设备号；

alloc_chrdev_region ：内核自动分配设备号；

cdev             ：cdev是个结构体，里面有很多函数，可以注册register_operation结构体等等。

(3)为什么需要新接口

 

### 5.3.1.2、cdev介绍

struct cdev {

  struct kobject kobj;

  struct module *owner;

  const struct file_operations *ops;

  struct list_head list;

  dev_t dev;       //主设备号加次设备号

  unsigned int count;  //设备调用计数，比如说被调用了几次；

};

(1)结构体

(2)相关函数：cdev_alloc ：给这个结构体创建内存空间

cdev_init：   初始化，将cdev结构体和ops结构体绑定起来

cdev_add：  注册

cdev_del ：  注销

### 5.3.1.3、设备号

(1)主设备号和次设备号

(2)dev_t类型

(3) 三个宏：MKDEV：用主设备要和此设备号算出一个主次设备号；

MAJOR：提取主设备号

  MINOR：提取次设备号

### 5.3.1.4、编程实践

(1)使用register_chrdev_region + cdev_init + cdev_add进行字符设备驱动注册

 

 

## 5.3.2.注册字符设备驱动新接口2

### 5.3.2.1、实践编程

static struct cdev test_cdev;

static dev_t mydev;

\#define MYMAJOR   200

 

static int __init chrdev_init(void)

{  

  int ret = -1;

 

  printk(KERN_INFO "chrdev_init helloworld init\n");

/* //在modul_init宏调用函数中去注册字符设备驱动

  mymajor = register_chrdev(0, MYNAME, &test_fops);

  if(mymajor < 0){

​    printk(KERN_ERR "register_chrdev fail. ");

​    return -EINVAL;

  }

  printk(KERN_INFO "register_chrdev success.mymajor = %d\n", mymajor);

*/ 

  //使用新的函数注册字符设备区动

  //新的接口注册字符设备驱动需要两步

  //第一步注册/分配主次设备号

  mydev = MKDEV(MYMAJOR, 0); //第一个次设备号选择0

  ret = register_chrdev_region(mydev, MYCOUNT, MYNAME);

  if (ret){

​    printk(KERN_ERR"Uable to register minors for %s",MYNAME);

​    return -EINVAL;

  }

  printk(KERN_INFO "register_chrdev_region success!\n");

  //注册字符设备驱动

  cdev_init(&test_cdev, &test_fops);

  ret = cdev_add(&test_cdev, mydev, MYCOUNT); //完成真正的注册的

  if(ret){

​    printk(KERN_ERR"Uable to cdev_add \n");

​    return -EINVAL;

  }

  printk(KERN_INFO "cdev_add success!\n");

​    return 0;

​    }

 

static void __exit chrdev_exit(void)

{

  printk(KERN_INFO "chrdev_exit helloworld exit\n");

/* //在modul_exit调用函数中去注销字符设备驱动

  unregister_chrdev(mymajor, MYNAME);

*/

  //注销也分为两步：

  //第一步真正注销字符这杯驱动cdev_del

  cdev_del(&test_cdev);

  //第二步去注销申请的设备号

  unregister_chrdev_region(mydev, MYCOUNT);

  //使用新的函数注销字符设备区动

 

}

 

### 5.3.2.2、测试

 

 

## 5.3.3.注册字符设备驱动新接口3

### 5.3.2.1、使用alloc_chrdev_region自动分配设备号

(1)register_chrdev_region是在事先知道要使用的主、次设备号时使用的；要先查看cat /proc/devices去查看没有使用的。

(2)更简便、更智能的方法是让内核给我们自动分配一个主设备号，使用alloc_chrdev_region就可以自动分配了。

(3)自动分配的设备号，我们必须去知道他的主次设备号，否则后面没法去mknod创建他对应的设备文件。

retval = alloc_chrdev_region(&mydev, 0, MYCNT, MYNAME);

//0代表次设备号从0开始，MYCNT次设备号有几个，MYNAME：设备驱动的名称

  if (retval < 0) 

  {

​    printk(KERN_ERR "Unable to alloc minors for %s\n", MYNAME);

​    return -EINVAL ;

  }

 

### 5.3.2.2、得到分配的主设备号和次设备号

  printk(KERN_INFO "alloc_chrdev_region success\n");

 printk(KERN_INFO "major = %d, minor = %d.\n", MAJOR(mydev), MINOR(mydev));

  

(1)使用MAJOR宏和MINOR宏从dev_t得到major和minor

(2)反过来使用MKDEV宏从major和minor得到dev_t。

(3)使用这些宏的代码具有可移植性

### 5.3.2.3、中途出错的倒影式错误处理方法

(1)内核中很多函数中包含了很多个操作，这些操作每一步都有可能出错，而且出错后后面的步骤就没有进行下去的必要性了。

​     static int __init chrdev_init(void)

{  

  int retval;

  

  printk(KERN_INFO "chrdev_init helloworld init\n");

 

  // 使用新的cdev接口来注册字符设备驱动

  // 新的接口注册字符设备驱动需要2步

  

  // 第1步：分配主次设备号

  retval = alloc_chrdev_region(&mydev, 12, MYCNT, MYNAME);

  if (retval < 0) 

  {

​    printk(KERN_ERR "Unable to alloc minors for %s\n", MYNAME);

​    goto flag1;

  }  

  printk(KERN_INFO "alloc_chrdev_region success\n");

  printk(KERN_INFO "major = %d, minor = %d.\n", MAJOR(mydev), MINOR(mydev));

  

  

  // 第2步：注册字符设备驱动

  cdev_init(&test_cdev, &test_fops);

  retval = cdev_add(&test_cdev, mydev, MYCNT);

  if (retval) {

​    printk(KERN_ERR "Unable to cdev_add\n");

​    goto flag2;

  }

  printk(KERN_INFO "cdev_add success\n");

 

  

  // 使用动态映射的方式来操作寄存器，可以一次申请多个字节，然后用*(pGPJ0CON+1)的方式来访问

  if (!request_mem_region(GPJ0CON_PA, 4, "GPJ0CON"))

//   return -EINVAL;

​    goto flag3;

  if (!request_mem_region(GPJ0DAT_PA, 4, "GPJ0CON"))

//   return -EINVAL;

​    goto flag3;

  

  pGPJ0CON = ioremap(GPJ0CON_PA, 4);

  pGPJ0DAT = ioremap(GPJ0DAT_PA, 4);

  

  *pGPJ0CON = 0x11111111;

  *pGPJ0DAT = ((0<<3) | (0<<4) | (0<<5));   // 亮

  return 0;

  

// 如果第4步才出错跳转到这里来  

flag4:

  release_mem_region(GPJ0CON_PA, 4);

  release_mem_region(GPJ0DAT_PA, 4);

 

// 如果第3步才出错跳转到这里来

flag3:

  cdev_del(&test_cdev);

 

// 如果第2步才出错跳转到这里来

flag2:

  // 在这里把第1步做成功的东西给注销掉

  unregister_chrdev_region(mydev, MYCNT);

// 如果第1步才出错跳转到这里来

flag1: 

  return -EINVAL;

}

  出错了需要跳转到相应的flag去将做成功没有注销掉的资源注销掉。goto跳转到标号flag4的话，执行完里面的程序，就会接下来执行flag3，一直到flag1；

 

 

## 5.3.4.注册字符设备驱动新接口4

### 5.3.4.1、使用cdev_alloc

static struct cdev test_cdev;我们申请了一个名为test_cdev的全局变量的结构体，它所占的内存被分配在.data数据段中，这样其实是不够灵活的，因为驱动装载的时候就占用这么一块内存知道卸载驱动才得以释放。

static struct cdev *pcdev;而我们在全局变量申请一个名为pcdev的指针，这个指针占了4个字节，不管你这个strcut结构体多大，在数据段只占4个字节。然后我们在要用到这个结构体的是够去cdev_alloc分配内存，这样这个结构体指针指向了一个结构体题实例了，而在cdev_del()这个函数进行了内存释放。

**struct lian y****
** **这时候系统会为y分配一个结构体的空间****
** **但是如果****
 struct lian \*y
** **这时候系统会为y分配一个指针的空间，也就是4个字节****
** **但是y指向某个位置空间，是不可用的****
** **必须要为其malloc**[**分配内存**](https://www.baidu.com/s?wd=分配内存&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)**才可以使用****
** **编译器就是这么规定的 没有为什么**

(1)cdev_alloc的编程实践

(2)从内存角度体会cdev_alloc用与不用的差别

(3)这就是非面向对象的语言和面向对象的代码

(4)再次感叹C语言的博大精深，好好去看《4.C语言高级专题》

 

### 5.3.4.2、cdev_init的替代

  pcdev->owner = THIS_MODULE;

  pcdev->ops = &test_fops;

 

(1)cdev_init源码分析

(2)不使用cdev_init时的编程

(3)为什么讲这个

 

 

## 5.3.5.字符设备驱动注册代码分析1

### 5.3.5.1、老接口分析 

register_chrdev

​    __register_chrdev

​       __register_chrdev_region

​       cdev_alloc

​       cdev_add

 

### 5.3.5.2、新接口分析

register_chrdev_region

​    __register_chrdev_region

 

alloc_chrdev_region

​    __register_chrdev_region

 

 

## 5.3.6.字符设备驱动注册代码分析2

​    Cdev_alloc   

​       Kzalloc

​       

 

## 5.3.7.自动创建字符设备驱动的设备文件

### 5.3.7.1、问题描述：

(1)整体流程回顾

(2)使用mknod创建设备文件的缺点

(3)能否自动生成和删除设备文件

### 5.3.7.2、解决方案：udev（嵌入式中用的是mdev）

(1)什么是udev？应用层的一个应用程序

(2)内核驱动和应用层udev之间有一套信息传输机制（netlink协议）

(3)应用层启用udev，内核驱动中使用相应接口

(4)驱动注册和注销时信息会被传给udev，由udev在应用层进行设备文件的创建和删除

### 5.3.7.3、内核驱动设备类相关函数

(1)class_create

(2)device_create

### 5.3.7.4、编程实践

static int __init chrdev_init(void)

{  

 

// 注册字符设备驱动完成后，添加设备类的操作，以让内核帮我们发信息

  // 给udev，让udev自动创建和删除设备文件

  test_class = class_create(THIS_MODULE, "aston_class");

  if (IS_ERR(test_class))

​    return -EINVAL;

  // 最后1个参数字符串，就是我们将来要在/dev目录下创建的设备文件的名字

  // 所以我们这里要的文件名是/dev/test

  device_create(test_class, NULL, mydev, NULL, "test111");

  

  

 

static void __exit chrdev_exit(void)

{

​      

device_destroy(test_class, mydev);

  class_destroy(test_class);

 

 

## 5.3.8.设备类相关代码分析1

### 5.3.8.1、sys文件系统简介

(1)sys文件系统的设计思想

(2)设备类的概念

(3)/sys/class/xxx/中的文件的作用

 

(4)class 底下存放的都是各种类别的驱动，而aston_class这个类就是我们刚刚装载我们程序生成的。这个目录就是内核给我们应用层的一扇窗户。

### 5.3.8.2、

(1)

class_create

​    __class_create

​       __class_register  //向内核注册这个类

​           kset_register

​              kobject_uevent //udev event

​    

(2)

device_create

​    device_create_vargs

​       kobject_set_name_vargs

​       device_register

​           device_add

​              kobject_add

​                  device_create_file

​                  device_create_sys_dev_entry

​                  devtmpfs_create_node

​                  device_add_class_symlinks

​                  device_add_attrs

​                  device_pm_add

​                  kobject_uevent

​                  这层函数创建改类别的虚拟文件中相关的文件。             

 

## 5.3.10.静态映射表建立过程分析

### 5.3.10.1、建立映射表的三个关键部分

​    建立了页表，MMU才可以去查页表表现里面存的虚拟地址对应的物理地址。

(1)映射表具体物理地址和虚拟地址的值相关的宏定义

(2)映射表建立函数。该函数负责由(1)中的映射表来建立linux内核的页表映射关系。

在kernel/arch/arm/mach-s5pv210/mach-smdkc110.c中的smdkc110_map_io函数

smdkc110_map_io

​    s5p_init_io

​       iotable_init

结论：经过分析，真正的内核移植时给定的静态映射表在arch/arm/plat-s5p/cpu.c中的s5p_iodesc，本质是一个结构体数组，数组中每一个元素就是一个映射，这个映射描述了一段物理地址到虚拟地址之间的映射。这个结构体数组所记录的几个映射关系被iotable_init所使用，该函数负责将这个结构体数组格式的表建立成MMU所能识别的页表映射关系，这样在开机后可以直接使用相对应的虚拟地址来访问对应的物理地址。

   struct map_desc {

  unsigned long virtual;  //虚拟地址起始值

  unsigned long pfn;    //物理地址转成pfn page frame number (页表表项编号)，也是起始值

  unsigned long length;  //映射的长度

  unsigned int type;    //

};

 

static struct map_desc s5p_iodesc[] __initdata = {

  {

​    .virtual  = (unsigned long)S5P_VA_CHIPID,

​    .pfn    = __phys_to_pfn(S5P_PA_CHIPID),

​    .length   = SZ_4K,

​    .type    = MT_DEVICE,

  }, {

​    .virtual  = (unsigned long)S3C_VA_SYS,

​    .pfn    = __phys_to_pfn(S5P_PA_SYSCON),

​    .length   = SZ_64K,

​    .type    = MT_DEVICE,

  },

 

(3)开机时调用映射表建立函数

问题：开机时（kernel启动时）smdkc110_map_io怎么被调用的？

start_kernel

​    setup_arch

​       paging_init

​           devicemaps_init

​           

if (mdesc->map_io)

​              mdesc->map_io();

​           

 

## 5.3.11.动态映射结构体方式操作寄存器

### 5.3.11.1、问题描述

(1)仿效真实驱动中，用结构体封装的方式来进行单次多寄存器的地址映射。来代替我们**5.2.17****节中讲的多次映射。**

之前我们单次只能映射一个寄存器

// 使用动态映射的方式来操作寄存器

  if (!request_mem_region(GPJ0CON_PA, 4, "GPJ0CON"))

​    return -EINVAL;

  if (!request_mem_region(GPJ0DAT_PA, 4, "GPJ0CON"))

​    return -EINVAL;

  

  pGPJ0CON = ioremap(GPJ0CON_PA, 4);

  pGPJ0DAT = ioremap(GPJ0DAT_PA, 4);

  

  *pGPJ0CON = 0x11111111;

  *pGPJ0DAT = ((0<<3) | (0<<4) | (0<<5));   // 亮

 

### 5.3.11.2、实践编码

用结构体封装的方式来进行单次多寄存器的地址映射

typedef struct GPJ0REG

{

  volatile unsigned int gpj0con;

  volatile unsigned int gpj0dat;

}gpj0_reg_t;

 

\#define MYMAJOR   200

\#define MYNAME   "testchar"

 

\#define GPJ0CON   S5PV210_GPJ0CON

\#define GPJ0DAT   S5PV210_GPJ0DAT

 

\#define rGPJ0CON  *((volatile unsigned int *)GPJ0CON)

\#define rGPJ0DAT  *((volatile unsigned int *)GPJ0DAT)

 

//#define GPJ0CON_PA  0xe0200240

//#define GPJ0DAT_PA  0xe0200244

\#define GPJ0_REGBASE  0xe0200240

 

//unsigned int *pGPJ0CON;

//unsigned int *pGPJ0DAT;

gpj0_reg_t *pGPJ0REG;

 

int mymajor;

 

char kbuf[100];     // 内核空间的buf

 

 

static int __init chrdev_init(void)

{  

  printk(KERN_INFO "chrdev_init helloworld init\n");

 

  // 在module_init宏调用的函数中去注册字符设备驱动

  // major传0进去表示要让内核帮我们自动分配一个合适的空白的没被使用的主设备号

  // 内核如果成功分配就会返回分配的主设备好；如果分配失败会返回负数

  mymajor = register_chrdev(0, MYNAME, &test_fops);

  if (mymajor < 0)

  {

​    printk(KERN_ERR "register_chrdev fail\n");

​    return -EINVAL;

  }

  printk(KERN_INFO "register_chrdev success... mymajor = %d.\n", mymajor);

  

/* 

  // 使用动态映射的方式来操作寄存器

  if (!request_mem_region(GPJ0CON_PA, 4, "GPJ0CON"))

​    return -EINVAL;

  if (!request_mem_region(GPJ0DAT_PA, 4, "GPJ0CON"))

​    return -EINVAL;

  

  pGPJ0CON = ioremap(GPJ0CON_PA, 4);

  pGPJ0DAT = ioremap(GPJ0DAT_PA, 4);

  

  *pGPJ0CON = 0x11111111;

  *pGPJ0DAT = ((0<<3) | (0<<4) | (0<<5));   // 亮

*/

  // 2步完成了映射

  if (!request_mem_region(GPJ0_REGBASE, sizeof(gpj0_reg_t), "GPJ0REG"))

​    return -EINVAL;

  pGPJ0REG = ioremap(GPJ0_REGBASE, sizeof(gpj0_reg_t));

  // 映射之后用指向结构体的指针来进行操作

  // 指针使用->结构体内元素的方式来操作各个寄存器

  pGPJ0REG->gpj0con = 0x11111111;

  pGPJ0REG->gpj0dat = ((0<<3) | (0<<4) | (0<<5));   // 亮

 

  return 0;

}

 

static void __exit chrdev_exit(void)

{

  printk(KERN_INFO "chrdev_exit helloworld exit\n");

 

// *pGPJ0DAT = ((1<<3) | (1<<4) | (1<<5)); 

  pGPJ0REG->gpj0dat = ((1<<3) | (1<<4) | (1<<5)); 

  

  // 解除映射

/*

  iounmap(pGPJ0CON);

  iounmap(pGPJ0DAT);

  release_mem_region(GPJ0CON_PA, 4);

  release_mem_region(GPJ0DAT_PA, 4);

*/

  iounmap(pGPJ0REG);

  release_mem_region(GPJ0_REGBASE, sizeof(gpj0_reg_t));

  

  // 在module_exit宏调用的函数中去注销字符设备驱动

  unregister_chrdev(mymajor, MYNAME);

  

// rGPJ0DAT = ((1<<3) | (1<<4) | (1<<5));

}

 

### 5.3.11.3、分析和总结

​    原来的方式需要多次申请动态映射内存，如果有多个寄存器那将会非常不方便，这个时候我们将那个寄存器做成一个结构体，申请动态内存一次申请的地址就是这个结构体的大小。

 

## 5.3.12.内核提供的读写寄存器接口

### 5.3.12.1、前面访问寄存器的方式

(1)行不行？

​    觉得可以的，之前我们裸机操控寄存器就是用的这种方法

(2)好不好？

​    不好，移植性差。ARM架构是统一编制的访问IO和内存一样，X86构架下就不一样了，这样的访问方式的在不同的架构下代码就需要大量的更改。

### 5.3.12.2、内核提供的寄存器读写接口

 

(1)writel和readl

(2)iowrite32和ioread32

 

### 5.3.12.3、代码实践

\#define MYMAJOR   200

\#define MYNAME   "testchar"

 

\#define GPJ0CON   S5PV210_GPJ0CON

\#define GPJ0DAT   S5PV210_GPJ0DAT

 

\#define rGPJ0CON  *((volatile unsigned int *)GPJ0CON)

\#define rGPJ0DAT  *((volatile unsigned int *)GPJ0DAT)

 

\#define GPJ0CON_PA 0xe0200240

\#define GPJ0DAT_PA 0xe0200244

 

\#define S5P_GPJ0REG(x)   (x)

\#define S5P_GPJ0CON     S5P_GPJ0REG(0)

\#define S5P_GPJ0DAT     S5P_GPJ0REG(4)

 

unsigned int *pGPJ0CON;

unsigned int *pGPJ0DAT;

 

static void __iomem *baseaddr;     // 寄存器的虚拟地址的基地址类型

 

int mymajor;

char kbuf[100];     // 内核空间的buf

 

// 模块安装函数

static int __init chrdev_init(void)

{  

  printk(KERN_INFO "chrdev_init helloworld init\n");

 

  // 在module_init宏调用的函数中去注册字符设备驱动

  // major传0进去表示要让内核帮我们自动分配一个合适的空白的没被使用的主设备号

  // 内核如果成功分配就会返回分配的主设备好；如果分配失败会返回负数

  mymajor = register_chrdev(0, MYNAME, &test_fops);

  if (mymajor < 0)

  {

​    printk(KERN_ERR "register_chrdev fail\n");

​    return -EINVAL;

  }

  printk(KERN_INFO "register_chrdev success... mymajor = %d.\n", mymajor);

  

  

  // 使用动态映射的方式来操作寄存器

/* if (!request_mem_region(GPJ0CON_PA, 4, "GPJ0CON"))

​    return -EINVAL;

  if (!request_mem_region(GPJ0DAT_PA, 4, "GPJ0CON"))

​    return -EINVAL;

  

  pGPJ0CON = ioremap(GPJ0CON_PA, 4);

  pGPJ0DAT = ioremap(GPJ0DAT_PA, 4);

*/ 

// *pGPJ0CON = 0x11111111;

// *pGPJ0DAT = ((0<<3) | (0<<4) | (0<<5));   // 亮

  // 测试1：用2次ioremap得到的动态映射虚拟地址来操作，测试成功

// writel(0x11111111, pGPJ0CON);

// writel(((0<<3) | (0<<4) | (0<<5)), pGPJ0DAT);

  

  // 测试2：用静态映射的虚拟地址来操作，测试成功

// writel(0x11111111, GPJ0CON);

// writel(((0<<3) | (0<<4) | (0<<5)), GPJ0DAT);

  

  // 测试3：用1次ioremap映射多个寄存器得到虚拟地址，测试成功

  if (!request_mem_region(GPJ0CON_PA, 8, "GPJ0BASE"))

​    return -EINVAL;

  baseaddr = ioremap(GPJ0CON_PA, 8);

  

  writel(0x11111111, baseaddr + S5P_GPJ0CON);

  writel(((0<<3) | (0<<4) | (0<<5)), baseaddr + S5P_GPJ0DAT);

  

 

  return 0;

}

 

// 模块下载函数

static void __exit chrdev_exit(void)

{

  printk(KERN_INFO "chrdev_exit helloworld exit\n");

 

  //*pGPJ0DAT = ((1<<3) | (1<<4) | (1<<5));

  //writel(((1<<3) | (1<<4) | (1<<5)), pGPJ0DAT); 

  //writel(((1<<3) | (1<<4) | (1<<5)), GPJ0DAT); 

  //writel(((1<<3) | (1<<4) | (1<<5)), baseaddr + S5P_GPJ0DAT);  

  

/* 

  // 解除映射

  iounmap(pGPJ0CON);

  iounmap(pGPJ0DAT);

  release_mem_region(GPJ0CON_PA, 4);

  release_mem_region(GPJ0DAT_PA, 4);

*/

  iounmap(baseaddr);

  release_mem_region(baseaddr, 8);

  

  // 在module_exit宏调用的函数中去注销字符设备驱动

  unregister_chrdev(mymajor, MYNAME);

  

// rGPJ0DAT = ((1<<3) | (1<<4) | (1<<5));

}

 

 

### 5.3.12.4、分析和总结

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 



 

# 第四章 驱动框架入门之LED

## 5.4.0 章节概要

**5.4.1.何谓驱动框架**

​    本节主要解释了什么是驱动框架，为什么需要驱动框架，基于驱动框架写驱动有什么优势等问题。

**5.4.2.内核驱动框架中LED的基本情况**

​    本节综合分析了2.6.35.7内核中LED驱动框架的整体设计，相关的文件位置，九鼎移植时LED驱动的实现方法等。

**5.4.3.初步分析led驱动框架源码1**

​    本节对led框架的核心代码led-class.c文件进行分析，主要分析了subsys_initcall宏。

**5.4.4.初步分析led驱动框架源码2**

​    本节接上节继续分析led-class.c文件，主要分析了led_classdev_register函数。

**5.4.5.在内核中添加或去除某个驱动**

​    本节讲述如何在内核源码目录中添加或去除某个驱动，并且做实验进行去除实验和添加实验，观察sysfs中的变化。

**5.4.6.基于驱动框架写led驱动1**

​    本节首先分析当前基于led驱动框架如何去编写程序，把思路理顺，然后动手编写好驱动源代码并编译通过。

**5.4.7.基于驱动框架写led驱动2**

​    本节对上节的代码进行实践操作，并且在实践中结合源码进行分析，通过这样的对比分析让大家更深入的理解驱动代码。

**5.4.8.基于驱动框架写led驱动3**

​    本节在上节基础上，讲述如何将4个LED分开实现，这样的实现更加灵活，也是真实驱动中采用的方式。

**5.4.9.linux内核的gpiolib学习1**

​    本节开始学习gpiolib，主要讲了学习重点的三条主线，和本部分的学习方法，并且结合前面的课程找到了gpiolib建立的入口。

**5.4.10.linux内核的gpiolib学习2**

​    本节重点讲了gpiolib中对gpio信息的封装结构体，以及内核中记录各个GPIO的编号方法宏定义等。

**5.4.11.linux内核的gpiolib学习3**

​    本节接上节继续分析gpiolib，主要分析了记录芯片中gpio bank的主结构体变量

**5.4.12.linux内核的gpiolib学习4**

​    本节接上节继续分析gpiolib，本节到了三星驱动工程师写的硬件操作函数部分，主要是设置GPIO为输入模式、输出模式的2个函数。

**5.4.13.linux内核的gpiolib学习5**

​    本节开始分析gpiolib的驱动框架部分，这部分是内核开发者编写的。本节主要讲述其中开放给goiolib使用者的接口

**5.4.14.linux内核的gpiolib学习6**   

​    本节接上节继续分析gpiolib的驱动框架部分，本节继续分析其中开放给gpiolib使用者的接口。

**5.4.15.linux内核的gpiolib学习7**   

​    本节接上节继续分析gpiolib的驱动框架部分，主要分析gpiolib自己工作的部分，其中重点是各个attribute的实现代码。

**5.4.16.使用gpiolib完成led驱动**   

​    本节使用gpiolib来操作硬件，继续改造我们之前写的led驱动，这个改造后得到的驱动就是一个真实的LED设备驱动了。

**5.4.17.将驱动添加到内核中**

​    本节讲述如何将一个写好的驱动程序源码添加到内核中，并且可以在内核中去配置该驱动的编译，这在实际工作中很有用。

## 5.4.1.何谓驱动框架

### 5.4.1.1、驱动是谁写的

(1)驱动开发工程师   

(2)内核维护者

### 5.4.1.2、驱动编程协作要求

(1)接口标准化

(2)尽量降低驱动开发者难度

### 5.4.1.3、到底什么是驱动框架

(1)内核中驱动部分维护者针对每个种类的驱动设计一套成熟的、标准的、典型的驱动实现，然后把不同厂家的同类硬件驱动中相同的部分抽出来自己实现好，再把不同部分留出接口给具体的驱动开发工程师来实现，这就叫驱动框架。

(2)内核维护者在内核中设计了一些统一管控系统资源的体系，这些体系让内核能够对资源在各个驱动之间的使用统一协调和分配，保证整个内核的稳定健康运行。譬如系统中所有的GPIO就属于系统资源，每个驱动模块如果要使用某个GPIO就要先调用特殊的接口先申请，申请到后使用，使用完后要释放。又譬如中断号也是一种资源，驱动在使用前也必须去申请。这也是驱动框架的组成部分。

(3)一些特定的接口函数、一些特定的数据结构，这些是驱动框架的直接表现。

 

 

## 5.4.2.内核驱动框架中LED的基本情况

### 5.4.2.1、相关文件

(1)drivers/leds目录，这个目录就是驱动框架规定的LED这种硬件的驱动应该待的地方。

(2)led-class.c和led-core.c，这两个文件加起来属于LED驱动框架的第一部分，这两个文件是内核开发者提供的，他们描述的是内核中所有厂家的不同LED硬件的相同部分的逻辑。

(3)leds-xxxx.c，这个文件是LED驱动框架的第2部分，是由不同厂商的驱动工程师编写添加的，厂商驱动工程师结合自己公司的硬件的不同情况来对LED进行操作，使用第一部分提供的接口来和驱动框架进行交互，最终实现驱动的功能。

### 5.4.2.2、九鼎移植的内核中led驱动

(1)九鼎实际未使用内核推荐的led驱动框架

(2)drivers/char/led/x210-led.c

### 5.4.2.3、案例分析驱动框架的使用

(1)以leds-s3c24xx.c为例。leds-s3c24xx.c中通过调用led_classdev_register来完成我们的LED驱动的注册，而led_classdev_register是在drivers/leds/led-class.c中定义的。所以其实SoC厂商的驱动工程师是调用内核开发者在驱动框架中提供的接口来实现自己的驱动的。

(2)驱动框架的关键点就是：分清楚内核开发者提供了什么，驱动开发者自己要提供什么

### 5.4.2.4、典型的驱动开发行业现状

(1)内核开发者对驱动框架进行开发和维护、升级，对应led-class.c和led-core.c

(2)SoC厂商的驱动工程师对设备驱动源码进行编写、调试，提供参考版本，对应leds-s3c24xx.c

(3)做产品的厂商的驱动工程师以SoC厂商提供的驱动源码为基础，来做移植和调试

 

 

## 5.4.3_4.初步分析led驱动框架源码1_2

### 5.4.3.1、涉及到的文件

(1)led-core.c

(2)led-class.c

### 5.4.3.2、subsys_initcall

(1)经过基本分析，发现LED驱动框架中内核开发者实现的部分主要是led-class.c

(2)我们发现led-class.c就是一个内核模块，对led-class.c分析应该从下往上，遵从对模块的基本分析方法。

(3)为什么LED驱动框架中内核开发者实现的部分要实现成一个模块？因为内核开发者希望这个驱动框架是可以被装载/卸载的。这样当我们内核使用者不需要这个驱动框架时可以完全去掉，需要时可以随时加上。

**查找方法：grep “subsys_initcall” \* -nR**

(4)subsys_initcall是一个宏，定义在linux/init.h中。经过对这个宏进行展开，发现这个宏的功能是：将其声明的函数放到一个特定的段：.initcall4.init。

subsys_initcall

​    __define_initcall("4",fn,4)

\#define __define_initcall(level,fn,id) \

  static initcall_t __initcall_##fn##id __used \

  __attribute__((__section__(".initcall" level ".init"))) = fn

(5)分析module_init宏，可以看出它将函数放到了.initcall6.init段中。

module_init

  __initcall

​      device_initcall

​         __define_initcall("6",fn,6)

(6)内核在启动过程中需要顺序的做很多事，内核如何实现按照先后顺序去做很多初始化操作。内核的解决方案就是给内核启动时要调用的所有函数归类，然后每个类按照一定的次序去调用执行。这些分类名就叫.initcalln.init。n的值从1到8。内核开发者在编写内核代码时只要将函数设置合适的级别，这些函数就会被链接的时候放入特定的段，内核启动时再按照段顺序去依次执行各个段即可。

(7)经过分析，可以看出，subsys_initcall和module_init的作用是一样的，只不过前者所声明的函数要比后者在内核启动时的执行顺序更早。

 

### 5.4.3.3、led_class_attrs

  leds_class->dev_attrs = led_class_attrs;

​    

​     static struct device_attribute led_class_attrs[] = {

  __ATTR(brightness, 0644, led_brightness_show, led_brightness_store),

  __ATTR(max_brightness, 0444, led_max_brightness_show, NULL),

\#ifdef CONFIG_LEDS_TRIGGERS

  __ATTR(trigger, 0644, led_trigger_show, led_trigger_store),

\#endif

  __ATTR_NULL,

};

(1)什么是attribute，对应将来/sys/class/leds/目录里的内容，一般是文件和文件夹。这些文件其实就是sysfs开放给应用层的一些操作接口（非常类似于/dev/目录下的那些设备文件）

(2)attribute有什么用，作用就是让应用程序可以通过/sys/class/leds/目录下面的属性文件来操作驱动进而操作硬件设备。

(3)attribute其实是另一条驱动实现的路线。有区别于之前讲的file_operations那条线。

 

### 5.4.3.4、led_classdev_register

led_classdev_register

​    device_create

  led_cdev->dev = device_create(leds_class, parent, 0, led_cdev,

​           "%s", led_cdev->name);

(1)分析可知，led_classdev_register这个函数其实就是去创建一个属于leds这个类的一个设备。其实就是去注册一个设备。所以这个函数其实就是led驱动框架中内核开发者提供给SoC厂家驱动开发者的一个注册驱动的接口。

(2)当我们使用led驱动框架去编写驱动的时候，这个led_classdev_register函数的作用类似于我们之前使用file_operations方式去注册字符设备驱动时的register_chrdev函数。

 

 

## 5.4.5.在内核中添加或去除某个驱动

### 5.4.5.1、去除九鼎移植的LED驱动

 

(1)九鼎移植的驱动在应用层的接口在/sys/devices/platform/x210-led/目录下，有led1、led2、led3、led4四个设备文件，各自管理一个led。

(2)要去掉九鼎自己移植的led驱动，要在make menucofig中去掉选择项，然后重新make得到zImage，然后重启时启动这个新的zImage即可。

(3)新的内核启动后，如果/sys/devices/platform/目录下已经没有了x210-led这个目录，就说明我们去掉这个驱动成功了。

Device divers

​    Character devices

​       [ ]x210 led diver

(4)为什么make menuconfig就能去掉这个驱动？

 

 

### 5.4.5.2、添加led驱动框架支持

(1)当前内核中是没有LED驱动框架的，我们要去添加它。

​    Device divers

​           [*]LED Support

​              <*>LED Class Support

​    

### 5.4.5.3、sysfs中的内容分析

 

Mmc0 ~3，这是个mmc的四个通道调用了这个led_classdev_register 这个函数，所以才会出现这4个文件。

### 5.4.5.4、后续展望：完成leds-x210.c

​    在这个驱动框架下完成这个驱动

 

## 5.4.6.基于驱动框架写led驱动1

### 5.4.6.1、分析

(1)参考哪里？ drivers/leds/leds-s3c24xx.c

(2)关键点：led_classdev_register

### 5.4.6.2、动手写led驱动模块

\#include <linux/module.h>    // module_init module_exit

\#include <linux/init.h>     // __init  __exit

\#include <linux/fs.h>

\#include <linux/leds.h>

 

struct led_classdev mydev;

 

/*

struct led_classdev {

  const char   *name;

  int     brightness;

  int     max_brightness;

  int     flags;

   //Set LED brightness level 

   //Must not sleep, use a workqueue if needed 

  void    (*brightness_set)(struct led_classdev *led_cdev,

​           enum led_brightness brightness);

*/

static void s5pv210_led_set(struct led_classdev *led_cdev,

​        enum led_brightness value)

{

​    printk(KERN_INFO"s5pv210_led_set");

}

 

// 模块安装函数

static int __init s5pv210_led_init(void)

{  

  //用户insmod安装驱动模块式会调用该函数

  //该函数的主要任务就是使用led驱动框架提供的设备注册函数来注册一个设备

  int ret =-1;

  mydev.name = "myled";

  mydev.brightness = 0;

  mydev.brightness_set = s5pv210_led_set;

​    ret = led_classdev_register(NULL, &mydev);

  if (ret < 0) {

​    printk(KERN_ERR "led_classdev_register failed\n");

​    return ret;

  }

 

  return 0;

}

 

// 模块卸载函数

static void __exit s5pv210_led_exit(void)

{

  led_classdev_unregister(&mydev);

}

 

module_init(s5pv210_led_init);

module_exit(s5pv210_led_exit);

 

// MODULE_xxx这种宏作用是用来添加模块描述信息

MODULE_LICENSE("GPL");         // 描述模块的许可证

MODULE_AUTHOR("Jay <jay4xie@qq.com>"); // 描述模块的作者

MODULE_DESCRIPTION("module test");   // 描述模块的介绍信息

MODULE_ALIAS("s5pv210 LED");      // 描述模块的别名信息

 

## 5.4.7.基于驱动框架写led驱动2

### 5.4.7.1、代码实践

 

(1)调试

(2)分析

通过分析看出：

第1：我们写的驱动确实工作了，被加载了，/sys/class/leds/目录下确实多出来了一个表示设备的文件夹。文件夹里面有相应的操控led硬件的2个属性brightness和max_brightness

 

static int __init leds_init(void)

{

  leds_class = class_create(THIS_MODULE, "leds");

  if (IS_ERR(leds_class))

​    return PTR_ERR(leds_class);

  leds_class->suspend = led_suspend;

  leds_class->resume = led_resume;

  leds_class->dev_attrs = led_class_attrs;

  return 0;

}

 

static struct device_attribute led_class_attrs[] = {

  __ATTR(brightness, 0644, led_brightness_show, led_brightness_store),

  __ATTR(max_brightness, 0444, led_max_brightness_show, NULL),

\#ifdef CONFIG_LEDS_TRIGGERS

  __ATTR(trigger, 0644, led_trigger_show, led_trigger_store),

\#endif

  __ATTR_NULL,

};

 

static ssize_t led_brightness_show(struct device *dev, 

​    struct device_attribute *attr, char *buf)

{

  struct led_classdev *led_cdev = dev_get_drvdata(dev);

 

  /* no lock needed for this */

  led_update_brightness(led_cdev);

 

  return sprintf(buf, "%u\n", led_cdev->brightness);

}

 

static ssize_t led_brightness_store(struct device *dev,

​    struct device_attribute *attr, const char *buf, size_t size)

{

  struct led_classdev *led_cdev = dev_get_drvdata(dev);

  ssize_t ret = -EINVAL;

  char *after;

  unsigned long state = simple_strtoul(buf, &after, 10);

  size_t count = after - buf;

 

  if (isspace(*after))

​    count++;

 

  if (count == size) {

​    ret = count;

 

​    if (state == LED_OFF)

​      led_trigger_remove(led_cdev);

​    led_set_brightness(led_cdev, state);

  }

 

  return ret;

}

第2：led-class.c中brightness方法有一个show方法和store方法，这两个方法对应用户在/sys/class/leds/myled/brightness目录下直接去读写这个文件时实际执行的代码。

当我们show brightness时，实际就会执行led_brightness_show函数

当我们echo 1 > brightness时，实际就会执行led_brightness_store函数

(3)show方法实际要做的就是读取LED硬件信息，然后把硬件信息返回给我们即可。所以show方法和store方法必要要会去操控硬件。但是led-class.c文件又属于驱动框架中的文件，它本身无法直接读取具体硬件，因此在show和store方法中使用函数指针的方式调用了struct led_classdev结构体中的相应的读取/写入硬件信息的方法。

(4)struct led_classdev结构体中的实际用来读写硬件信息的函数，就是我们自己写的驱动文件leds-s5pv210.c中要提供的。

 

### 5.4.7.2、添加硬件操作

\#include <linux/module.h>    // module_init module_exit

\#include <linux/init.h>     // __init  __exit

\#include <linux/fs.h>

\#include <linux/leds.h>

\#include <linux/io.h>

\#include <linux/ioport.h>

\#include <mach/regs-gpio.h>

\#include <mach/gpio-bank.h> 

 

//静态映射

\#define GPJ0CON   S5PV210_GPJ0CON

\#define GPJ0DAT   S5PV210_GPJ0DAT

 

struct led_classdev mydev;

 

/*

struct led_classdev {

  const char   *name;

  int     brightness;

  int     max_brightness;

  int     flags;

   //Set LED brightness level 

   //Must not sleep, use a workqueue if needed 

  void    (*brightness_set)(struct led_classdev *led_cdev,

​           enum led_brightness brightness);

*/

static void s5pv210_led_set(struct led_classdev *led_cdev,

​        enum led_brightness value)

{

​    printk(KERN_INFO"s5pv210_led_set\n");

​    //在这里根据用户设置的值来操作硬件

​    //用户设置的是就是value

​    if(value == LED_OFF){

​      //用户给了个0，希望led灭

​      writel(0x11111111, GPJ0CON);  

​      writel(((1<<3) | (1<<4) | (1<<5)), GPJ0DAT);

​    }else{

​      //用户给的是非零，希望led亮

​      writel(0x11111111, GPJ0CON);  

​      writel(((0<<3) | (0<<4) | (0<<5)), GPJ0DAT);

​    }

}

 

// 模块安装函数

static int __init s5pv210_led_init(void)

{  

  //用户insmod安装驱动模块式会调用该函数

  //该函数的主要任务就是使用led驱动框架提供的设备注册函数来注册一个设备

  int ret =-1;

  mydev.name = "myled";

  mydev.brightness = 1;

  mydev.brightness_set = s5pv210_led_set;

​    ret = led_classdev_register(NULL, &mydev);

  if (ret < 0) {

​    printk(KERN_ERR "led_classdev_register failed\n");

​    return ret;

  }

 

  return 0;

}

 

// 模块卸载函数

static void __exit s5pv210_led_exit(void)

{

  led_classdev_unregister(&mydev);

}

 

module_init(s5pv210_led_init);

module_exit(s5pv210_led_exit);

 

// MODULE_xxx这种宏作用是用来添加模块描述信息

MODULE_LICENSE("GPL");         // 描述模块的许可证

MODULE_AUTHOR("Jay <jay4xie@qq.com>"); // 描述模块的作者

MODULE_DESCRIPTION("module test");   // 描述模块的介绍信息

MODULE_ALIAS("s5pv210 LED");      // 描述模块的别名信息

 

 

## 5.4.8.基于驱动框架写led驱动3

### 5.4.8.1、在驱动中将4个LED分开

(1)好处。驱动层实现对各个LED设备的独立访问，并向应用层展示出4个操作接口led1、led2、led3、led4，这样应用层可以完全按照自己的需要对LED进行控制。

**驱动的设计理念：**不要对最终需求功能进行假定，而应该只是直接的对硬件的操作。有一个概念就是：机制和策略的问题。在硬件操作上驱动只应该提供机制而不是策略。策略由应用程序来做。

(2)如何实现

\#include <linux/module.h>    // module_init module_exit

\#include <linux/init.h>     // __init  __exit

\#include <linux/fs.h>

\#include <linux/leds.h>

\#include <linux/io.h>

\#include <linux/ioport.h>

\#include <mach/regs-gpio.h>

\#include <mach/gpio-bank.h> 

 

//静态映射

\#define GPJ0CON   S5PV210_GPJ0CON

\#define GPJ0DAT   S5PV210_GPJ0DAT

 

struct led_classdev mydev1;

struct led_classdev mydev2;

struct led_classdev mydev3;

/*

struct led_classdev {

  const char   *name;

  int     brightness;

  int     max_brightness;

  int     flags;

   //Set LED brightness level 

   //Must not sleep, use a workqueue if needed 

  void    (*brightness_set)(struct led_classdev *led_cdev,

​           enum led_brightness brightness);

*/

static void s5pv210_led1_set(struct led_classdev *led_cdev,

​        enum led_brightness value)

{    

​    printk(KERN_INFO"s5pv210_led1_set\n");

​    writel(0x11111111, GPJ0CON);  

​    //在这里根据用户设置的值来操作硬件

​    //用户设置的是就是value

 

​    if(value == LED_OFF){

​      //用户给了个0，希望led灭

​      //读改写三部曲

​      writel((readl(GPJ0DAT) | (1 << 3)), GPJ0DAT);

​    }else{

​      //用户给的是非零，希望led亮

​      writel((readl(GPJ0DAT) & ~(1 << 3)), GPJ0DAT);

​    }

}

 

static void s5pv210_led2_set(struct led_classdev *led_cdev,

​        enum led_brightness value)

{    

​    printk(KERN_INFO"s5pv210_led2_set\n");

​    writel(0x11111111, GPJ0CON);  

​    if(value == LED_OFF){

 

​      writel((readl(GPJ0DAT) | (1 << 4)), GPJ0DAT);

​    }else{

​      writel((readl(GPJ0DAT) & ~(1 << 4)), GPJ0DAT);

​    }

}

 

static void s5pv210_led3_set(struct led_classdev *led_cdev,

​        enum led_brightness value)

{    

​    printk(KERN_INFO"s5pv210_led3_set\n");

​    writel(0x11111111, GPJ0CON);  

​    if(value == LED_OFF){

 

​      writel((readl(GPJ0DAT) | (1 << 5)), GPJ0DAT);

​    }else{

​      writel((readl(GPJ0DAT) & ~(1 << 5)), GPJ0DAT);

​    }

}

 

// 模块安装函数

static int __init s5pv210_led_init(void)

{  

  //用户insmod安装驱动模块式会调用该函数

  //该函数的主要任务就是使用led驱动框架提供的设备注册函数来注册一个设备

  int ret =-1;

  //led 1

  mydev1.name = "led1";

  mydev1.brightness = 0;

  mydev1.brightness_set = s5pv210_led1_set;

​    ret = led_classdev_register(NULL, &mydev1);

  if (ret < 0) {

​    printk(KERN_ERR "led_classdev_register failed\n");

​    return ret;

  }

 

  //led 2

  mydev2.name = "led2";

  mydev2.brightness = 0;

  mydev2.brightness_set = s5pv210_led2_set;

​    ret = led_classdev_register(NULL, &mydev2);

  if (ret < 0) {

​    printk(KERN_ERR "led_classdev_register failed\n");

​    return ret;

  }

 

  //led 3

  mydev3.name = "led3";

  mydev3.brightness = 0;

  mydev3.brightness_set = s5pv210_led3_set;

​    ret = led_classdev_register(NULL, &mydev3);

  if (ret < 0) {

​    printk(KERN_ERR "led_classdev_register failed\n");

​    return ret;

  }

  return 0;

}

 

// 模块卸载函数

static void __exit s5pv210_led_exit(void)

{

  led_classdev_unregister(&mydev1);

  led_classdev_unregister(&mydev2);

  led_classdev_unregister(&mydev3);

}

 

module_init(s5pv210_led_init);

module_exit(s5pv210_led_exit);

 

// MODULE_xxx这种宏作用是用来添加模块描述信息

MODULE_LICENSE("GPL");         // 描述模块的许可证

MODULE_AUTHOR("Jay <jay4xie@qq.com>"); // 描述模块的作者

MODULE_DESCRIPTION("module test");   // 描述模块的介绍信息

MODULE_ALIAS("s5pv210 LED");      // 描述模块的别名信息

 



 

 

### 5.4.8.2、和leds-s3c24xx.c的不同

static int __init s3c24xx_led_init(void)

{

  return platform_driver_register(&s3c24xx_led_driver);

}

 

static void __exit s3c24xx_led_exit(void)

{

  platform_driver_unregister(&s3c24xx_led_driver);

}

 

module_init(s3c24xx_led_init);

module_exit(s3c24xx_led_exit);

 

static int s3c24xx_led_probe(struct platform_device *dev)

{

  struct s3c24xx_led_platdata *pdata = dev->dev.platform_data;

  struct s3c24xx_gpio_led *led;

  int ret;

 

  led = kzalloc(sizeof(struct s3c24xx_gpio_led), GFP_KERNEL);

  if (led == NULL) {

​    dev_err(&dev->dev, "No memory for device\n");

​    return -ENOMEM;

  }

 

  platform_set_drvdata(dev, led);

 

  led->cdev.brightness_set = s3c24xx_led_set;

  led->cdev.default_trigger = pdata->def_trigger;

  led->cdev.name = pdata->name;

  led->cdev.flags |= LED_CORE_SUSPENDRESUME;

 

  led->pdata = pdata;

 

  /* no point in having a pull-up if we are always driving */

 

  if (pdata->flags & S3C24XX_LEDF_TRISTATE) {

​    s3c2410_gpio_setpin(pdata->gpio, 0);

​    s3c2410_gpio_cfgpin(pdata->gpio, S3C2410_GPIO_INPUT);

  } else {

​    s3c2410_gpio_pullup(pdata->gpio, 0);

​    s3c2410_gpio_setpin(pdata->gpio, 0);

​    s3c2410_gpio_cfgpin(pdata->gpio, S3C2410_GPIO_OUTPUT);

  }

 

  /* register our new led device */

 

  ret = led_classdev_register(&dev->dev, &led->cdev);

  if (ret < 0) {

​    dev_err(&dev->dev, "led_classdev_register failed\n");

​    kfree(led);

​    return ret;

  }

 

  return 0;

}

 

 

platform driver 平台驱动 还有个platform device 会导致 s3c24xx_led_probe()函数会被执行。

### 5.4.8.3、gpiolib引入

(1)一个事实：很多硬件都要用到GPIO、GPIO会复用，比如是声音的gpio，边听音乐边看电影，这时候就出现了两种声音，这时候gpio就复用。

(2)如果同一个GPIO被2个驱动同时控制了，就会出现bug

(3)内核提供gpiolib来统一管理系统中所有GPIO

(4)gpiolib本身属于驱动框架的一部分

​    Gpiolib不是一种法律而是一种道德约束。

 

## 5.4.9.linux内核的gpiolib学习1

### 5.4.9.1、gpiolib学习重点

(1)gpiolib的建立过程

(2)gpiolib的使用方法：申请、使用、释放

(3)gpiolib的架构：涉及哪些目录的哪些文件

### 5.4.9.2、gpiolib的学习方法

(1)以一条主线进去，坚持主线

(2)中途遇到杂碎知识，彻底搞定之，然后继续主线

(3)随时做笔记以加深理解和记忆

(4)学习途中注意架构思想，提升自己大脑的空间复杂度

### 5.4.9.3、主线1：gpiolib的建立

(1)找到目标函数\arch\arm\mach-s5pv210\gpiolib.c

smdkc110_map_io

​        s5pv210_gpiolib_init      这个函数就是我们gpiolib初始化的函数

​    

 

## 5.4.10.linux内核的gpiolib学习2

__init int s5pv210_gpiolib_init(void)

{

  struct s3c_gpio_chip *chip = s5pv210_gpio_4bit;

  int nr_chips = ARRAY_SIZE(s5pv210_gpio_4bit); //当前系统中端口的数量

  int i = 0;

 

  for (i = 0; i < nr_chips; i++, chip++) {

​    if (chip->config == NULL)

​      **chip->config = &gpio_cfg;**

​    if (chip->base == NULL)

​      **chip->base = S5PV210_BANK_BASE(i);**

  }

 

  samsung_gpiolib_add_4bit_chips(s5pv210_gpio_4bit, nr_chips);

 

  return 0;

}

**static struct s3c_gpio_cfg gpio_cfg = {**

  .set_config = s3c_gpio_setcfg_s3c64xx_4bit,

  .set_pull  = s3c_gpio_setpull_updown,

  .get_pull  = s3c_gpio_getpull_updown,

};

 

  

**#define S5PV210_BANK_BASE(bank_nr) (S5P_VA_GPIO + ((bank_nr) \* 0x20))**

//每一端口和下一个端口的差值是x20;如图所示

 

 

__init int s5pv210_gpiolib_init(void)这个函数初始化了gpiolib首先我们来看 第一句话的s5pv210_gpio_4bit这个结构体，以及s5pv210_gpio_4bit；

### 5.4.10.1、struct s3c_gpio_chip

//\arch\arm\plat-samsung\include\plat\gpio-core.h

struct s3c_gpio_chip {

  struct gpio_chip  chip;   //jpio的属性信息，

  struct s3c_gpio_cfg *config;

  struct s3c_gpio_pm *pm;

  void __iomem    *base; //这个是io端口操作寄存器组的基地址（虚拟地址）

  int     eint_offset;

  spinlock_t    lock;

\#ifdef CONFIG_PM

  u32     pm_save[7];

\#endif

};

 

//\include\asm-generic\gpio.h

struct gpio_chip {

  const char   *label;    //端口的名字

  struct device    *dev;

  struct module    *owner;

 

  int     (*request)(struct gpio_chip *chip,  //请求分配io

​            unsigned offset);

  void      (*free)(struct gpio_chip *chip,//用完了释放io

​            unsigned offset);

 

  int     (*direction_input)(struct gpio_chip *chip,  //将io模式设置为输入模式

​            unsigned offset);

  int     (*get)(struct gpio_chip *chip,    //读取IO的值

​            unsigned offset);

  int     (*direction_output)(struct gpio_chip *chip, //将io模式设置为输出模式

​            unsigned offset, int value);

  int     (*set_debounce)(struct gpio_chip *chip,

​            unsigned offset, unsigned debounce);

 

  void      (*set)(struct gpio_chip *chip,  //设置IO的值

​            unsigned offset, int value);

 

  int     (*to_irq)(struct gpio_chip *chip,

​            unsigned offset);

 

  void      (*dbg_show)(struct seq_file *s,

​            struct gpio_chip *chip);

  int     base;   //这个base是当前这个io所在的io端口的基准编号

  u16     ngpio;

  const char   *const *names;

  unsigned    can_sleep:1;

  unsigned    exported:1;

};

 

 (1)这个结构体是一个GPIO端口的抽象,这个结构体的一个变量就可以完全的描述一个IO端口。

(2)端口和IO口是两个概念。S5PV210有很多个IO口（160个左右），这些IO口首先被分成N个端口（port group），然后每个端口中又包含了M个IO口。譬如GPA0是一个端口，里面包含了8个IO口，我们一般记作：GPA0_0（或GPA0.0）、GPA0_1、

(3)内核中为每个GPIO分配了一个编号，编号是一个数字（譬如一共有160个IO时编号就可以从1到160连续分布），编号可以让程序很方便的去识别每一个GPIO。

### 5.4.10.2、s5pv210_gpio_4bit

//kernel_jiuding\arch\arm\mach-s5pv210\gpiolib.c

static struct s3c_gpio_chip s5pv210_gpio_4bit[] = {

  {

​    .chip  = {

​      .base  = S5PV210_GPA0(0),  //当前端口的基础编号

​      .ngpio = **S5PV210_GPIO_A0_NR**, //当前端口拥有的IO口数量

​      .label = "GPA0",       //当前端口的名字

​      .to_irq = s5p_gpiolib_gpioint_to_irq, //点前端口中IO口换算成对应的中断口方法

​    },

  }, {

​    .chip  = {

​      .base  = S5PV210_GPA1(0),

​      .ngpio = S5PV210_GPIO_A1_NR,

​      .label = "GPA1",

​      .to_irq = s5p_gpiolib_gpioint_to_irq,

​    },

  }

**……**

{

​    .base  = (S5P_VA_GPIO + 0xC20),   //虚拟地址的基址

​    .config = &gpio_cfg_noint,

​    .eint_offset = IRQ_EINT(8),

​    .chip  = {

​      .base  = S5PV210_GPH1(0),

​      .ngpio = S5PV210_GPIO_H1_NR,

​      .label = "GPH1",

​      .to_irq = s5p_gpiolib_eint_to_irq,

​    }

**……**

(1)这个东西是一个结构体数组，数组中包含了很多个struct s3c_gpio_chip类型的变量。

 

 

## 5.4.11.linux内核的gpiolib学习3

### 5.4.11.1、S5PV210_GPA0宏

//kernel_jiuding\arch\arm\mach-s5pv210\gpiolib.c

static struct s3c_gpio_chip s5pv210_gpio_4bit[] = {

  {

​     .chip  = {

​      .base  = S5PV210_GPA0(0),  //当前端口的基础编号

​      .ngpio = **S5PV210_GPIO_A0_NR**, //当前端口拥有的IO口数量

​      .label = "GPA0",       //当前端口的名字

​      .to_irq = s5p_gpiolib_gpioint_to_irq, //点前端口中IO口换算成对应的中断口方法

​    },

  }, {

​    .chip  = {

​      .base  = S5PV210_GPA1(0),

​      .ngpio = S5PV210_GPIO_A1_NR,

​      .label = "GPA1",

​      .to_irq = s5p_gpiolib_gpioint_to_irq,

​    },

  }

**……**

 

//\arch\arm\mach-s5pv210\include\mach\gpio.h

/* S5PV210 GPIO number definitions */

\#define S5PV210_GPA0(_nr)  (S5PV210_GPIO_A0_START + (_nr))

\#define S5PV210_GPA1(_nr)  (S5PV210_GPIO_A1_START + (_nr))

\#define S5PV210_GPB(_nr)  (S5PV210_GPIO_B_START + (_nr))

 

enum s5p_gpio_number {

  S5PV210_GPIO_A0_START  = 0,

  **S5PV210_GPIO_A1_START**  **=** **S5PV210_GPIO_NEXT****(S5PV210_GPIO_A0),**

  S5PV210_GPIO_B_START  = S5PV210_GPIO_NEXT(S5PV210_GPIO_A1),

  S5PV210_GPIO_C0_START  = S5PV210_GPIO_NEXT(S5PV210_GPIO_B),

 

\#define S5PV210_GPIO_NEXT(__gpio) \

  ((__gpio##_START) + (__gpio##_NR) + CONFIG_S3C_GPIO_SPACE + 1)

 

**展开：**

\#define S5PV210_GPIO_NEXT(__gpio) \

  ((S5PV210_GPIO_A0_start) + (S5PV210_GPIO_A0_NR)+**CONFIG_S3C_GPIO_SPACE** + 1)

  

/* GPIO bank sizes */

\#define S5PV210_GPIO_A0_NR (8)

\#define S5PV210_GPIO_A1_NR (4)

\#define S5PV210_GPIO_B_NR  (8)

\#define S5PV210_GPIO_C0_NR (5)

\#define S5PV210_GPIO_C1_NR (5)

\#define S5PV210_GPIO_D0_NR (4)

 

//kernel/.config

CONFIG_S5P_GPIO_DRVSTR=y

CONFIG_SAMSUNG_GPIO_EXTRA=0

**CONFIG_S3C_GPIO_SPACE=0**

CONFIG_S3C_GPIO_TRACK=y

 

​    最终**S5PV210_GPIO_A1_START** **=  0 + 8 + 0 + 1 = 9** 

(1)这个宏的返回值就是GPA0端口的某一个IO口的编号值，传参就是我们这个IO口在GPA0端口中的局部编号。

(2)samsung_gpiolib_add_4bit_chips这个函数才是具体进行gpiolib的注册的。这个函数接收的参数是我们当前文件中定义好的结构体数组s5pv210_gpio_4bit（其实2个参数分别是数组名和数组元素个数），这个数组中其实就包含了当前系统中所有的IO端口的信息（这些信息包含：端口的名字、端口中所有GPIO的编号、端口操作寄存器组的虚拟地址基地址、端口中IO口的数量、端口上下拉等模式的配置函数、端口中的IO口换算其对应的中断号的函数）。

 

 

## 5.4.12.linux内核的gpiolib学习4

### 5.4.12.1、几个问题

__init int s5pv210_gpiolib_init(void)

{

  struct s3c_gpio_chip *chip = s5pv210_gpio_4bit;

  int nr_chips = ARRAY_SIZE(s5pv210_gpio_4bit);

  int i = 0;

 

  for (i = 0; i < nr_chips; i++, chip++) {

​    if (chip->config == NULL)

​      chip->config = &gpio_cfg;

​    if (chip->base == NULL)

​      chip->base = S5PV210_BANK_BASE(i);

  }

 

  samsung_gpiolib_add_4bit_chips(s5pv210_gpio_4bit, nr_chips);

​    //4bit_chips表示4个bit位对应一个io口

  return 0;

}

 

//\kernel_jiuding\arch\arm\plat-samsung\gpiolib.c

void __init samsung_gpiolib_add_4bit_chips(struct s3c_gpio_chip *chip,

​            int nr_chips)

{

  for (; nr_chips > 0; nr_chips--, chip++) {

​    samsung_gpiolib_add_4bit(chip);

​    s3c_gpiolib_add(chip);

  }

}

 

void __init samsung_gpiolib_add_4bit(struct s3c_gpio_chip *chip)

{

  chip->chip.direction_input = samsung_gpiolib_4bit_input;  //这里仅仅只是把函数地址复制给他

  chip->chip.direction_output = **samsung_gpiolib_4bit_output**;

  chip->pm = __gpio_pm(&s3c_gpio_pm_4bit); //低功耗模式

}

 

static int samsung_gpiolib_4bit_input(struct gpio_chip *chip,

​           unsigned int offset) //chip 精确到端口（如GPA0）offset 精确到io （GPA0_5中的_5）

{

  struct s3c_gpio_chip *ourchip = to_s3c_gpio(chip);

  void __iomem *base = ourchip->base;

  unsigned long con;

  //读改写三部曲

  con = __raw_readl(base + GPIOCON_OFF); //层级较高的readl , GPIOCON_OFF =0x00 ,

con &= ~(0xf << **con_4bit_shift**(offset));

**#define con_4bit_shift(__off) ((__off) \* 4)** 

  __raw_writel(con, base + GPIOCON_OFF);

 

  gpio_dbg("%s: %p: CON now %08lx\n", __func__, base, con);

 

  return 0;

}

 

static inline struct **s3c_gpio_chip** *to_s3c_gpio(struct gpio_chip *gpc)

//这个函数由gpio_chip 得到 s3c_gpio_chip的地址

{

  return container_of(gpc, struct s3c_gpio_chip, chip);

}

  

static int **samsung_gpiolib_4bit_output**(struct gpio_chip *chip,

​            unsigned int offset, int value)

{

  struct s3c_gpio_chip *ourchip = to_s3c_gpio(chip);

  void __iomem *base = ourchip->base;

  unsigned long con;

  unsigned long dat;

 

  con = __raw_readl(base + GPIOCON_OFF);

  con &= ~(0xf << con_4bit_shift(offset));

  con |= 0x1 << con_4bit_shift(offset);

 

  dat = __raw_readl(base + GPIODAT_OFF);

 

  if (value)     //处理用户输入的数据

​    dat |= 1 << offset;

  else

​    dat &= ~(1 << offset);

 

  __raw_writel(dat, base + GPIODAT_OFF);

  __raw_writel(con, base + GPIOCON_OFF);

  __raw_writel(dat, base + GPIODAT_OFF);

 

  gpio_dbg("%s: %p: CON %08lx, DAT %08lx\n", __func__, base, con, dat);

 

  return 0;

}

 

(1)哪个目录的哪个文件

(2)函数名中为什么有个4bit：三星的CPU中2440的CON寄存器是2bit对应一个IO口，而6410和210以及之后的系列中CON寄存器是4bit对应1个IO口。所以gpiolib在操作2440和210的CON寄存器时是不同的。

### 5.4.12.2、函数调用关系

samsung_gpiolib_add_4bit_chips

​    samsung_gpiolib_add_4bit

​    s3c_gpiolib_add

经过分析，发现samsung_gpiolib_add_4bit内部其实并没有做gpiolib的注册工作，而是还在做填充，填充的是每一个GPIO被设置成输入模式/输出模式的操作方法。

 

 

## 5.4.13_14.linux内核的gpiolib学习5_6

### 5.4.13.1、s3c_gpiolib_add

//\arch\arm\plat-samsung\gpiolib.c

void __init samsung_gpiolib_add_4bit_chips(struct s3c_gpio_chip *chip,

​            int nr_chips)

{

  for (; nr_chips > 0; nr_chips--, chip++) {

​    samsung_gpiolib_add_4bit(chip);

​    s3c_gpiolib_add(chip);

  }

}

 

//\arch\arm\plat-samsung\gpio.c

__init void s3c_gpiolib_add(struct s3c_gpio_chip *chip)

{

  struct gpio_chip *gc = &chip->chip;  //struct gpio结构体中的chip，代表一个端口 

  int ret;

 

  BUG_ON(!chip->base);  //Debug调试信息

  BUG_ON(!gc->label);

  BUG_ON(!gc->ngpio);

 

  spin_lock_init(&chip->lock); //自旋锁

 

  if (!gc->direction_input) //之前的已经设置了output input

​    gc->direction_input = s3c_gpiolib_input; 

  if (!gc->direction_output)

​    gc->direction_output = s3c_gpiolib_output;

  if (!gc->set)

​    gc->set = s3c_gpiolib_set;

  if (!gc->get)

​    gc->get = s3c_gpiolib_get;

 

\#ifdef CONFIG_PM

  if (chip->pm != NULL) {

​    if (!chip->pm->save || !chip->pm->resume)

​      printk(KERN_ERR "gpio: %s has missing PM functions\n",

​          gc->label);

  } else

​    printk(KERN_ERR "gpio: %s has no PM function\n", gc->label);

\#endif

 

  /* gpiochip_add() prints own failure message on error. */

  ret = gpiochip_add(gc);  //这个函数是内核开发者写的 ，完成真正的注册

  if (ret >= 0)

​    s3c_gpiolib_track(chip);

}

 

 

(1)首先检测并完善chip的direction_input/direction_ouput/set/get这4个方法

(2)然后调用gpiochip_add方法进行真正的注册操作。其实这个注册就是将我们的封装了一个GPIO端口的所有信息的chip结构体变量挂接到内核gpiolib模块定义的一个gpio_desc数组中的某一个格子中。

//\kernel_jiuding\arch\arm\plat-samsung\gpiolib.c 这个文件里面是210 6410这种4bitCON寄存器类型的操作方法

//\arch\arm\plat-samsung\gpio.c 这个文件是24xx这种2bit CON寄存器的操作方法。

 

### 5.4.13.2、从驱动框架角度再来分析一下gpiolib

(1)之前的分析已经告一段落，截至目前我们已经搞清楚了gpiolib的建立工程。但是这只是整个gpiolib建立的一部分，是厂商驱动工程师负责的那一部分；还有另一部分是内核开发者提供的驱动框架的那一部分，就是我们后面要去分析的第2条主线。

(2)drivers/gpio/gpiolib.c这个文件中所有的函数构成了我们第2部分，也就是内核开发者写的gpiolib框架部分。这个文件中提供的函数主要有以下部分：

**gpiochip_add:**   是框架开出来的接口，给厂商驱动工程师用，用于向内核注册我们的gpiolib

**gpio_request**:    是框架开出来的接口，给使用gpiolib来**编写自己的驱动的驱动工程师用**，驱动中要想使用某一个gpio，就必须先调用gpio_request接口来向内核的gpiolib部分申请，得到允许后才可以去使用这个gpio。

**gpio_free**:       对应gpio_request，用来释放申请后用完了的gpio

**gpio_request_one/gpio_request_array:**  这两个是gpio_request的变种    

**gpiochip_is_requested:**  接口用来判断某一个gpio是否已经被申请了

**gpio_direction_input/gpio_direction_output**: 接口用来设置GPIO为输入/输出模式，

注意该函数内部实际并没有对硬件进行操作，只是通过chip结构体变量的函数指针调用了将来SoC厂商的驱动工程师写的真正的操作硬件实现gpio设置成输出模式的那个函数。

​              

以上的接口属于一类，这些都是给写其他驱动并且用到了gpiolib的人使用的

剩下的还有另外一类函数，这类函数是gpiolib内部自己的一些功能实现的代码

​              

 

## 5.4.15.linux内核的gpiolib学习7

### 5.4.15.1、gpiolib的attribute部分

(1)CONFIG_GPIO_SYSFS

​    这个宏在 .config 文件中被配置成y

(2)GPIO的attribute演示

### 5.4.15.2、相关代码分析

(1)

 

gpiolib_sysfs_init

​    gpiochip_export

​       sysfs_create_group

 

在/sys/class/gpio

​       使用 echo 23 > export 用来显示 编号为23 的gpio 的属性

​           echo 23 > unexport 取消显示

## 5.4.16.使用gpiolib完成led驱动

### 5.4.16.1、流程分析

(1)第1步：使用gpio_request申请要使用的一个GPIO

(2)第2步：gpio_direction_input/gpio_direction_output 设置输入/输出模式

(3)第3步：设置输出值gpio_set_value 获取IO口值gpio_get_value

### 5.4.16.2、代码实践

\#include <linux/module.h>    // module_init module_exit

\#include <linux/init.h>     // __init  __exit

\#include <linux/fs.h>

\#include <linux/leds.h>

\#include <mach/regs-gpio.h>

\#include <mach/gpio-bank.h>

\#include <linux/io.h>

\#include <linux/ioport.h>

\#include <mach/gpio.h>

 

\#define GPIO_LED1  S5PV210_GPJ0(3)

\#define GPIO_LED2  S5PV210_GPJ0(4)

\#define GPIO_LED3  S5PV210_GPJ0(5)

 

\#define X210_LED_OFF  1      // X210中LED是正极接电源，负极节GPIO

\#define X210_LED_ON   0      // 所以1是灭，0是亮

 

static struct led_classdev mydev1;     // 定义结构体变量

static struct led_classdev mydev2;     // 定义结构体变量

static struct led_classdev mydev3;     // 定义结构体变量

 

// 这个函数就是要去完成具体的硬件读写任务的

static void s5pv210_led1_set(struct led_classdev *led_cdev,

​        enum led_brightness value)

{

  printk(KERN_INFO "s5pv210_led1_set\n");

  

  //writel(0x11111111, GPJ0CON);

  

  // 在这里根据用户设置的值来操作硬件

  // 用户设置的值就是value

  if (value == LED_OFF)

  {

​    // 用户给了个0，希望LED灭

​    //writel(0x11111111, GPJ0CON);

​    // 读改写三部曲

​    //writel((readl(GPJ0DAT) | (1<<3)), GPJ0DAT);

​    gpio_set_value(GPIO_LED1, X210_LED_OFF);

  }

  else

  {

​    // 用户给的是非0，希望LED亮

​    //writel(0x11111111, GPJ0CON);

​    //writel((readl(GPJ0DAT) & ~(1<<3)), GPJ0DAT);

​    gpio_set_value(GPIO_LED1, X210_LED_ON);

  }

}

 

static void s5pv210_led2_set(struct led_classdev *led_cdev,

​        enum led_brightness value)

{

  printk(KERN_INFO "s5pv2102_led_set\n");

  

  //writel(0x11111111, GPJ0CON);

  

  // 在这里根据用户设置的值来操作硬件

  // 用户设置的值就是value

  if (value == LED_OFF)

  {

​    // 用户给了个0，希望LED灭

​    //writel(0x11111111, GPJ0CON);

​    // 读改写三部曲

​    //writel((readl(GPJ0DAT) | (1<<4)), GPJ0DAT);

  }

  else

  {

​    // 用户给的是非0，希望LED亮

​    //writel(0x11111111, GPJ0CON);

​    //writel((readl(GPJ0DAT) & ~(1<<4)), GPJ0DAT);

  }

}

 

static void s5pv210_led3_set(struct led_classdev *led_cdev,

​        enum led_brightness value)

{

  printk(KERN_INFO "s5pv210_led3_set\n");

  

  //writel(0x11111111, GPJ0CON);

  

  // 在这里根据用户设置的值来操作硬件

  // 用户设置的值就是value

  if (value == LED_OFF)

  {

​    // 用户给了个0，希望LED灭

​    //writel(0x11111111, GPJ0CON);

​    // 读改写三部曲

​    //writel((readl(GPJ0DAT) | (1<<5)), GPJ0DAT);

  }

  else

  {

​    // 用户给的是非0，希望LED亮

​    //writel(0x11111111, GPJ0CON);

​    //writel((readl(GPJ0DAT) & ~(1<<5)), GPJ0DAT);

  }

}

 

static int __init s5pv210_led_init(void)

{

  // 用户insmod安装驱动模块时会调用该函数

  // 该函数的主要任务就是去使用led驱动框架提供的设备注册函数来注册一个设备

  int ret = -1;

  

  // 在这里去申请驱动用到的各种资源，当前驱动中就是GPIO资源

  if (gpio_request(GPIO_LED1, "led1_gpj0.3")) //返回值不是0表示失败

  {

​    printk(KERN_ERR "gpio_request failed\n");

  } 

  else 

  {

​    // 设置为输出模式，并且默认输出1让LED灯灭

​    gpio_direction_output(GPIO_LED1, 1);

  }

  

  

  

  // led1

  mydev1.name = "led1";

  mydev1.brightness = 0; 

  mydev1.brightness_set = s5pv210_led1_set;

  

  ret = led_classdev_register(NULL, &mydev1);

  if (ret < 0) {

​    printk(KERN_ERR "led_classdev_register failed\n");

​    return ret;

  }

  

  // led2

  mydev2.name = "led2";

  mydev2.brightness = 0; 

  mydev2.brightness_set = s5pv210_led2_set;

  

  ret = led_classdev_register(NULL, &mydev2);

  if (ret < 0) {

​    printk(KERN_ERR "led_classdev_register failed\n");

​    return ret;

  }

  

  // led3

  mydev3.name = "led3";

  mydev3.brightness = 0; 

  mydev3.brightness_set = s5pv210_led3_set;

  

  ret = led_classdev_register(NULL, &mydev3);

  if (ret < 0) {

​    printk(KERN_ERR "led_classdev_register failed\n");

​    return ret;

  }

  

  return 0;

}

 

static void __exit s5pv210_led_exit(void)

{

  led_classdev_unregister(&mydev1);

  led_classdev_unregister(&mydev2);

  led_classdev_unregister(&mydev3);

  

  gpio_free(GPIO_LED1);

}

 

module_init(s5pv210_led_init);

module_exit(s5pv210_led_exit);

 

// MODULE_xxx这种宏作用是用来添加模块描述信息

MODULE_LICENSE("GPL");             // 描述模块的许可证

MODULE_AUTHOR("aston <1264671872@qq.com>");   // 描述模块的作者

MODULE_DESCRIPTION("s5pv210 led driver");    // 描述模块的介绍信息

MODULE_ALIAS("s5pv210_led");          // 描述模块的别名信息



 

 

(1)在led1上编写代码测试通过

(2)扩展支持led2和led3、led4.可以分开注册也可以使用gpio_request_array去一次注册

(3)学习linux中查看gpio使用情况的方法

内核中提供了虚拟文件系统debugfs，里面有一个gpio文件，提供了gpio的使用信息。

使用方法：**mount -t debugfs debugfs  /tmp**，然后cat /tmp/gpio即可得到gpio的所有信息，使用完后umount /tmp卸载掉debugfs

 

 

## 5.4.17.将驱动添加到内核中

### 5.4.17.1、驱动的存在形式

(1)野生，优势是方便调试开发，所以在开发阶段都是这种

(2)家养，优势可以在内核配置时make menuconfig决定内核怎么编译，方便集成

### 5.4.17.2、驱动开发的一般步骤

(1)以模块的形式在外部编写、调试

(2)将调试好的驱动代码集成到kernel中

### 5.4.17.3、实践

(1)关键点：Kconfig、Makefile、make menuconfig

(2)操作步骤：

第1步：将写好的驱动源文件放入内核源码中正确的目录下

第2步：在Makefile中添加相应的依赖

obj-$(CONFIG_LEDS_S5PV210)       += leds-s5pv210.o

第3步：在Kconfig中添加相应的配置项

config LEDS_CLASS

​    tristate "LED Class Support"

​    help

​     This option enables the led sysfs class in /sys/class/leds. You'll

​     need this to do anything useful with LEDs. If unsure, say N.

 

第4步：make menuconfig

Menuconfig 设置成模块将在源码树的相应的设置模块的文件目录底下编程.ko驱动文件。

 

 

​    

目录和文件结构：

mach-s5pv210/gpiolib.c      s5pv210_gpiolib_init

mach-s5pv210/include/mach/gpio.h        #define S5PV210_GPA0(_nr)   (S5PV210_GPIO_A0_START + (_nr))

arch/arm/plat-samsung/gpiolib.c       里面是210/6410这种4bit CON寄存器类型的操作方法

arch/arm/plat-samsung/gpio.c     里面是24XX这种2bit CON寄存器类型的操作方法

drivers/gpio/gpiolib.c            里面是内核开发者提供的gpiolib的驱动框架部分



 

# 第五章 linux设备驱动模型

## 5.5.0 章节概要

**5.5.1.linux设备驱动模型简介**

​    本节介绍linux设备驱动模型诞生的原因、技术特征、以及其对驱动开发编写调试的影响，主要目的是让大家全方位感受一下到底什么是linux的设备驱动模型。

**5.5.2.设备驱动模型的底层架构**

​    本节讲解设备驱动模型的最底层实现模块，主要有三个：kobject、kobj_type、kset。

**5.5.3.总线式设备驱动组织方式**

​    本节详解linux内核中以总线为连接的驱动组织形式，详细分析了类、总线、设备、驱动等几个概念及其关联。

**5.5.4.platform平台总线简介1**  

​    本节讲述platform平台总线的目的、意义、工作方式等，注意本节的关键在于platform自己本身的构建和工作原理，下节课才会讲如何使用平台总线来组织设备和驱动。

**5.5.5.platform平台总线工作原理2**

​    本节首先讲解平台总线的工作流程，然后重点讲了平台总线本身自己构建的过程。

**5.5.6.platform平台总线工作原理3**

​    本节以内核中自带的三星为2440写的基于平台总线的LED驱动源码为例，分析了如何在平台总线下去编写驱动程序。

**5.5.7.平台总线实践环节1**

​    本节开始实战编写X210下基于平台总线的LED驱动，本节把上个课程的LED驱动源码拿来改写成平台总线制式，先实现platform_driver。

**5.5.8.平台总线实践环节2**

​    本节分析系统移植时的mach文件，然后找到并添加LED相关的platform_device的注册。

**5.5.9.平台总线实践环节3**

​    本节同时调试platform_device和platform_driver，进行问题排除和代码测试、讲解。

**5.5.10.平台总线实践环节4** 

​    本节重点是硬件配置信息中的数据如何从device端传递到driver端，并且被driver接收用于硬件的操作方法中。

## 5.5.1.linux设备驱动模型简介

### 5.5.1.1、什么是设备驱动模型

(1)**类class**、**总线bus**（将设备和驱动挂接起来）、**设备device**、**驱动driver**：这四个都是linux的结构体，在内核里面用来表示这四个概念；

(2**)kobject**；这也是个结构体kernel object （内核的一个物体），表示内核里面的一个对象。

**对象生命周期**：对象的诞生于消亡，在kobject能让每一个对象有自我管理的特性。

(3)**sysfs**：虚拟文件系统，内核空间和用户空间建立关系，将内核里一些值可以通过文件的形式展现出来；

(4)**udev**：为了实现内核空间和用户空间的信息的转换，可以在用户空间及时得到内核中的信息，比如说驱动加载了，被卸载了。

### 5.5.1.2、为什么需要设备驱动模型

(1)早期内核（2.4之前）没有统一的设备驱动模型，但照样可以用

(2)2.6版本中正式引入设备驱动模型，目的是在设备越来越多，功耗要求等新特性要求的情况下让驱动体系更易用、更优秀。

(3)设备驱动模型负责统一实现和维护一些特性，诸如：电源管理、热插拔、对象生命周期、用户空间和驱动空间的交互等基础设施

(4)设备驱动模型目的是简化驱动程序编写，但是客观上设备驱动模型本身设计和实现很复杂。

### 5.5.1.3、驱动开发的2个点

(1)驱动源码本身编写、调试。重点在于对硬件的了解。

(2)驱动什么时候被安装、驱动中的函数什么时候被调用。跟硬件无关，完全和设备驱动模型有关。**设备驱动已经写好了，用设备驱动模型来解决什么时候加载驱动什么时候卸载驱动。**

 

 

## 5.5.2.设备驱动模型的底层架构

### 5.5.2.1、kobject

struct kobject {

  const char   *name; 

  struct list_head  entry; //链表的节点

  struct kobject   *parent; //和别的kobject上下层之间的挂接

​                //，不是链表平行之间的挂接

  struct kset   *kset;   //结构体中提供上锁功能

  struct kobj_type  *ktype; // 结构体中提供对用户空间

  struct sysfs_dirent *sd;

  struct kref   kref;  //他被引用了几次，对象引用计数，生命周期

  unsigned int state_initialized:1;

  unsigned int state_in_sysfs:1;

  unsigned int state_add_uevent_sent:1;

  unsigned int state_remove_uevent_sent:1;

  unsigned int uevent_suppress:1;

};

 

(1)定义在linux/kobject.h中

(2)各种对象最基本单元，提供一些公用型服务如：对象引用计数、维护对象链表、对象上锁、对用户空间的表示。结构体提供了一些成员变量和一些函数指针，就构成了服务

(3)设备驱动模型中的各种对象其内部都会包含一个kobject

(4)地位相当于面向对象体系架构中的**总基类**

### 5.5.2.2、kobj_type

struct kobj_type {

  void (*release)(struct kobject *kobj);

  const struct sysfs_ops *sysfs_ops;

  struct attribute **default_attrs;

  const struct kobj_ns_type_operations *(*child_ns_type)(struct kobject *kobj);

  const void *(*namespace)(struct kobject *kobj);

};

(1)很多书中简称为ktype，每一个kobject都需要**绑定**一个ktype来提供相应功能

(2)关键点1：sysfs_ops，提供该对象在sysfs中的操作方法（show和store）

(2)关键点2：attribute，提供在sysfs中以文件形式存在的属性，其实就是应用接口

Kobj_type就相当于一个文件，ops 和attribute就相当于操作的方法。

### 5.5.2.3、kset

struct kset {

  struct list_head list;

  spinlock_t list_lock;

  struct kobject kobj;

  const struct kset_uevent_ops *uevent_ops;

};

 

  

(1)kset的主要作用是做顶层kobject的容器类

(2)kset的主要目的是将各个kobject（代表着各个对象）组织出目录层次架构

(3)可以认为kset就是为了在sysfs中弄出目录，从而让设备驱动模型中的多个对象能够有层次有逻辑性的组织在一起

 

 

## 5.5.3.总线式设备驱动组织方式

### 5.5.3.1、总线

(1)物理上的真实总线及其作用（英文bus）

(2)驱动框架中的总线式设计

(3)bus_type结构体，关键是match函数和uevent函数

struct bus_type {

  const char   *name;

  struct bus_attribute  *bus_attrs;

  struct device_attribute *dev_attrs;

  struct driver_attribute *drv_attrs;

 

  int (*match)(struct device *dev, struct device_driver *drv);

  int (*uevent)(struct device *dev, struct kobj_uevent_env *env);

  int (*probe)(struct device *dev);

  int (*remove)(struct device *dev);

  void (*shutdown)(struct device *dev);

 

  int (*suspend)(struct device *dev, pm_message_t state);

  int (*resume)(struct device *dev);

 

  const struct dev_pm_ops *pm;

 

  struct bus_type_private *p;

};

 

 

### 5.5.3.2、设备

(1)struct device是硬件设备在内核驱动框架中的抽象

(2)device_register用于向内核驱动框架注册一个设备

(3)通常device不会单独使用，而是被包含在一个具体设备结构体中，如struct usb_device

### 5.5.3.3、驱动

(1)struct device_driver是驱动程序在内核驱动框架中的抽象

(2)关键元素1：name，驱动程序的名字，很重要，经常被用来作为驱动和设备的匹配依据

(3)关键元素2：probe，驱动程序的探测函数，用来检测一个设备是否可以被该驱动所管理。Probe函数能不能和设备对上，这样驱动和设备就对上了。

### 5.5.3.4、类

(1)相关结构体：struct class 和 struct class_device

(2)udev的使用离不开class

(3)class的真正意义在于作为同属于一个class的多个设备的容器。也就是说，class是一种人造概念，**目的就是为了对各种设备进行分类管理，设备有不同的管理路线，bus也是管理设备的，比方说usb摄像头设备，属于usb类bus，属于影像设备的class，usb硬盘属于ubs类bus，属于储存类class**。当然，class在分类的同时还对每个类贴上了一些“标签”，这也是设备驱动模型为我们写驱动提供的基础设施。

### 5.5.3.5、总结

(1)模型思想很重要，其实就是面向对象的思想

(2)全是结构体套结构体，对基本功（语言功底和大脑复杂度）要求很高

 

 

## 5.5.4.platform平台总线工作原理1

### 5.5.4.1、何为平台总线

(1)相对于usb、pci、i2c等物理总线来说，platform总线是虚拟的、抽象出来的。

(2)回顾裸机中讲的，CPU与外部通信的2种方式：地址总线式连接和专用接口式连接。平台总线对应地址总线式连接设备，也就是SoC内部集成的各种内部外设。

(3)思考：为什么要有平台总线？进一步思考：为什么要有总线的概念？

### 5.5.4.2、平台总线下管理的2员大将

(1)platform工作体系都定义在drivers/base/platform.c中

(2)两个结构体：platform_device和platform_driver

(3)两个接口函数：platform_device_register和platform_driver_register

 

struct platform_device {

  const char * name;     // 平台总线下设备的名字，不能重名

  int   id;

  struct device  dev;    // 所有设备通用的属性部分

  u32   num_resources;   // 设备使用到的resource的个数，

  struct resource * resource; // 设备使用到的资源数组的首地址

 

  const struct platform_device_id *id_entry; // 设备ID表

 

  /* arch specific additions */

  struct pdev_archdata  archdata;      // 自留地，用来提供扩展性的

};

 

struct platform_driver {

  int (*probe)(struct platform_device *);   // 驱动探测函数，当我们驱动找到一个设备的时候，我们probe函数就试图去看看是不是我们能去驱动的设备，一般就是对设备初始化，看看设备有没有反应。

  int (*remove)(struct platform_device *);  // 去掉一个设备，在我们当前驱动支持的设备去除掉

  void (*shutdown)(struct platform_device *); // 关闭一个设备，只是把这个设备关掉了，卸载是在系统中去除掉

  int (*suspend)(struct platform_device *, pm_message_t state);//待机是将设备挂起

  int (*resume)(struct platform_device *);//从挂起状态唤醒重新工作

  struct device_driver driver;        // 所有设备共用的一些属性

  const struct platform_device_id *id_table; // 设备ID表， 驱动支持哪些设备

};

 

 

## 5.5.5.platform平台总线工作原理2

### 5.5.5.1、平台总线体系的工作流程

(1)第一步：系统启动时在bus系统中注册platform

(2)第二步：内核移植的人负责提供platform_device

(3)第三步：写驱动的人负责提供platform_driver

(4)第四步：platform的match函数发现driver和device匹配后，调用driver的probe函数来完成驱动的初始化和安装，然后设备就工作起来了

### 5.5.5.2、代码分析：platform本身注册

//\drivers\base\platform.c

int __init platform_bus_init(void)

{

  int error;

 

  early_platform_cleanup();

 

  error = device_register(&platform_bus);

  if (error)

​    return error;

  error = bus_register(&platform_bus_type);

  if (error)

​    device_unregister(&platform_bus);

  return error;

}

 

struct bus_type platform_bus_type = {

  .name    = "platform",

  .dev_attrs = platform_dev_attrs,

  .match   = platform_match,

  .uevent   = platform_uevent,

  .pm   = &platform_dev_pm_ops,

};

 

(1)每种总线（不光是platform，usb、i2c那些也是）都会带一个match方法，match方法用来对总线下的device和driver进行匹配。理论上每种总线的匹配算法是不同的，但是实际上一般都是看name的。

 

static int platform_match(struct device *dev, struct device_driver *drv)

{

struct platform_device *pdev = **to_platform_device**(dev); //用 container_of宏 将结构体的一个成员反推到宏的起始地址就得到了整个宏，也就是platform_device 宏， 为什么不直接传platform_device类型呢？因为你不知道要传进来的plaform_device 是什么类型的总线，

  struct platform_driver *pdrv = to_platform_driver(drv);

**#define to_platform_device(x) container_of((x), struct platform_device, dev)**

 

  /* match against the id table first */

  if (pdrv->id_table)

​    return platform_match_id(pdrv->id_table, pdev) != NULL;

 

  /* fall-back to driver name match */

  return (strcmp(pdev->name, drv->name) == 0);

}

(2)platform_match函数就是平台总线的匹配方法。该函数的工作方法是：如果有id_table就说明驱动可能支持多个设备，所以这时候要去对比id_table中所有的name，只要找到一个相同的就匹配上了不再找了，如果找完id_table都还没找到就说明每匹配上；如果没有id_table或者每匹配上，那就直接对比device和driver的name，如果匹配上就匹配上了，如果还没匹配上那就匹配失败。

 

 

## 5.5.6.platform平台总线工作原理3

## 5.5.6.1、以leds-s3c24xx.c为例来分析platform设备和驱动的注册过程

(1)platform_driver_register

(2)platform_device_register

module_init(s3c24xx_led_init); //内核启动的时候这两个函数会被调用

module_exit(s3c24xx_led_exit);

 

static int __init s3c24xx_led_init(void)

{

  return platform_driver_register(&s3c24xx_led_driver);

}

 

static void __exit s3c24xx_led_exit(void)

{

  platform_driver_unregister(&s3c24xx_led_driver);

}

static struct platform_driver s3c24xx_led_driver = {

  .probe   = s3c24xx_led_probe,

  .remove   = s3c24xx_led_remove,

  .driver   = {

​    .name    = "s3c24xx_led", //这个name一定要和device相匹配

​    .owner   = THIS_MODULE,

  },

};

 

 

 

## 5.5.6.2、platdata怎么玩

struct s3c24xx_led_platdata {

  unsigned int     gpio;

  unsigned int     flags;

 

  char      *name;

  char      *def_trigger;

};

 

//\arch\arm\mach-s3c2440\mach-mini2440.c

static struct s3c24xx_led_platdata mini2440_led1_pdata = {

  .name    = "led1",

  .gpio    = S3C2410_GPB(5),

  .flags   = S3C24XX_LEDF_ACTLOW | S3C24XX_LEDF_TRISTATE,

  .def_trigger  = "heartbeat",

};

//\arch\arm\mach-s3c2440\mach-mini2440.c

static struct platform_device *mini2440_devices[] __initdata = {

  &s3c_device_ohci,

  &s3c_device_wdt,

  &s3c_device_i2c0,

  &s3c_device_rtc,

  &s3c_device_usbgadget,

  &mini2440_device_eth,

  &mini2440_led1,

  &mini2440_led2,

  &mini2440_led3,

  &mini2440_led4,

  &mini2440_button_device,

  &s3c_device_nand,

  &s3c_device_sdi,

  &s3c_device_iis,

  &mini2440_audio,

};

 

 

(1)platdata其实就是设备注册时提供的设备有关的一些数据（譬如设备对应的gpio、使用到的中断号、设备名称····）

(2)这些数据在设备和驱动match之后，会由设备方转给驱动方。驱动拿到这些数据后，通过这些数据得知设备的具体信息，然后来操作设备。

(3)这样做的好处是：驱动源码中不携带数据，只负责算法（对硬件的操作方法）。现代驱动设计理念就是算法和数据分离，这样最大程度保持驱动的独立性和适应性。

## 5.5.6.3、match函数的调用轨迹

## 5.5.6.4、probe函数的功能和意义

 

 

## 5.5.7.平台总线实践环节1

### 5.5.7.1、回顾

### 5.5.7.2、先初步改造添加platform_driver

(1)第1步：先修改原来的代码到只有led1

(2)第2步：

\#include <linux/module.h>    // module_init module_exit

\#include <linux/init.h>     // __init  __exit

\#include <linux/fs.h>

\#include <linux/leds.h>

\#include <mach/regs-gpio.h>

\#include <mach/gpio-bank.h>

\#include <linux/io.h>

\#include <linux/ioport.h>

\#include <mach/gpio.h>

\#include <linux/platform_device.h>

 

\#define GPIO_LED1  S5PV210_GPJ0(3)

\#define GPIO_LED2  S5PV210_GPJ0(4)

\#define GPIO_LED3  S5PV210_GPJ0(5)

 

\#define X210_LED_OFF  1      // X210中LED是正极接电源，负极节GPIO

\#define X210_LED_ON   0      // 所以1是灭，0是亮

 

static struct led_classdev mydev1;     // 定义结构体变量

 

// 这个函数就是要去完成具体的硬件读写任务的

static void s5pv210_led1_set(struct led_classdev *led_cdev,

​        enum led_brightness value)

{

  printk(KERN_INFO "s5pv210_led1_set\n");

  

  //writel(0x11111111, GPJ0CON);

  

  // 在这里根据用户设置的值来操作硬件

  // 用户设置的值就是value

  if (value == LED_OFF)

  {

​    // 用户给了个0，希望LED灭

​    //writel(0x11111111, GPJ0CON);

​    // 读改写三部曲

​    //writel((readl(GPJ0DAT) | (1<<3)), GPJ0DAT);

​    gpio_set_value(GPIO_LED1, X210_LED_OFF);

  }

  else

  {

​    // 用户给的是非0，希望LED亮

​    //writel(0x11111111, GPJ0CON);

​    //writel((readl(GPJ0DAT) & ~(1<<3)), GPJ0DAT);

​    gpio_set_value(GPIO_LED1, X210_LED_ON);

  }

}

 

static int s5pv210_led_probe(struct platform_device *dev)

{

  // 用户insmod安装驱动模块时会调用该函数

  // 该函数的主要任务就是去使用led驱动框架提供的设备注册函数来注册一个设备

  int ret = -1;

  

  // 在这里去申请驱动用到的各种资源，当前驱动中就是GPIO资源

  if (gpio_request(GPIO_LED1, "led1_gpj0.3")) 

  {

​    printk(KERN_ERR "gpio_request failed\n");

  } 

  else 

  {

​    // 设置为输出模式，并且默认输出1让LED灯灭

​    gpio_direction_output(GPIO_LED1, 1);

  }

  

  

  

  // led1

  mydev1.name = "led1";

  mydev1.brightness = 0; 

  mydev1.brightness_set = s5pv210_led1_set;

  

  ret = led_classdev_register(NULL, &mydev1);

  if (ret < 0) {

​    printk(KERN_ERR "led_classdev_register failed\n");

​    return ret;

  }

​    

  return 0;

}

 

static int s5pv210_led_remove(struct platform_device *dev)

{

  led_classdev_unregister(&mydev1);

  gpio_free(GPIO_LED1);

 

  return 0;

}

 

static struct platform_driver s5pv210_led_driver = {

  .probe   = s5pv210_led_probe,

  .remove   = s5pv210_led_remove,

  .driver   = {

​    .name    = "s5pv210_led",

​    .owner   = THIS_MODULE,

  },

};

 

static int __init s5pv210_led_init(void)

{

  return platform_driver_register(&s5pv210_led_driver);

}

 

static void __exit s5pv210_led_exit(void)

{

  platform_driver_unregister(&s5pv210_led_driver);

}

 

module_init(s5pv210_led_init);

module_exit(s5pv210_led_exit);

 

// MODULE_xxx这种宏作用是用来添加模块描述信息

MODULE_LICENSE("GPL");             // 描述模块的许可证

MODULE_AUTHOR("aston <1264671872@qq.com>");   // 描述模块的作者

MODULE_DESCRIPTION("s5pv210 led driver");    // 描述模块的介绍信息

MODULE_ALIAS("s5pv210_led");          // 描述模块的别名信息

 

 

### 5.5.7.3、实验现象分析

 

·

## 5.5.8.平台总线实践环节2

### 5.5.8.1、检查mach-x210.c中是否有led相关的platform_device

没有，去相似的板子里找

### 5.5.8.2、参考mach-mini2440.c中添加led的platform_device定义

 

//LED的platform devices

 

static struct s5pv210_led_platdata x210_led1_pdata = {

  .name    = "led1",

  .gpio    = S5PV210_GPJ0(3),

  .flags   = S5PV210_LEDF_ACTLOW | S5PV210_LEDF_TRISTATE,

  .def_trigger  = "heartbeat",

};

 

static struct platform_device x210_led1 = {

  .name    = "s5pv210_led",

  .id   = 1,

  .dev    = {

​    .platform_data = &x210_led1_pdata,

  },

};

### 5.5.8.3、添加.platform_data 结构体的头文件

​    参照\arch\arm\mach-s3c2440\include\mach/led-gpio.h，将其内容复制到\arch\arm\mach-s5pv210\include\mach/led-gpio.h改成我们需要的内容：

 

\#ifndef __ASM_ARCH_LEDSGPIO_H

\#define __ASM_ARCH_LEDSGPIO_H "leds-gpio.h"

 

\#define S5PV210_LEDF_ACTLOW (1<<0)   /* LED is on when GPIO low */

\#define S5PV210_LEDF_TRISTATE  (1<<1)   /* tristate to turn off */

 

struct s5pv210_led_platdata {

  unsigned int     gpio;

  unsigned int     flags;

 

  char      *name;

  char      *def_trigger;

};

\#endif /* __ASM_ARCH_LEDSGPIO_H */

### 5.5.8.4、测试只有platform_device没有platform_driver时是怎样的

 

 

## 5.5.9.平台总线实践环节3

### 5.5.9.1、测试platform_device和platform_driver相遇时会怎样

### 5.5.9.2、代码实践函数

(1)probe函数应该做什么

(2)probe函数的数据从哪里来

(3)编程实践

\#include <linux/module.h>    // module_init module_exit

\#include <linux/init.h>     // __init  __exit

\#include <linux/fs.h>

\#include <linux/leds.h>

\#include <mach/regs-gpio.h>

\#include <mach/gpio-bank.h>

\#include <linux/io.h>

\#include <linux/ioport.h>

\#include <mach/gpio.h>

\#include <linux/platform_device.h>

\#include <mach/leds-gpio.h>

\#include <linux/slab.h>

 

\#define GPIO_LED1  S5PV210_GPJ0(3)

\#define GPIO_LED2  S5PV210_GPJ0(4)

\#define GPIO_LED3  S5PV210_GPJ0(5)

 

\#define X210_LED_OFF  1      // X210中LED是正极接电源，负极节GPIO

\#define X210_LED_ON   0      // 所以1是灭，0是亮

 

struct s5pv210_gpio_led {

  struct led_classdev   cdev;

  struct s5pv210_led_platdata *pdata;

};

//从platform_dev结构体反推到我们自己写的结构体 s5pv210_gpio_led。

static inline struct s5pv210_gpio_led *pdev_to_gpio(struct platform_device *dev)

{

  return platform_get_drvdata(dev);

}

//从led_classdev 结构体反推到我们自己写的结构体 s5pv210_gpio_led。

static inline struct s5pv210_gpio_led *to_gpio(struct led_classdev *led_cdev)

{

  return container_of(led_cdev, struct s5pv210_gpio_led, cdev);

}

 

// 这个函数就是要去完成具体的硬件读写任务的

static void s5pv210_led_set(struct led_classdev *led_cdev,

​        enum led_brightness value)

{

​    struct s5pv210_gpio_led *p = to_gpio(led_cdev);

​    

​    printk(KERN_INFO "s5pv210_led1_set\n");

  // 在这里根据用户设置的值来操作硬件

  // 用户设置的值就是value

  if (value == LED_OFF)

  {

​    // 用户给了个0，希望LED灭

​    gpio_set_value(p->pdata->gpio, X210_LED_OFF);

  }

  else

  {

​    // 用户给的是非0，希望LED亮

​    gpio_set_value(p->pdata->gpio, X210_LED_ON);

  }

}

 

static int s5pv210_led_probe(struct platform_device *dev)

{

  // 用户insmod安装驱动模块时会调用该函数

  // 该函数的主要任务就是去使用led驱动框架提供的设备注册函数来注册一个设备

  int ret = -1;

  struct s5pv210_led_platdata *pdata = dev->dev.platform_data;

  struct s5pv210_gpio_led *led;

  printk(KERN_INFO"s5pv210_led_probe \n");

  // 在这里去申请驱动用到的各种资源，当前驱动中就是GPIO资源

  led = kzalloc(sizeof(struct s5pv210_gpio_led), GFP_KERNEL);

  if (led == NULL) {

​    dev_err(&dev->dev, "No memory for device\n");

​    return -ENOMEM;

  }

 

  **platform_set_drvdata**(dev, led);//将led这个函数指针添加到platform——device中放着

 

  if (gpio_request(pdata->gpio, pdata->name)) 

  {

​    printk(KERN_ERR "gpio_request failed\n");

  } 

  else 

  {

​    // 设置为输出模式，并且默认输出1让LED灯灭

​    gpio_direction_output(pdata->gpio, 1);

  }

  

  // led1

  led->cdev.name = pdata->name;

  led->cdev.brightness = 0;  

  led->cdev.brightness_set = s5pv210_led_set;

  led->pdata =pdata;

  

  ret = led_classdev_register(&dev->dev, &led->cdev);

  if (ret < 0) {

​    printk(KERN_ERR "led_classdev_register failed\n");

​    return ret;

  }

​    

  return 0;

}

 

static int s5pv210_led_remove(struct platform_device *dev)

{

  struct s5pv210_gpio_led *p = pdev_to_gpio(dev);

  led_classdev_unregister(&p->cdev);

  gpio_free(p->pdata->gpio);

  kfree(p);          //放在最后一步

  return 0;

}

 

static struct platform_driver s5pv210_led_driver = {

  .probe   = s5pv210_led_probe,

  .remove   = s5pv210_led_remove,

  .driver   = {

​    .name    = "s5pv210_led",

​    .owner   = THIS_MODULE,

  },

};

 

static int __init s5pv210_led_init(void)

{

  return platform_driver_register(&s5pv210_led_driver);

}

 

static void __exit s5pv210_led_exit(void)

{

  platform_driver_unregister(&s5pv210_led_driver);

}

 

module_init(s5pv210_led_init);

module_exit(s5pv210_led_exit);

 

// MODULE_xxx这种宏作用是用来添加模块描述信息

MODULE_LICENSE("GPL");             // 描述模块的许可证

MODULE_AUTHOR("aston <1264671872@qq.com>");   // 描述模块的作者

MODULE_DESCRIPTION("s5pv210 led driver");    // 描述模块的介绍信息

MODULE_ALIAS("s5pv210_led");          // 描述模块的别名信息

 

 

\#define platform_get_drvdata(_dev) dev_get_drvdata(&(_dev)->dev)

### 5.5.9.3、platform_get_drvdata函数分析

\#define platform_get_drvdata(_dev) dev_get_drvdata(&(_dev)->dev)

 

void *dev_get_drvdata(const struct device *dev)

{

  if (dev && dev->p)

​    return dev->p->driver_data;

  return NULL;

}

//dev->p->driver_data 这里存放着 s5pv210_gpio_led **platform_set_drvdata**

(dev, led); 这个函数放进去的

\#define **platform_set_drvdata**(_dev,data) **dev_set_drvdata**(&(_dev)->dev, (data))

void **dev_set_drvdata**(struct device *dev, void *data)

{

  int error;

 

  if (!dev)

​    return;

  if (!dev->p) {

​    error = device_private_init(dev);

​    if (error)

​      return;

  }

  dev->p->driver_data = data;

}



 

# 第六章 misc类设备与蜂鸣器驱动

## 5.6.0.章节概要

5.6.1.板载蜂鸣器驱动测试

​    本节使用内核中提供的蜂鸣器驱动来实践测试蜂鸣器，要同时解决驱动层的问题和应用程序的编写。

5.6.2.misc类设备介绍

​    本节系统介绍misc类设备的概念和分类特点，并且对misc类设备的驱动框架详细讲解。

5.6.3.misc驱动框架源码分析1

​    本节分析misc类设备驱动框架中内核自己实现的部分，即misc.c。

5.6.4.misc驱动框架源码分析2

​    本节继续分析misc.c，重点是信号量及其使用的一般技巧。

5.6.5.蜂鸣器驱动源码分析1

​    本节分析九鼎提供的buzzer驱动源码

5.6.6.蜂鸣器驱动源码分析2

​    本节分析九鼎提供的buzzer驱动源码

 

## 5.6.1.板载蜂鸣器驱动测试

### 5.6.1.1、驱动部分

(1)九鼎移植内核已经提供了蜂鸣器驱动源码

(2)make menuconfig

​    Divce drivers

​       Character divce

​           x210 buzzer driver

(3)bug排查。修改Makefile中的宏名，最终可以在系统中看到 /dev/buzzer

### 5.6.1.2、应用部分

(1)应用编写：打开文件+ioctl

(2)测试实践

\#include <stdio.h>

\#include <sys/stat.h>

\#include <fcntl.h>

 

\#define DEVNAME "/dev/buzzer"

\#define PWM_IOCTL_SET_FREQ   1

\#define PWM_IOCTL_STOP     0

 

int main(void)

{

  int fd = -1;

  fd = open(DEVNAME, O_RDWR);

  if(fd < 0){

​    perror("open");

​    return -1;

  }

  ioctl(fd, PWM_IOCTL_SET_FREQ, 10000);

  sleep(3);

   ioctl(fd, PWM_IOCTL_STOP);

  sleep(3);

ioctl(fd, PWM_IOCTL_SET_FREQ, 4000);

 sleep(3);

  ioctl(fd, PWM_IOCTL_STOP);

  close(fd);

  return 0;

}

 

 

## 5.6.2.misc类设备介绍

### 5.6.2.1、何为misc(*misc*ellaneous)

(1)中文名：杂项设备\杂散设备

(2)/sys/class/misc

(3)典型的字符设备

(4)有一套驱动框架，内核实现一部分（misc.c），驱动实现一部分（x210-buzzer.c）。

(5)misc是对原始的字符设备注册接口的一个类层次的封装，很多典型字符设备都可以归类到misc类中，使用misc驱动框架来管理。

### 5.6.2.2、misc类设备驱动架构

(1)内核开发者实现部分，关键点有2个：一个是类的创建，另一个是开放给驱动开发者的接口

(2)具体设备驱动工程师实现部分

### 5.6.2.3、本部分学习方法

(1)蜂鸣器驱动源码已有，分析为主

(2)复习并验证前面讲的驱动框架的思维

(3)有余力的不妨开始注意一些细节

 

 

## 5.6.3.misc驱动框架源码分析1

### 5.6.3.1、misc源码框架基础

(1)misc源码框架本身也是一个模块，内核启动时自动加载.

(2)源码框架的主要工作：注册misc类，使用老接口注册字符设备驱动（主设备号10），开放device注册的接口misc_register给驱动工程师.

//kernel_jiuding\drivers\char\misc.c

\#ifdef CONFIG_PROC_FS

  proc_create("misc", 0, NULL, &misc_proc_fops);

\#endif

  misc_class = class_create(THIS_MODULE, "misc");

  err = PTR_ERR(misc_class);

  if (IS_ERR(misc_class))

​    goto fail_remove;

 

  err = -EIO;

  if (register_chrdev(MISC_MAJOR,"misc",&misc_fops))

​    goto fail_printk;

  misc_class->devnode = misc_devnode;

  return 0;

 

fail_printk:

  printk("unable to get major %d for misc devices\n", MISC_MAJOR);

  class_destroy(misc_class);

fail_remove:

  remove_proc_entry("misc", NULL);

  return err;

}

subsys_initcall(misc_init); //module_init一样，只不过层级不一样

### 5.6.3.2、misc类设备的注册

int misc_register(struct miscdevice * misc)

{

  struct miscdevice *c;

  dev_t dev;

  int err = 0;

 

  INIT_LIST_HEAD(&misc->list);

 

  mutex_lock(&misc_mtx);

list_for_each_entry(c, &misc_list, list) {  //这个宏定义，不知为什么能在后面加{}的语句？？？？为什么能这么用？ 这个定义了个for语句

​    if (c->minor == misc->minor) {

​      mutex_unlock(&misc_mtx);

​      return -EBUSY;

​    }

  }



  //自动分配设备号

  if (misc->minor == MISC_DYNAMIC_MINOR) { //位图数据结构

​    int i = find_first_zero_bit(misc_minors, DYNAMIC_MINORS);

​    if (i >= DYNAMIC_MINORS) {



​      mutex_unlock(&misc_mtx);

​      return -EBUSY;

​    }

​    misc->minor = DYNAMIC_MINORS - i - 1;

​    set_bit(i, misc_minors);

  }

 

  dev = MKDEV(MISC_MAJOR, misc->minor);

 

  misc->this_device = device_create(misc_class, misc->parent, dev,

​           misc, "%s", misc->name);

  if (IS_ERR(misc->this_device)) {

​    int i = DYNAMIC_MINORS - misc->minor - 1;

​    if (i < DYNAMIC_MINORS && i >= 0)

​      clear_bit(i, misc_minors);

​    err = PTR_ERR(misc->this_device);

​    goto out;

  }

 

  /*

   \* Add it to the front, so that later devices can "override"

   \* earlier defaults

   */

  list_add(&misc->list, &misc_list);

 out:

  mutex_unlock(&misc_mtx);

  return err;

}

struct miscdevice {

  int minor;

  const char *name;

  const struct file_operations *fops;

  struct list_head list;

  struct device *parent;

  struct device *this_device;

  const char *nodename;

  mode_t mode;

};

 

(1)驱动工程师需要借助misc来加载自己的驱动时，只需要调用misc_register接口注册自己的设备即可，其余均不用管。

(2)misc_list链表的作用。内核定义了一个misc_list链表用来记录所有内核中注册了的杂散类设备。当我们向内核注册一个misc类设备时，内核就会向misc_list链表中insert一个节点。

\#define LIST_HEAD_INIT(name) { &(name), &(name) }

\#define LIST_HEAD(name) \

​    struct list_head name = LIST_HEAD_INIT(name)

 

原式子：static LIST_HEAD(misc_list);

展开后：static struct list_head misc_list = { &(misc_list), &(misc_list) }

(3)主设备号和次设备号的作用和区分

​       一个类是主设备号，相似的设备都会被放到这个类中以此设备号编号。

 

## 5.6.4.misc驱动框架源码分析2

### 5.6.4.1、open函数分析

static int misc_open(struct inode * inode, struct file * file)

{ //inode 硬盘里的路径， file设备文件名的路径。

  int minor = iminor(inode);

  struct miscdevice *c;

  int err = -ENODEV;

  const struct file_operations *old_fops, *new_fops = NULL;

   //为什么要有两个 old 和 new fops，因为调用open真正函数失败时做还原用。

  mutex_lock(&misc_mtx);

  

  list_for_each_entry(c, &misc_list, list) {

​    if (c->minor == minor) {

​      new_fops = fops_get(c->fops);    

​      break;

​    }

  }

​    

  if (!new_fops) {

​    mutex_unlock(&misc_mtx);

​    request_module("char-major-%d-%d", MISC_MAJOR, minor);

​    mutex_lock(&misc_mtx);

 

​    list_for_each_entry(c, &misc_list, list) {

​      if (c->minor == minor) {

​        new_fops = fops_get(c->fops);

​        break;

​      }

​    }

​    if (!new_fops)

​      goto fail;

  }

 

  err = 0;

  old_fops = file->f_op;

  file->f_op = new_fops;

  if (file->f_op->open) {

​    file->private_data = c;

​    err=file->f_op->open(inode,file); //正真的open函数

​    if (err) {

​      fops_put(file->f_op);

​      file->f_op = fops_get(old_fops);

​    }

  }

  fops_put(old_fops);

fail:

  mutex_unlock(&misc_mtx);

  return err;

}

 

### 5.6.4.2、misc在proc下的展现

 

 

### 5.6.4.3、内核互斥锁

(1)何为互斥锁， 我访问了这块代码，别人就不能访问。

(2)定义：DEFINE_MUTEX

static DEFINE_MUTEX(misc_mtx);

(3)上锁mutex_lock()和解锁mutex_unlock()

(4)内核防止竞争状态的手段：原子访问(不可被打断)、自旋锁(多核cpu年代发明的)、互斥锁、信号量

(5)原子访问主要用来做计数、自旋锁后面讲中断会详细讲、互斥锁和信号量很相似（其实就是计数值为1的信号量），互斥锁的出现比信号量晚，实现上比信号量优秀，尽量使用互斥锁。

 

 

## 5.6.5.蜂鸣器驱动源码分析1

### 5.6.5.1、dev_init

(1)信号量

(2)miscdevice

(3)gpio_request

(4)printk

 

static struct file_operations dev_fops = {

  .owner  =  THIS_MODULE,

  .open  =  x210_pwm_open,  //应用成打开的文件

  .release =  x210_pwm_close, //关闭

  .ioctl  =  x210_pwm_ioctl, //执行ioctl

};

 

static struct miscdevice misc = {

  .minor = MISC_DYNAMIC_MINOR,  //自动分配设备号

  .name = DEVICE_NAME,      //这个设备的名字

  .fops = &dev_fops,       //操作函数

};

 

static int __init dev_init(void)

{

  int ret;

 

  init_MUTEX(&lock);

  ret = misc_register(&misc);

  

  /* GPD0_2 (PWMTOUT2) */ 硬件初始化

  ret = gpio_request(S5PV210_GPD0(2), "GPD0");

  if(ret)

​    printk("buzzer-x210: request gpio GPD0(2) fail");

​       

  s3c_gpio_setpull(S5PV210_GPD0(2), S3C_GPIO_PULL_UP);

  s3c_gpio_cfgpin(S5PV210_GPD0(2), S3C_GPIO_SFN(1));

  gpio_set_value(S5PV210_GPD0(2), 0);

 

  printk ("x210 "DEVICE_NAME" initialized\n");

​    return ret;

}

 

static int x210_pwm_open(struct inode *inode, struct file *file)

{

  if (!down_trylock(&lock)) //不让重复打开

​    return 0;

  else

​    return -EBUSY;

  

}

 

 

### 5.6.5.2、ioctl

(1)为什么需要ioctl（input output control，输入输出控制）。

(2)ioctl怎么用

 

 

## 5.6.6.蜂鸣器驱动源码分析2

硬件操作有关的代码

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 



 

# 第七章 framebuffer驱动详解

## 5.7.0章节概要

5.7.1.framebuffer介绍

​    本节介绍framebuffer的概念和来源、设计思想，fb设备的应用层接口等，为下节使用framebuffer应用编程打基础。

5.7.2.framebuffer应用编程实践1

​    本节基于framebuffer编写应用程序进行显示，主要是打开设备文件及获取设备的关键硬件信息、进行显存映射等。

5.7.3.framebuffer应用编程实践2

​    本节继续framebuffer应用编程，使用framebuffer在LCD上显示背景色.

5.7.4.framebuffer应用编程实践3

​    本节继续framebuffer应用编程，使用framebuffer更改分辨率等操作，最后讲述如何去写字、画图等。

5.7.5.framebuffer驱动框架总览

​    本节对内核的framebuffer驱动框架做整体介绍，各部分涉及哪些文件，每个文件是谁写的，具体功能是什么，详细的代码分析在后面。

## 5.7.1.framebuffer介绍

###    5.7.1.1、什么是framebuffer

(1)裸机中如何操作LCD

​    在DDR内存里面分出来一块内存作为显存，显存和我们的LCD屏幕之间做一个双向映射，cpu只需要把内容丢到显存里面去，硬件就会自动的吧显存里的数据自动的刷到LCD的驱动器里面驱动显示。

(2)OS下操作LCD的难点

​    裸机是一个程序全干了，有操作系统分为驱动和应用。驱动完成初始化工作，和LCD建立关系，应用负责把需要显示的内容丢到显存中。

(3)framebuffer帧缓冲（简称fb）是linux内核中虚拟出的一个设备

(4)framebuffer向应用层提供一个统一标准接口的显示设备

(5)从驱动来看，fb是一个典型的字符设备，而且创建了一个类/sys/class/graphics

### 5.7.1.2、framebuffer的使用

(1)设备文件 /dev/fb0

(2)获取设备信息 #include <linux/fb.h>

(3)mmap做映射

(4)填充framebuffer

 

 

## 5.7.2.framebuffer应用编程实践1

### 5.7.2.1、打开设备

\#include <stdio.h>

\#include <sys/types.h>

\#include <sys/stat.h>

\#include <fcntl.h>

\#include <unistd.h>

\#include <linux/fb.h>

\#include <sys/ioctl.h>

 

\#define DEV_NAME  "/dev/fb0"

 

int main(void)

{

  int fd = -1, fd1 = -1, ret = -1;

  struct fb_fix_screeninfo finfo = {0};

  struct fb_var_screeninfo vinfo = {0};

 

  fd = open(DEV_NAME, O_RDWR);

  if(fd < 0){

​    perror("open");

  }

  printf("open %s success!\n", DEV_NAME);

  

  ret = ioctl(fd, FBIOGET_FSCREENINFO, &finfo);

  if(ret < 0){

​    perror("ioctl");

​    close(fd);

​    return -1;

  }

 

  ret = ioctl(fd, FBIOGET_VSCREENINFO, &vinfo);

  if(ret < 0){

​    perror("ioctl");

​    close(fd);

​    return -1;

  }

 

  printf("smem_start = %0x,smem_len =%u\n",

​      finfo.smem_start,finfo.smem_len);

  printf("xres = %u,yres =%u,bits_per_pixel= %u\n",

​      vinfo.xres ,vinfo.yres, vinfo.bits_per_pixel);

  printf("xres_virtual = %u,yres_virtual =%u\n",

​      vinfo.xres_virtual,vinfo.yres_virtual);

  close(fd);

  return 0;

}

**输出：**

root@Jay210 5.7]# ./appfb 

open /dev/fb0 success!

smem_start = 475ab000,smem_len =3072000

xres = 800,yres =480,bits_per_pixel= 32

xres_virtual = 800,yres_virtual =960

[root@Jay210 5.7]# 

 

### 5.7.2.2、获取设备信息

(1)不可变信息FSCREENINFO，使用ioctl的FBIOGET_FSCREENINFO名

(2)可变信息VSCREENINFO，使用ioctl的FBIOGET_VSCREENINFO名

###    5.7.2.3、双缓冲

 

 

 

 

 

​    

​    虚拟屏幕是真实的屏幕的两倍，当切换到(0,480)也就是虚拟屏幕下的部分，屏幕显示也切换了，而虚拟屏幕的上部分用来缓冲下一数据，这样子的缓冲机制就叫双缓冲结构或者乒乓结构。

## 5.7.3.framebuffer应用编程实践2

### 5.7.3.1、mmap做映射

  void *mmap(void *addr, size_t length, int prot, int flags,

​         int fd, off_t offset);

 

做完了mmap后fb在当前进程中就已经就绪了，随时可以去读写LCD显示器了。

### 5.7.3.2、fb显示之刷背景

\#include <stdio.h>

\#include <sys/types.h>

\#include <sys/stat.h>

\#include <fcntl.h>

\#include <unistd.h>

\#include <linux/fb.h>

\#include <sys/ioctl.h>

\#include <sys/mman.h>

 

\#define DEV_NAME  "/dev/fb0"

\#define WIDTH   1024 

\#define HEIGHT   600

 

unsigned int *pfb = NULL;

 

void draw_back(unsigned int width,unsigned int lenth,

​         unsigned int color);

 

int main(void)

{

  int fd = -1, fd1 = -1, ret = -1;

  struct fb_fix_screeninfo finfo = {0};

  struct fb_var_screeninfo vinfo = {0};

 

  fd = open(DEV_NAME, O_RDWR);

  if(fd < 0){

​    perror("open");

  }

  printf("open %s success!\n", DEV_NAME);

  

  ret = ioctl(fd, FBIOGET_FSCREENINFO, &finfo);

  if(ret < 0){

​    perror("ioctl");

​    close(fd);

​    return -1;

  }

 

  ret = ioctl(fd, FBIOGET_VSCREENINFO, &vinfo);

  if(ret < 0){

​    perror("ioctl");

​    close(fd);

​    return -1;

  }

 

  printf("smem_start = %0x,smem_len =%u\n",

​      finfo.smem_start,finfo.smem_len);

  printf("xres = %u,yres =%u,bits_per_pixel= %u\n",

​      vinfo.xres ,vinfo.yres, vinfo.bits_per_pixel);

  printf("xres_virtual = %u,yres_virtual =%u\n",

​      vinfo.xres_virtual,vinfo.yres_virtual);

  /*mmap 地址映射

  void *mmap(void *addr, size_t length, int prot, int flags,

​         int fd, off_t offset);

  */

  pfb = mmap(NULL, finfo.smem_len,PROT_READ | PROT_WRITE, MAP_SHARED,

​        fd, 0) ;

​    if(NULL == pfb){

​      perror("mmap");

​      close(fd);

​      return -1;

​    }

  printf("mmap:0x%p success !\n",pfb);

 

  //显示背景

  draw_back(WIDTH, HEIGHT, 0xffffffff);

 

  close(fd);

  return 0;

}

 

void draw_back(unsigned int width,unsigned int height,

​         unsigned int color)

{

  unsigned int x, y;

  for(y = 0; y < height; y++){

​    for(x = 0 ; x < width; x++){

​      *(pfb + y*WIDTH + x) = color;

​    }

  }

}

 

 

## 5.7.4.framebuffer应用编程实践3

### 5.7.4.1、设置分辨率

  //modify screen resolution

  

  vinfo.xres = 1024;

  vinfo.yres = 600;

  vinfo.xres_virtual =1024;

  vinfo.yres_virtual =1200;

 

  ret = ioctl(fd, FBIOPUT_VSCREENINFO, &vinfo);

  if(ret < 0){

​    perror("ioctl");

​    close(fd);

​    return -1;

  }

  printf("---------------修改过之后-------------------\n");

  printf("xres = %u,yres =%u,bits_per_pixel= %u\n",

​      vinfo.xres ,vinfo.yres, vinfo.bits_per_pixel);

  printf("xres_virtual = %u,yres_virtual =%u\n",

​      vinfo.xres_virtual,vinfo.yres_virtual);

 

(1)实验失败，实验结果是只能修改虚拟分辨率，不能修改可视分辨率。原因要去驱动里找。

(2)正确的做法是在驱动中去修改参数，然后重新编译运行，才能解决。

### 5.7.4.2、写字、画线、图片显示等

\#include <stdio.h>

\#include <sys/types.h>

\#include <sys/stat.h>

\#include <fcntl.h>

\#include <unistd.h>

\#include <linux/fb.h>

\#include <sys/ioctl.h>

\#include <sys/mman.h>

 

\#define DEV_NAME  "/dev/fb0"

\#define WIDTH   1024 

\#define HEIGHT   600

 

\#define WHITE   0xffffffff

\#define GREEN   0xff0000ff

 

unsigned int *pfb = NULL;

struct line_arg{

  unsigned int width;

  unsigned int height;

  unsigned int color;

  unsigned int x1;

  unsigned int y1;

  unsigned int x2;

  unsigned int y2;

 

};

 

void draw_back(unsigned int width,unsigned int lenth,

​         unsigned int color);

void draw_line(struct line_arg line); 

int main(void)

{

  int fd = -1, fd1 = -1, ret = -1;

  struct fb_fix_screeninfo finfo = {0};

  struct fb_var_screeninfo vinfo = {0};

  struct line_arg line = {0};

  fd = open(DEV_NAME, O_RDWR);

  if(fd < 0){

​    perror("open");

  }

  printf("open %s success!\n", DEV_NAME);

  

  ret = ioctl(fd, FBIOGET_FSCREENINFO, &finfo);

  if(ret < 0){

​    perror("ioctl");

​    close(fd);

​    return -1;

  }

 

  ret = ioctl(fd, FBIOGET_VSCREENINFO, &vinfo);

  if(ret < 0){

​    perror("ioctl");

​    close(fd);

​    return -1;

  }

 

  printf("smem_start = %0x,smem_len =%u\n",

​      finfo.smem_start,finfo.smem_len);

  printf("xres = %u,yres =%u,bits_per_pixel= %u\n",

​      vinfo.xres ,vinfo.yres, vinfo.bits_per_pixel);

  printf("xres_virtual = %u,yres_virtual =%u\n",

​      vinfo.xres_virtual,vinfo.yres_virtual);

 

 

  /*mmap 地址映射

  void *mmap(void *addr, size_t length, int prot, int flags,

​         int fd, off_t offset);

  */

 

  pfb = mmap(NULL, finfo.smem_len, PROT_READ | PROT_WRITE, MAP_SHARED,

​        fd, 0) ;

​    if(NULL == pfb){

​      perror("mmap");

​      close(fd);

​      return -1;

​    }

  printf("mmap:0x%p success !\n",pfb);

 

  //显示背景

  draw_back(WIDTH, HEIGHT, 0xffffffff);

 

  line.width = WIDTH;

  line.height = HEIGHT;

  line.color = GREEN;

  line.x1   =50;

  line.y1   =300;

  line.x2   =800;

  line.y2   =300;

  draw_line(line);

 

  close(fd);

  return 0;

}

 

void draw_back(unsigned int width,unsigned int height,

​         unsigned int color)

{

  unsigned int x, y;

  for(y = 0; y < height; y++){

​    for(x = 0 ; x < width; x++){

​      *(pfb + y*WIDTH + x) = color;

​    }

  }

}

 

void draw_line(struct line_arg line) 

{

  

  for(line.x1 = 50; line.x1 <line.x2 ;line.x1++){

​    *(pfb + line.y1*line.width +line.x1) =line.color;

  }

 

}

 

 

## 5.7.5.framebuffer驱动框架总览

### 5.7.5.1、驱动框架部分

(1)drivers/video/fbmem.c。主要任务：1、创建graphics类、注册FB的字符设备驱动、提供register_framebuffer接口给具体framebuffer驱动编写着来注册fb设备的。本文件相对于fb来说，地位和作用和misc.c文件相对于杂散类设备来说一样的，结构和分析方法也是类似的。

(2)drivers/video/fbsys.c。这个文件是处理fb在/sys目录下的一些属性文件的。

(3)drivers/video/modedb.c。这个文件是管理显示模式（譬如VGA、720P等就是显示模式）的

(4)drivers/video/fb_notify.c

### 5.7.5.2、驱动部分

(1)drivers/video/samsung/s3cfb.c，驱动主体

(2)drivers/video/samsung/s3cfb_fimd6x.c，里面有很多LCD硬件操作的函数

(2)arch/arm/mach-s5pv210/mach-x210.c，负责提供platform_device的

(3)arch/arm/plat-s5p/devs.c，为platform_device提供一些硬件描述信息的

### 5.7.5.3、如何分析

(1)经验

(2)分析menuconfig、Makefile、Kconfig等

(3)内核编译后检查编译结果中的.o文件

 

 

## 5.7.6.framebuffer驱动框架分析1

### 5.7.6.1、fbmem_init函数

//条件编译，如果定义了MODULE，也就是驱动选择模块化，用这套直接编译成模块.ko文件

//如果没有定义MODULE 就直接执行eles subsys_inicall

\#ifdef MODULE

module_init(fbmem_init);

static void __exit

fbmem_exit(void)

{

  remove_proc_entry("fb", NULL);

  class_destroy(fb_class);

  unregister_chrdev(FB_MAJOR, "fb");

}

 

module_exit(fbmem_exit);

MODULE_LICENSE("GPL");

MODULE_DESCRIPTION("Framebuffer base");

\#else

subsys_initcall(fbmem_init);

\#endif

 

(1)#ifdef MODULE

(2)fb_proc_fops和fb在proc文件系统中的表现

(3)register_chrdev注册fb设备

(4)class_create创建graphics类

(5)fbmem_exit的对应

### 5.7.6.2、fb_fops

static const struct file_operations fb_fops = {

  .owner =  THIS_MODULE,

  .read =   fb_read,

  .write =  fb_write,

  .unlocked_ioctl = fb_ioctl,

\#ifdef CONFIG_COMPAT

  .compat_ioctl = fb_compat_ioctl,

\#endif

  .mmap =   fb_mmap,

  .open =   fb_open,

  .release = fb_release,

\#ifdef HAVE_ARCH_FB_UNMAPPED_AREA

  .get_unmapped_area = get_fb_unmapped_area,

\#endif

\#ifdef CONFIG_FB_DEFERRED_IO

  .fsync =  fb_deferred_io_fsync,

\#endif

};

 

static ssize_t

fb_read(struct file *file, char __user *buf, size_t count, loff_t *ppos)

{

  unsigned long p = *ppos;

  struct inode *inode = file->f_path.dentry->d_inode; //在硬盘里面设备文件，

​                            // 主设备号，次设备号等等

  int fbidx = iminor(inode);

struct fb_info *info = registered_fb[fbidx]; 

truct fb_info *registered_fb[FB_MAX] __read_mostly; //fb设备的信息都在这个结构体里面 。这个指针数组就是用来管理所有的fb设备的。 ，这个数组的下标就是我们的次设备号。

int num_registered_fb __read_mostly; //已经注册的fb个数。

 

 

  u32 *buffer, *dst;

  u32 __iomem *src;

  int c, i, cnt = 0, err = 0;

  unsigned long total_size;

 

  if (!info || ! info->screen_base)

​    return -ENODEV;

 

  if (info->state != FBINFO_STATE_RUNNING)

​    return -EPERM;

 

  if (info->fbops->fb_read)

​    return info->fbops->fb_read(info, buf, count, ppos);

  

  total_size = info->screen_size;

 ……

 }

 

 

(1)read/write/mmap/ioctl

(2)registered_fb和num_registered_fb

(3)struct fb_info

 

 

## 5.7.7.framebuffer驱动框架分析2

### 5.7.7.1、register_framebuffer

int

register_framebuffer(struct fb_info *fb_info)//给写驱动部分代码注册驱用的

{          

  int i;

  struct fb_event event;

  struct fb_videomode mode;

struct **fb_videomode** {

  const char *name;  /* optional */

  u32 refresh;    /* optional */

  u32 xres;

  u32 yres;

  u32 pixclock;

  u32 left_margin;

  u32 right_margin;

  u32 upper_margin;

  u32 lower_margin;

  u32 hsync_len;

  u32 vsync_len;

  u32 sync;

  u32 vmode;

  u32 flag;

};

 

 

  if (num_registered_fb == FB_MAX) //最多支持的fb个数已经饱和。

​    return -ENXIO;

 

  if (fb_check_foreignness(fb_info))//判断大小端模式  

​    return -ENOSYS;

 

  remove_conflicting_framebuffers(fb_info->apertures, fb_info->fix.id,

​           fb_is_primary_device(fb_info));

 

  num_registered_fb++;

  for (i = 0 ; i < FB_MAX; i++)

​    if (!registered_fb[i])

​      break;

  fb_info->node = i;  //node其实就是fb_info在registered_fb数组中的下标

  mutex_init(&fb_info->lock);

  mutex_init(&fb_info->mm_lock);

 

  fb_info->dev = device_create(fb_class, fb_info->device,

​           MKDEV(FB_MAJOR, i), NULL, "fb%d", i);

  if (IS_ERR(fb_info->dev)) {

​    /* Not fatal */

​    printk(KERN_WARNING "Unable to create device for framebuffer %d; errno = %ld\n", i, PTR_ERR(fb_info->dev));

​    fb_info->dev = NULL;

  } else

​    fb_init_device(fb_info);

 

int fb_init_device(struct fb_info *fb_info) 

{

  int i, error = 0;

 

  dev_set_drvdata(fb_info->dev, fb_info);

 

*void* *dev_set_drvdata**(**struct* *device ***dev**,* *void* ****data**)* //将自己的地址指针存在 struct device结构体中p中的的driver_data 中；将来只要有device结构体就可以得到fb_info 结构体。

*{*

  *int* *error;*

 

  *if* *(!dev)*

​    *return**;*

  *if* *(!**dev**->**p**) {*

​    *error =* *device_private_init**(dev);*

​    *if* *(error)*

​      *return**;*

  *}*

  *dev**->**p**->**driver_data* *= data;* 

*}*// void dev_set_drvdata

 

  fb_info->class_flag |= FB_SYSFS_FLAG_ATTR;

 

  for (i = 0; i < ARRAY_SIZE(device_attrs); i++) {

​    error = device_create_file(fb_info->dev, &device_attrs[i]);//给设备文件创建属性

​    if (error)

​      break;

  }

  **return** **0****;**

**}**//int fb_init_device

 

   …

  …

  unlock_fb_info(fb_info);

  return 0;

}

 

(1)fb驱动框架开放给驱动编写着的注册接口

(2)fb_check_foreignness

(3)remove_conflicting_framebuffers

(4)device_create

(5)fb_init_device 

### 5.7.7.2、fb在sysfs中的接口

(1)device_attrs

(2)dev_set_drvdatas和dev_get_drvdata

 

 

## 5.7.8.framebuffer驱动框架分析3

### 5.7.8.1、fb的mode

int

register_framebuffer(struct fb_info *fb_info)//给我写驱动部分代码注册驱动的

{    

 

​    … …

  if (!fb_info->modelist.prev || !fb_info->modelist.next)

​    INIT_LIST_HEAD(&fb_info->modelist);

 

  fb_var_to_videomode(&mode, &fb_info->var); //显示模式，屏幕大小啊，分辨率之类的

  fb_add_videomode(&mode, &fb_info->modelist);

  registered_fb[i] = fb_info; //这句很重要，是吧fb_info这个结构体添加进系统记录的链表里面

 

(1)什么是mode

(2)fb_var_to_videomode

(3)fb_add_videomode

### 5.7.8.2、注册登记该fb设备

(1)registered_fb[i] = fb_info;

(2)结合fb_read等函数中对fb_info的使用

(3)关键点：数据如何封装、数据由谁准备由谁消费、数据如何传递

### 5.7.8.3、fb_notifier_call_chain

 

 

## 5.7.9.framebuffer驱动分析1

### 5.7.9.1、s3cfb.c

static int __init s3cfb_register(void)

{

  platform_driver_register(&s3cfb_driver);

 

  return 0;

}

static void __exit s3cfb_unregister(void)

{

  platform_driver_unregister(&s3cfb_driver);

}

 

 

module_init(s3cfb_register);

module_exit(s3cfb_unregister);

static struct platform_driver s3cfb_driver = {

  .probe = s3cfb_probe,

  .remove = __devexit_p(s3cfb_remove),

  .driver = {

​      .name = S3CFB_NAME,

​      .owner = THIS_MODULE,

  },

};

 

(1)注意目录结构的组织

(2)s3cfb_driver

(3)但有一个platform dirver是不能工作了,还需要有一个platform device,这个platform device 在arch\arm\mach-s5pv210\mach-x210.c中.

### 5.7.9.2、s3c_device_fb

struct platform_device s3c_device_fb = {

  .name     = "s3cfb",

  .id    = -1,

  .num_resources  = ARRAY_SIZE(s3cfb_resource),

  .resource   = s3cfb_resource,

  .dev     = {

​    .dma_mask    = &fb_dma_mask,

​    .coherent_dma_mask = 0xffffffffUL

  }

};

 

 

(1)mach-x210.c中，被使用

(2)devs.c中

(3)resource的定义和作用

 

 

## 5.7.10.framebuffer驱动分析1

### 5.7.10.1、probe函数分析

 

 

(1)struct s3c_platform_fb   这个结构体是fb的platform_data结构体，这个结构体变量就是platform设备的私有数据，这个数据在platform_device.device.platform_data中存储。在mach文件中去准备并填充这些数据，在probe函数中通过传参的platform_device指针取出来。

(2)struct s3cfb_global     这个结构体主要作用是在驱动部分的2个文件（s3cfb.c和s3cfb_fimd6x.c）的函数中做数据传递用的。

(3)struct resource 存放一些相关的资源的

​          struct resource {

  resource_size_t start;

  resource_size_t end;

  const char *name;

  unsigned long flags;

  struct resource *parent, *sibling, *child;

};

 

(4)regulator

 

static int __devinit s3cfb_probe(struct platform_device *pdev)

{

  struct s3c_platform_fb *pdata;//

  struct s3cfb_global *fbdev;

  struct resource *res;

  int i, j, ret = 0;

 

  fbdev = kzalloc(sizeof(struct s3cfb_global), GFP_KERNEL);

  if (!fbdev) {

​    dev_err(&pdev->dev, "failed to allocate for "

​      "global fb structure\n");

​    ret = -ENOMEM;

​    goto err_global;

  }

  fbdev->dev = &pdev->dev; //将platform_device *pdev 

  //和struct s3cfb_global *fbdev 挂接起来 ，在remov函数中可以从

  //platform_device *pdev 得到 struct s3cfb_global *fbdev .这个设计是

  //非常模式化，很多代码都是这么干的。

 

  fbdev->regulator = regulator_get(&pdev->dev, "pd");//整流器，电流电压的控制器

  //近些年来电子设备对于耗电很敏感，要求低功耗，需要对电源管理精细化。在不同模式下对

  //LCD进行不同的供电。

  if (!fbdev->regulator) {

​    dev_err(fbdev->dev, "failed to get regulator\n");

​    ret = -EINVAL;

​    goto err_regulator;

  }

  ret = regulator_enable(fbdev->regulator);

  if (ret < 0) {

​    dev_err(fbdev->dev, "failed to enable regulator\n");

​    ret = -EINVAL;

​    goto err_regulator;

  }

  pdata = to_fb_plat(&pdev->dev); 

  if (!pdata) {

​    dev_err(fbdev->dev, "failed to get platform data\n");

​    ret = -EINVAL;

​    goto err_pdata;

}

…

…

 

**//\arch\arm\plat-s5p\devs.c**

struct s3c_platform_fb *to_fb_plat(struct device *dev)

{

  struct platform_device *pdev = to_platform_device(dev);

 

  return (struct s3c_platform_fb *)pdev->dev.platform_data;

}

 

struct platform_device s3c_device_fb = {

  .name     = "s3cfb",

  .id    = -1,

  .num_resources  = ARRAY_SIZE(s3cfb_resource),

  .resource   = s3cfb_resource,

  .dev     = {

​    .dma_mask    = &fb_dma_mask,

​    .coherent_dma_mask = 0xffffffffUL

//这里没有设置.data，但是下面的函数中添加了.data

  }

};

 

 

void __init s3cfb_set_platdata(struct s3c_platform_fb *pd)

{

  struct s3c_platform_fb *npd;

  int i;

 

  if (!pd)

​    pd = &default_fb_data;

 

  npd = kmemdup(pd, sizeof(struct s3c_platform_fb), GFP_KERNEL);

  if (!npd)

​    printk(KERN_ERR "%s: no memory for platform data\n", __func__);

  else {

​    for (i = 0; i < npd->nr_wins; i++)

​      npd->nr_buffers[i] = 1;

 

​    npd->nr_buffers[npd->default_win] = CONFIG_FB_S3C_NR_BUFFERS;

 

​    s3cfb_get_clk_name(npd->clk_name);

​    npd->clk_on = s3cfb_clk_on;

​    npd->clk_off = s3cfb_clk_off;

 

​    /* starting physical address of memory region */

​    npd->pmem_start = s5p_get_media_memory_bank(S5P_MDEV_FIMD, 1);

​    /* size of memory region */

​    npd->pmem_size = s5p_get_media_memsize_bank(S5P_MDEV_FIMD, 1);

 

​    **s3c_device_fb****.****dev****.****platform_data** **= npd;**   //填充了.platform_data

  }

}

**//\arch\arm\mach-s5pv210\mach-x210.c** 

//这个文件中调用这个函数

static void __init smdkc110_machine_init(void)

{

 

\#ifdef CONFIG_FB_S3C_EK070TN93

  smdkv210_backlight_off();

  s3cfb_set_platdata(&ek070tn93_fb_data); //这里调用了该函数

\#endif

static struct s3c_platform_fb ek070tn93_fb_data __initdata = {

  .hw_ver = 0x62,

  .nr_wins = 5,

  .default_win = CONFIG_FB_S3C_DEFAULT_WINDOW,

  .swap = FB_SWAP_WORD | FB_SWAP_HWORD,

 

  .lcd = &ek070tn93,

  .cfg_gpio  = ek070tn93_cfg_gpio,

  .backlight_on  = ek070tn93_backlight_on,

  .backlight_onoff  = ek070tn93_backlight_off,

  .reset_lcd = ek070tn93_reset_lcd,

};

\#endif

 

### 5.7.10.2、platform_data的传递过程

(1)to_fb_plat

(2)s3cfb_set_platdata

(3)smdkc110_machine_init

 

 

## 5.7.11.framebuffer驱动分析2

 

static int __devinit s3cfb_probe(struct platform_device *pdev)

{

​    **…**

​    **…**

  fbdev->lcd = (struct s3cfb_lcd *)pdata->lcd;

 

  if (pdata->cfg_gpio)

​    pdata->cfg_gpio(pdev);

 

  if (pdata->clk_on)

​    pdata->clk_on(pdev, &fbdev->clock);

  //取出io资源

  res = platform_get_resource(pdev, IORESOURCE_MEM, 0);

  if (!res) {

​    dev_err(fbdev->dev, "failed to get io memory region\n");

​    ret = -EINVAL;

​    goto err_io;

  }

  //向内核申请资源

  res = request_mem_region(res->start,

​         res->end - res->start + 1, pdev->name);

  if (!res) {

​    dev_err(fbdev->dev, "failed to request io memory region\n");

​    ret = -EINVAL;

​    goto err_io;

  }

  //做一个地址映射将I/O内存资源的物理地址映射成核心虚地址后

  fbdev->regs = ioremap(res->start, res->end - res->start + 1);

  if (!fbdev->regs) {

​    dev_err(fbdev->dev, "failed to remap io region\n");

​    ret = -EINVAL;

​    goto err_mem;

  }

 

### 5.7.11.1、struct s3cfb_lcd

/*

 \* struct s3cfb_lcd

 \* @width:   horizontal resolution

 \* @height:   vertical resolution

 \* @p_width:      width of lcd in mm

 \* @p_height:      height of lcd in mm

 \* @bpp:    bits per pixel

 \* @freq:    vframe frequency

 \* @timing:   timing values

 \* @polarity:    polarity settings

 \* @init_ldi:    pointer to LDI init function

 *

*/

 

struct s3cfb_lcd {

  int width;

  int height;

  int p_width; 

  int p_height;

  int bpp;

  int freq;

  struct s3cfb_lcd_timing timing; //时序信息

  struct s3cfb_lcd_polarity polarity;

 

  void  (*init_ldi)(void);

  void  (*deinit_ldi)(void);

};

​    换了屏幕分辨率和尺寸不一样,改这个结构体就可以了.

### 5.7.11.2、pdata->cfg_gpio

//初始化LCD用到的一些GPIO

static void ek070tn93_cfg_gpio(struct platform_device *pdev)

{

  int i;

 

  for (i = 0; i < 8; i++) {

​    s3c_gpio_cfgpin(S5PV210_GPF0(i), S3C_GPIO_SFN(2));

​    s3c_gpio_setpull(S5PV210_GPF0(i), S3C_GPIO_PULL_NONE);

  }

 

  for (i = 0; i < 8; i++) {

​    s3c_gpio_cfgpin(S5PV210_GPF1(i), S3C_GPIO_SFN(2));

​    s3c_gpio_setpull(S5PV210_GPF1(i), S3C_GPIO_PULL_NONE);

  }

 

  for (i = 0; i < 8; i++) {

​    s3c_gpio_cfgpin(S5PV210_GPF2(i), S3C_GPIO_SFN(2));

​    s3c_gpio_setpull(S5PV210_GPF2(i), S3C_GPIO_PULL_NONE);

  }

 

  for (i = 0; i < 4; i++) {

​    s3c_gpio_cfgpin(S5PV210_GPF3(i), S3C_GPIO_SFN(2));

​    s3c_gpio_setpull(S5PV210_GPF3(i), S3C_GPIO_PULL_NONE);

  }

 

  /* mDNIe SEL: why we shall write 0x2 ? */

  writel(0x2, S5P_MDNIE_SEL);

 

  /* drive strength to max */

  writel(0xffffffff, S5PV210_GPF0_BASE + 0xc);

  writel(0xffffffff, S5PV210_GPF1_BASE + 0xc);

  writel(0xffffffff, S5PV210_GPF2_BASE + 0xc);

  writel(0x000000ff, S5PV210_GPF3_BASE + 0xc);

}

 

### 5.7.11.3、pdata->clk_on

static void clk_on(struct at91_udc *udc)

{

  if (udc->clocked)

​    return;

  udc->clocked = 1;

  clk_enable(udc->iclk);

  clk_enable(udc->fclk);

}

 

static void clk_off(struct at91_udc *udc)

{

  if (!udc->clocked)

​    return;

  udc->clocked = 0;

  udc->gadget.speed = USB_SPEED_UNKNOWN;

  clk_disable(udc->fclk);

  clk_disable(udc->iclk);

}

 

 

### 5.7.11.4、resource的处理

 //取出io资源

  res = platform_get_resource(pdev, IORESOURCE_MEM, 0);

  if (!res) {

​    dev_err(fbdev->dev, "failed to get io memory region\n");

​    ret = -EINVAL;

​    goto err_io;

  }

  //向内核申请资源

  res = request_mem_region(res->start,

​         res->end - res->start + 1, pdev->name);

  if (!res) {

​    dev_err(fbdev->dev, "failed to request io memory region\n");

​    ret = -EINVAL;

​    goto err_io;

  }

  //做一个地址映射将I/O内存资源的物理地址映射成核心虚地址后

  fbdev->regs = ioremap(res->start, res->end - res->start + 1);

  if (!fbdev->regs) {

​    dev_err(fbdev->dev, "failed to remap io region\n");

​    ret = -EINVAL;

​    goto err_mem;

  }

 

 

 

(1)platform_device中提供resource结构体数组

(2)probe中platform_get_resource取出resource并且按FLAG分头处理

 

 

## 5.7.12.framebuffer驱动分析3

static int __devinit s3cfb_probe(struct platform_device *pdev)

{

​     **…**

​     **…**

  s3cfb_set_vsync_interrupt(fbdev, 1);

  s3cfb_set_global_interrupt(fbdev, 1);

  s3cfb_init_global(fbdev);

//framebuffer分配内存

if (s3cfb_alloc_framebuffer(fbdev)) {

 

​    ret = -ENOMEM;

​    goto err_alloc;

  }

  //注册framebuffer,x210以公共注册了5个

  if (s3cfb_register_framebuffer(fbdev)) {

​    ret = -EINVAL;

​    goto err_register;

  }

 

static int s3cfb_alloc_framebuffer(struct s3cfb_global *ctrl)

{

  struct s3c_platform_fb *pdata = to_fb_plat(ctrl->dev);

  int ret, i;

 

  ctrl->fb = kmalloc(pdata->nr_wins *

​      sizeof(*(ctrl->fb)), GFP_KERNEL);

  if (!ctrl->fb) {

​    dev_err(ctrl->dev, "not enough memory\n");

​    ret = -ENOMEM;

​    goto err_alloc;

  }

 

  for (i = 0; i < pdata->nr_wins; i++) {

​    ctrl->fb[i] = framebuffer_alloc(sizeof(*ctrl->fb),

​             ctrl->dev);

​    if (!ctrl->fb[i]) {

​      dev_err(ctrl->dev, "not enough memory\n");

​      ret = -ENOMEM;

​      goto err_alloc_fb;

​    }

 

​    s3cfb_init_fbinfo(ctrl, i);

   …

   …

  }   //将s3cfb_global 这个结构体填充好LCD的设备为参数

static void s3cfb_init_fbinfo(struct s3cfb_global *ctrl, int id)

{

  struct fb_info *fb = ctrl->fb[id];

  struct fb_fix_screeninfo *fix = &fb->fix;

  struct fb_var_screeninfo *var = &fb->var;

  struct s3cfb_window *win = fb->par;

  struct s3cfb_alpha *alpha = &win->alpha;

  struct s3cfb_lcd *lcd = ctrl->lcd;

  struct s3cfb_lcd_timing *timing = &lcd->timing;

 

  memset(win, 0, sizeof(*win));

  platform_set_drvdata(to_platform_device(ctrl->dev), ctrl);

  strcpy(fix->id, S3CFB_NAME);

 

  win->id = id;

  win->path = DATA_PATH_DMA;

  win->dma_burst = 16;

  alpha->mode = PLANE_BLENDING;

 

  fb->fbops = &s3cfb_ops;

  fb->flags = FBINFO_FLAG_DEFAULT;

  fb->pseudo_palette = &win->pseudo_pal;

\#if (CONFIG_FB_S3C_NR_BUFFERS != 1)

  fix->xpanstep = 2;

  fix->ypanstep = 1;

\#else

  fix->xpanstep = 0;

  fix->ypanstep = 0;

\#endif

  fix->type = FB_TYPE_PACKED_PIXELS;

  fix->accel = FB_ACCEL_NONE;

  fix->visual = FB_VISUAL_TRUECOLOR;

  var->xres = lcd->width;

  var->yres = lcd->height;

\#if defined(CONFIG_FB_S3C_VIRTUAL)

  var->xres_virtual = CONFIG_FB_S3C_X_VRES;

  var->yres_virtual = CONFIG_FB_S3C_Y_VRES * CONFIG_FB_S3C_NR_BUFFERS;

\#else

  var->xres_virtual = var->xres;

  var->yres_virtual = var->yres * CONFIG_FB_S3C_NR_BUFFERS;

\#endif

  …

  …

  } 

### 5.7.12.1、一些硬件操作

(1)s3cfb_set_vsync_interrupt

**\drivers\video\samsung\s3cfb_fimd6x.c**

可以通过这个文件学习到我们是如何操作硬件的

int s3cfb_set_vsync_interrupt(struct s3cfb_global *ctrl, int enable)

{

  u32 cfg = 0;

 

  cfg = readl(ctrl->regs + S3C_VIDINTCON0);

  cfg &= ~S3C_VIDINTCON0_FRAMESEL0_MASK;

 

  if (enable) {

​    dev_dbg(ctrl->dev, "vsync interrupt is on\n");

​    cfg |= S3C_VIDINTCON0_FRAMESEL0_VSYNC;

  } else {

​    dev_dbg(ctrl->dev, "vsync interrupt is off\n");

​    cfg &= ~S3C_VIDINTCON0_FRAMESEL0_VSYNC;

  }

 

  writel(cfg, ctrl->regs + S3C_VIDINTCON0);

 

  return 0;

}

 

(2)s3cfb_set_global_interrupt

int s3cfb_set_global_interrupt(struct s3cfb_global *ctrl, int enable)

{

  u32 cfg = 0;

 

  cfg = readl(ctrl->regs + S3C_VIDINTCON0);

  cfg &= ~(S3C_VIDINTCON0_INTFRMEN_ENABLE | S3C_VIDINTCON0_INT_ENABLE);

 

  if (enable) {

​    dev_dbg(ctrl->dev, "video interrupt is on\n");

​    cfg |= (S3C_VIDINTCON0_INTFRMEN_ENABLE |

​      S3C_VIDINTCON0_INT_ENABLE);

  } else {

​    dev_dbg(ctrl->dev, "video interrupt is off\n");

​    cfg |= (S3C_VIDINTCON0_INTFRMEN_DISABLE |

​      S3C_VIDINTCON0_INT_DISABLE);

  }

 

  writel(cfg, ctrl->regs + S3C_VIDINTCON0);

 

  return 0;

}

 

### 5.7.12.2、s3cfb_init_global

**//\drivers\video\samsung\s3cfb.c**

static int s3cfb_init_global(struct s3cfb_global *ctrl)

{

ctrl->output = OUTPUT_RGB;

enum s3cfb_output_t {

  OUTPUT_RGB,

  OUTPUT_ITU,

  OUTPUT_I80LDI0,

  OUTPUT_I80LDI1,

  OUTPUT_WB_RGB,

  OUTPUT_WB_I80LDI0,

  OUTPUT_WB_I80LDI1,

};

 

ctrl->rgb_mode = MODE_RGB_P;

 

enum s3cfb_rgb_mode_t {

  MODE_RGB_P = 0,

  MODE_BGR_P = 1,

  MODE_RGB_S = 2,

  MODE_BGR_S = 3,

};

 

 

  init_completion(&ctrl->fb_complete);

  mutex_init(&ctrl->lock);

 

  s3cfb_set_output(ctrl);

  s3cfb_set_display_mode(ctrl);

  s3cfb_set_polarity(ctrl);

  s3cfb_set_timing(ctrl);

  s3cfb_set_lcd_size(ctrl);

 

  return 0;

}

### 5.7.12.3、向框架注册该fb设备

(1)s3cfb_alloc_framebuffer

(2)s3cfb_register_framebuffer

 

 

## 5.7.13.framebuffer驱动分析4

### 5.7.13.1、一些硬件操作

(1)s3cfb_set_clock

static int s3cfb_init_global(struct s3cfb_global *ctrl)

{

​       **…**

​       **…**

​           var->pixclock = lcd->freq * (var->left_margin + var->right_margin +

​        var->hsync_len + var->xres) *

​        (var->upper_margin + var->lower_margin +

​        var->vsync_len + var->yres);

​       **…**

​       **…**

  }

 

(2)s3cfb_set_window

  s3cfb_set_window(fbdev, pdata->default_win, 1);

static void s3cfb_set_window(struct s3cfb_global *ctrl, int id, int enable)

{

  struct s3cfb_window *win = ctrl->fb[id]->par;

 

  if (enable) {

​    s3cfb_window_on(ctrl, id);

​    win->enabled = 1;

  } else {

​    s3cfb_window_off(ctrl, id);

​    win->enabled = 0;

  }

}

 

(3)s3cfb_display_on

### 5.7.13.2、驱动中处理中断

(1)platform_get_irq

(2)request_irq

### 5.7.13.3、logo显示

\#if !defined(CONFIG_FRAMEBUFFER_CONSOLE) && defined(CONFIG_LOGO)

  if (fb_prepare_logo( fbdev->fb[pdata->default_win], FB_ROTATE_UR)) {

​    printk("Start display and show logo\n");

​    /* Start display and show logo on boot */

​    fb_set_cmap(&fbdev->fb[pdata->default_win]->cmap, fbdev->fb[pdata->default_win]);

​    fb_show_logo(fbdev->fb[pdata->default_win], FB_ROTATE_UR);

  }

\#endif

​    S3cfb_probe

​       Fb_prepare_logo

​           Fb_find_logo               真正查找logo

​       Fb_whow_logo

​           Fb_whow_logo_line          真正显示logo

​              Info->fbops->fb_imageblit    实际操作硬件fb

### 5.7.13.4、backlight点亮

\#endif

  mdelay(100);

  if (pdata->backlight_on)

​    pdata->backlight_on(pdev);

 

  return 0;

 

 

## 5.7.14.应用层为何不能设置分辨率

### 5.7.14.1、问题描述

(1)第4节时试图在应用层设置分辨率失败了，原因何在？

(2)定位问题：肯定是驱动的事儿

(3)进一步驱动中定位：ioctl部分的事儿

### 5.7.14.2、fb的ioctl部分

(1)fb是典型的字符设备驱动

(2)ioctl分为2部分，在驱动框架部分和驱动部分各有一半

(3)一路追踪找问题

  Fbmem.c

​      Fbmem_init

​         Register_chrdev

​             Fb_fops

​                Fb_ioctl

​                    Do_fb_ioctl

 

static long fb_ioctl(struct file *file, unsigned int cmd, unsigned long arg)

{

  struct inode *inode = file->f_path.dentry->d_inode;

  int fbidx = iminor(inode);

  struct fb_info *info = registered_fb[fbidx];

 

  return do_fb_ioctl(info, cmd, arg);

}

 

static long do_fb_ioctl(struct fb_info *info, unsigned int cmd,

​      unsigned long arg)

{

  case FBIOPUT_VSCREENINFO:

​    if (copy_from_user(&var, argp, sizeof(var)))

​      return -EFAULT;

​    if (!lock_fb_info(info))

​      return -ENODEV;

​    acquire_console_sem();

​    info->flags |= FBINFO_MISC_USEREVENT;

​    ret = fb_set_var(info, &var);

​    info->flags &= ~FBINFO_MISC_USEREVENT;

​    release_console_sem();

​    unlock_fb_info(info);

​    if (!ret && copy_to_user(argp, &var, sizeof(var)))

​      ret = -EFAULT;

​    break;

​    **…**

​    **…**

}

int

fb_set_var(struct fb_info *info, struct fb_var_screeninfo *var)

{

  int flags = info->flags;

  int ret = 0;

 

  if (var->activate & FB_ACTIVATE_INV_MODE) {

​    struct fb_videomode mode1, mode2;

 

​    fb_var_to_videomode(&mode1, var);//应用层传过来的

​    fb_var_to_videomode(&mode2, &info->var); //当前内核中存储的var

​    /* make sure we don't delete the videomode of current var */

​    ret = fb_mode_is_equal(&mode1, &mode2);

 

​    if (!ret) {

​      struct fb_event event;

 

​      event.info = info;

​      event.data = &mode1;

​      ret = fb_notifier_call_chain(FB_EVENT_MODE_DELETE, &event);

​    }

 

​    if (!ret)

​      fb_delete_videomode(&mode1, &info->modelist);

 

​    ret = (ret) ? -EINVAL : 0;

​    goto done;

  }

 

  if ((var->activate & FB_ACTIVATE_FORCE) ||

​    memcmp(&info->var, var, sizeof(struct fb_var_screeninfo))) {

​    u32 activate = var->activate;

 

​    if (!info->fbops->fb_check_var) {

​      *var = info->var;

​      goto done;

​    }

 

​    ret = info->fbops->fb_check_var(var, info);

//\drivers\video\samsung\s3cfb.c

​    struct fb_ops s3cfb_ops = {

  .owner = THIS_MODULE,

  .fb_fillrect = cfb_fillrect,

  .fb_copyarea = cfb_copyarea,

  .fb_imageblit = cfb_imageblit,

  .fb_check_var = s3cfb_check_var,

  .fb_set_par = s3cfb_set_par,

  .fb_blank = s3cfb_blank,

  .fb_pan_display = s3cfb_pan_display,

  .fb_setcolreg = s3cfb_setcolreg,

  .fb_ioctl = s3cfb_ioctl,

  .fb_open = s3cfb_open,

  .fb_release = s3cfb_release,

};

 

static void s3cfb_set_alpha_info(struct fb_var_screeninfo *var,

​        struct s3cfb_window *win)

{

  if (var->transp.length > 0)

​    win->alpha.mode = PIXEL_BLENDING;

  else {

​    win->alpha.mode = PLANE_BLENDING;

​    win->alpha.channel = 0;

​    win->alpha.value = S3CFB_AVALUE(0xf, 0xf, 0xf);

  }

}

static int s3cfb_check_var(struct fb_var_screeninfo *var, struct fb_info *fb)

{

  struct s3cfb_window *win = fb->par;

  struct s3cfb_global *fbdev =

​    platform_get_drvdata(to_platform_device(fb->device));

  struct s3cfb_lcd *lcd = fbdev->lcd;

 

  dev_dbg(fbdev->dev, "[fb%d] check_var\n", win->id);

 

  if (var->bits_per_pixel != 16 && var->bits_per_pixel != 24 &&

​    var->bits_per_pixel != 32) {

​    dev_err(fbdev->dev, "invalid bits per pixel\n");

​    return -EINVAL;

  }

 

  if (var->xres > lcd->width)    //这里就解释了应用层为什么不能修改分辨率

​    var->xres = lcd->width;

 

  if (var->yres > lcd->height)

​    var->yres = lcd->height;

…

…

}

## 5.7.15.折腾内核的启动logo

### 5.7.15.1、让logo显示在屏幕中央

​    

(1)sudo apt-get install netpbm （先让ubuntu上网）

(2)pngtopnm logo.png | ppmquant -fs 224 | pnmtoplainpnm > logo_linux_clut224.ppm

(3)用制作好的logo_linux_clut224.ppm，替换源码目录drivers/video/logo/logo_x210_clut224.ppm，然后重新编译内核

(4)启动新内核，就能看到新的启动logo了。

### 5.7.15.2、自定义内核启动logo

​    S3cfb_probe

​       Fb_prepare_logo

​           Fb_find_logo               真正查找logo

​       Fb_whow_logo

​           Fb_whow_logo_line          真正显示logo

​              Info->fbops->fb_imageblit    实际操作硬件fb

 

static void fb_do_show_logo(struct fb_info *info, struct fb_image *image,

​        int rotate, unsigned int num)

{

 

  image.dx = 0;    //logo左上角的起始x值

  image.dy = y;    //logo左上角的起始y值

  image.width = logo->width; //logo的大小

  image.height = logo->height;

 

修改：不要直接写计算后的得数，这样没有可移植性，去结构体里取数据来；

image.dx = (info->var.xres - logo->width) / 2;    // logo显示的起始坐标x值

image.dy = (info->var.yres - logo->height) / 2;   

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 



 

 

 

 

 

# 第八章 input子系统基础之按键

## 5.8.0 章节概要

5.8.1.什么是input子系统

​    本节全面介绍input子系统的概念和来源、解决的主要问题，目的是让大家对linux中输入类设备有一个全面了解

5.8.2.input设备应用层编程实践1

​    本节实践编写应用层程序，操作键盘和鼠标这些常见input类设备，目的是让大家先学会使用输入类设备，后面再来分析驱动。

5.8.3.input设备应用层编程实践2

​    本节接着上节对读上来的数据进行解析，分析其规律并且和设备本身特性进行关联分析。

5.8.4.input子系统架构总览1

​    本节详细介绍input子系统的三层结构以及各层的功能特点。

5.8.5.input子系统架构总览2

​    本节介绍input子系统下编写驱动的路线和方法。

5.8.6.输入核心层源码分析1

​    本节分析输入核心层，主要是模块装载和开放给其他层的接口的分析。

5.8.7.输入核心层源码分析2    

​    本节接着分析输入核心层，主要是handler和device的匹配、安装部分的源码分析。

5.8.8.输入事件驱动层源码分析

​    本节对输入事件层源码分析，主要以evdev.c为例分析了event handler的安装函数、数据上报函数的实现。

5.8.9.输入设备驱动层源码分析1

​    本节分析输入设备驱动层，以x210自带的按键驱动为例进行分析。

5.8.10.输入设备驱动层源码分析2   

​    本节接着分析按键驱动，主要是一些源码细节探究。

5.8.11.中断方式按键驱动实战1

​    本节开始按键驱动实战，先找到内核提供的模版，并且对模版程序进行分析讲解。

5.8.12.中断方式按键驱动实战2 

​    本节以模版驱动为基础，结合x210开发板的情况进行驱动移植、编译、测试、修改。

 

## 5.8.1.什么是input子系统

​    Linux内核的一套体系，用来管理输入设备的。不光包含驱动还包含应用。

### 5.8.1.1、何为输入设备

​    通过外部的接口给我们的操作系统输入一些信息进来。输入设备是各种各样的，Linux中的输入设备就是用来处理管理这些不同的输入设备。

### 5.8.1.2、linux中输入设备的编程模型

 

(1)命令行界面的输入类设备应用接口

(2)GUI界面带来的麻烦、用户的自由度大，操作系统相应用户的操作就比较困难。不同的输入类设备也会带来麻烦，比如说键盘、鼠标、触摸屏都有自己的特点，因为物理原理工作原理不一样，输入的信号也不一样，他们之间存在差异。应用程序需要相应这些不同的设备，我们发明 struct input_event 结构体，用来管理这些不同的设备输入信号。

(3)struct input_event 在kernel/include/linux/Input.h中，

struct input_event {

  struct timeval time;

  __u16 type;

  __u16 code;

  __s32 value;

};

  这个结构体包含了输入设备的所有的信息，linux系统设定的输入事件从驱动层到应用层反馈的一个借口。一个这个结构体的变量就表示了一次输入的事件。什么是输入事件？比如说你动一下鼠标，按下了一个键盘按键。当你发生了一次输入事件的时候，操作系统需要去感知这次输入事件，并且把你输入的事件感知出来，到底干了什么?比如说感知鼠标到时是左移了还是右移动了，什么时候移动了的？这些都在 struct input_event 结构体当中。 

struct timeval time：这个结构体linux内核用来描述时间的，这是个时间点，表

明输入事件发生的时间。

 __u16 type: 表示哪一种输入设备输入的？比如说是键盘还是鼠标

__u16 code: 输入设备的编码。比如说按下键盘的B键，那么B键的编码是多少？

 __s32 value: 比如说你按下B键的值就1，B键弹起来值就是0；鼠标可能就表示移动了多少。

​    这个结构体就是用来驱动层和应用层之间来进行一个标准输入类事件的传送。驱动层读取输入事件包装成这个类型的结构体变量然后送个应用层去解析执行。

### 5.8.1.3、input子系统简介

(1)linux的input子系统解决了什么问题

​      管理了处理各类不同的输入设备。

(2)input子系统分4个部分：应用层 + input event + input core（内核驱动矿建那一部分）+ 硬件驱动。

(3)input子系统如何工作

   以此时输入的事件的发生，响应、结束的过程来介绍：

​     假设一切就绪，没有事件发生，一切都是静止状态。当按下鼠标时候，硬件接受到了中断事件，执行了中断处理程序。把这个事件上报给input core部分，最后上报给iput event层包装成 input_event结构体题，应用层通过read函数读取这个结构体就可以得到发生的事件。

(4)事件驱动型GUI框架，如QT、VC等。

​    将来学习GUI框架，QT、VC有个理念就叫事件驱动型，当没有事件发生就在那里等待，当有事件发生应用层（GUI）就去处理相应的事件。

 

## 5.8.2.input设备应用层编程实践1

### 5.8.2.1、确定设备文件名

(1)应用层操作驱动有2条路：/dev目录下的设备文件，/sys目录下的属性文件

(2)input子系统用的/dev目录下的设备文件，具体一般都是在 /dev/input/eventn

(3)用cat命令来确认某个设备文件名对应哪个具体设备。我在自己的ubuntu中实测的键盘是event1，而鼠标是event3。

（4）自己的键盘是event1，鼠标event2；

### 5.8.2.2、标准接口打开并读取文件

\#include <stdio.h>

\#include <sys/types.h>

\#include <sys/stat.h>

\#include <fcntl.h>

\#include <unistd.h>

\#include <linux/input.h>

\#include <string.h>

 

\#define DEV_KEY   "/dev/input/event1"

\#define DEV_MOUSE  "/dev/input/event2"

 

int main(void)

{

  int fd = -1;

  int ret = -1;

  struct input_event ev;

  //打开设备文件

  fd = open(DEV_KEY, O_RDONLY);

  if(fd < 0){

​    perror("open");

​    close(fd);

​    return -1;

  }

  while(1){

​    //读取一个event时间 ,有缓冲区，并不是有输入就读出来，而是一定量后才读出来

​    memset(&ev, 0, sizeof(struct input_event));

​    ret = read(fd, &ev, sizeof(struct input_event));

​    if(ret != sizeof(struct input_event))

​    {

​      perror("read");

​      close(fd);

​      return -1;

​    }

​    //解析event包

​    printf("%s",(unsigned char*)&ev);

  }

 

  close(fd);

  return 0;

}

### 5.8.2.3、解析struct input_event

   //解析event包

​    printf("%s",(unsigned char*)&ev);

​    按字符读出来就是乱码

​     

 

## 5.8.3.input设备应用层编程实践2

### 5.8.3.1、解析键盘事件数据

 

\#define EV_SYN     0x00   //同步类型

\#define EV_KEY     0x01   //按键

\#define EV_REL     0x02   //relative，相对的，主要描述鼠标坐标

\#define EV_ABS     0x03   //absolutate,绝对的，主要是触摸屏坐标

\#define EV_MSC     0x04   //常用类设备,键盘

q-----------------------

type: 4

code: 4

value: 16

 

\-----------------------

type: 1  

code: 16

value: 0

 

\-----------------------

type: 0

code: 0

value: 0

 type4表示键盘，code都是4表示扫描类，键值就是对应的按键，如q是16； 第二个type1表示是一个按键，code对应按键，value的值代表有没有按下去。Type0表示同步信号，code 0 表示报告，值0表示

### 5.8.3.2、解析鼠标事件数据

type: 3

code: 0

value: 13219

 

\-----------------------

type: 0

code: 0

value: 0

 

## 5.8.4.input子系统架构总览1

### 5.8.4.1、input子系统分为三层

(1)最上层：输入事件驱动层，/drivers/input/evdev.c和mousedev.c和joydev.c属于这一层

(2)中间层：输入核心层，/drivers/input/input.c属于这一层

(3)最下层：输入设备驱动层，drivers/input/xxx 文件夹下

### 5.8.4.2、input类设备驱动开发方法

 

Input Core 用来协调上下层，不负责干具体的工作。

最下层：输入设备驱动，主要负责硬件的操作，把硬件的具体信息提取出来上报到核心层，核心层上报给输入事件层。

最下层：从核心层拿过来硬件的数据包装 struct input_event 结构体给用户。

(1)输入事件驱动层和输入核心层不需要动，只需要编写设备驱动层

(2)设备驱动层编写的接口和调用模式已定义好，驱动工程师的核心工作量是对具体输入设备硬件的操作和性能调优。

(3)input子系统不算复杂，学习时要注意“标准模式”四个字。

 

 

## 5.8.5.input子系统架构总览2

Event handler 是一个通用型的模型。

 

## 5.8.6.输入核心层源码分析1

**文件在drivers/input/input.c**

### 5.8.6.1、核心模块注册input_init

static int __init input_init(void)

{

  int err;

 

  input_init_abs_bypass(); 

 

  err = class_register(&input_class);

  if (err) {

​    printk(KERN_ERR "input: unable to register input_dev class\n");

​    return err;

  }

 

  err = input_proc_init();

  if (err)

​    goto fail1;

 

  err = register_chrdev(INPUT_MAJOR, "input", &input_fops);

  if (err) {

​    printk(KERN_ERR "input: unable to register char major %d", INPUT_MAJOR);

​    goto fail2;

  }

 

  return 0;

 

 fail2: input_proc_exit();

 fail1: class_unregister(&input_class);

  return err;

}

 

(1)class_register

(2)input_proc_init

(3)register_chrdev

### 5.8.6.2、设备驱动层的接口函数

(1)input_allocate_device 

​     struct input_dev *input_allocate_device(void)

{

  struct input_dev *dev; //定义一个输入结构体变量

 

  dev = kzalloc(sizeof(struct input_dev), GFP_KERNEL);

  if (dev) {

​    dev->dev.type = &input_dev_type;

​    dev->dev.class = &input_class;

​    device_initialize(&dev->dev); 

​    mutex_init(&dev->mutex);

​    spin_lock_init(&dev->event_lock);

​    INIT_LIST_HEAD(&dev->h_list);

​    INIT_LIST_HEAD(&dev->node);

 

​    __module_get(THIS_MODULE);

  }

 

  return dev;

}

​    数据结构类的初始化。

 

(2)input_set_capability

void input_set_capability(struct input_dev *dev, unsigned int type, unsigned int code)

{

  switch (type) {

  case EV_KEY:

​    __set_bit(code, dev->keybit);

​    break;

 

  case EV_REL:

​    __set_bit(code, dev->relbit);

​    break;

 

  case EV_ABS:

​    __set_bit(code, dev->absbit);

​    break;

 

​    和设备操作有关的初始化。给我们驱动用来向哪些设备文件汇报事件的能力。

​    如：input_set_capability(dev, EV_KEY, BTN_LEFT);

 

(3)input_register_device

 输入设备的注册（已经被第一个函数填充好的）。

 

## 5.8.7.输入核心层源码分析2

### 5.8.7.1、handler和device的匹配

(1) input_register_device

input_attach_handler

​       input_match_device       匹配device和handler

​       handler->connect(handler, dev, id)      连接device和handler

​    把自己绑到了input到链表上来，给dev找handler。

### 5.8.7.2、事件驱动层的接口函数

 

(1)input_register_handler 向input core 注册一个handler，

 

(2)input_register_handle结构体 ： 用来处理handler和dev 之间的事情，用来记录的，表示一个handler和dev匹配上了。相当于结婚证，记录了handler和dev的信息。

 

 

## 5.8.8.输入事件驱动层源码分析

**代码在\drivers\input\evdev.c**

### 5.8.8.1、input_handler

static struct input_handler evdev_handler = {

  .event   = evdev_event,

  .connect  = evdev_connect,

  .disconnect = evdev_disconnect,

  .fops    = &evdev_fops,

  .minor   = EVDEV_MINOR_BASE,

  .name    = "evdev",

  .id_table  = evdev_ids,

};

 

 

### 5.8.8.2、evdev_connect

static int evdev_connect(struct input_handler *handler, struct input_dev *dev,

​       const struct input_device_id *id)

{

  struct evdev *evdev;

  int minor;

  int error;

​    …

​    …

 

​     //绑定传进来的dev 设备到evdev

​      evdev->handle.dev = input_get_device(dev);

  //绑定传进来的handler 到 evdev

​    evdev->handle.handler = handler;

 

​    …

​    …

 

}

在数组的格子里面找到一个minor，注册了struct evdev *evdev结构体变量进行必要的填充，然后分别调device_initialize(&evdev->dev);device_add

(&evdev->dev); 用来设备注册,其实这两个函数就是device_register()函数的拆分。input_register_handle(&evdev->handle)向我们的链表注册了一个handle

 

### 5.8.8.3、evdev_event

static void evdev_event(struct input_handle *handle,

​      unsigned int type, unsigned int code, int value)

{

  struct evdev *evdev = handle->private;

  struct evdev_client *client;

  struct input_event event;

  struct timespec ts;

 

  ktime_get_ts(&ts); //内核里面的一个函数，获取内核里的一个时间的。

  event.time.tv_sec = ts.tv_sec;

  event.time.tv_usec = ts.tv_nsec / NSEC_PER_USEC;

  event.type = type;

  event.code = code;

  event.value = value;

 

  rcu_read_lock();

 

  client = rcu_dereference(evdev->grab);

  if (client)

​    evdev_pass_event(client, &event);//把event数据丢个client

  else  //通知多个client

​    list_for_each_entry_rcu(client, &evdev->client_list, node)

​      evdev_pass_event(client, &event);

 

  rcu_read_unlock();

 

  wake_up_interruptible(&evdev->wait);

}

 

static void evdev_pass_event(struct evdev_client *client,

​         struct input_event *event)

{

  /*

   \* Interrupts are disabled, just acquire the lock

   */

  spin_lock(&client->buffer_lock);

  wake_lock_timeout(&client->wake_lock, 5 * HZ);

  client->buffer[client->head++] = *event; //将包丢给了client

  client->head &= EVDEV_BUFFER_SIZE - 1;

  spin_unlock(&client->buffer_lock);//阻塞式访问被解除

 

  if (event->type == EV_SYN)

​    kill_fasync(&client->fasync, SIGIO, POLL_IN); //发一个异步通知

}   //向我们那个上层关注这个handler发一个SIGIO信号；我们应用层正在poll它。通知我们用异步函数的那个进程可以读它？

### 5.8.8.4、linux spin_lock应用分析

spin_lock是一种死等机制。

场景分析：

spin_lock保护的资源可能来自多个cpu_core的进程上下文和中断上下文。

1，两个进程A和B，在系统调用时均会访问共享资源R，

假设A访问共享资源R时发生中断，中断结束后唤醒了具有更高优先级的进程B，如果没有锁保护，就会导致有两个进程进入临界区，导致竞态。

在进程A加上spin_lock，那么B在由于没有获得lock，会一直等待。如果A,B运行在同一个core上，此时系统进入永久的spin。所以kernel的做法是在A进程获取spin_lock时禁止本CPU上的抢占。

2，两个进程A和B，在系统调用时会访问共享资源R，并且外设P的中断handler中也会访问R

假设进程cpu0上的进程A持有spin_lock进入临界区，外设P发生了中断，如果中断handler被调度到同一个cpu core（cpu0）那么handler中也会出现死锁，所以涉及中断上下文的访问，spin_lock需要和禁止本cpu的中断联合使用。

3，不同中断上下文间的竞争

同一种中断时不论在单核多核都是不会并行的，不存在保护问题。

不同中断中，需要加spin_lock保护共享资源，而且在中断handler中都是关闭中断的，所以spin_lock不需要关中断操作的配合。

————————————————

版权声明：本文为CSDN博主「weixin_29379325」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。

原文链接：https://blog.csdn.net/weixin_29379325/article/details/82973027

## 5.8.9_10.输入设备驱动层源码分析1_2

### 5.8.9.1、先找到bsp中按键驱动源码

(1)锁定目标：板载按键驱动

(2)确认厂家提供的BSP是否已经有驱动

(3)找到bsp中的驱动源码

 

 用s3c-button在vscode中搜索

， 

发现有两个， 一个是三星官方开发的，一个九鼎开发的。

### 5.8.9.2、按键驱动源码初步分析

(1)模块装载分析

(2)平台总线相关分析

(3)确定重点：probe函数

  

 

 

 

 

 

 

 

 

Value1代表按下，0代表弹起。这里没有同步包。

### 5.8.9.3、源码细节实现分析

(1)gpio_request

static int s3c_button_probe(struct platform_device *pdev)

{

  int i;

  int ret;

 

\#if defined(CONFIG_X210) && (CONFIG_X210 > 0)

  /* gph0_1 (power) */

  ret = gpio_request(S5PV210_GPH0(1), "GPH0");

  if(ret)

​    printk("button-x210: request gpio GPH0(1) fail");

  s3c_gpio_setpull(S5PV210_GPH0(1), S3C_GPIO_PULL_UP);

s3c_gpio_cfgpin(S5PV210_GPH0(1), S3C_GPIO_SFN(0));

s3c_button_history[0] = gpio_get_value(S5PV210_GPH0(1));

 

​    input = input_allocate_device(); //分配内存，input 全局变量

// static struct input_dev * input;

 

  if(!input) 

​    return -ENOMEM;

 

  set_bit(EV_KEY, input->evbit); //告诉我们当前我们驱动支持EV_KEY这个事件

 

  for(i = 0; i < MAX_BUTTON_CNT; i++)//设置支持哪些KEY

​    set_bit(s3c_Keycode[i], input->keybit);

tatic int s3c_Keycode[MAX_BUTTON_CNT] = {KEY_POWER, KEY_UP, KEY_DOWN, KEY_LEFT, KEY_RIGHT, KEY_A, KEY_B};

  input->name = "s3c-button";

  input->phys = "s3c-button/input0";

  //校验匹配

  input->id.bustype = BUS_HOST;

  input->id.vendor = 0x0001;

  input->id.product = 0x0001;

  input->id.version = 0x0100;

 

  input->keycode = s3c_Keycode;

 

  if(input_register_device(input) != 0)

  {

​    printk("s3c-button input register device fail!!\n");

 

​    input_free_device(input);

​    return -ENODEV;

  }

 

  /* Scan timer init */ 启动计时器用来轮询查看这个按键。

  init_timer(&timer);

  timer.function = s3cbutton_timer_handler;//时间到了自动启动这个函数

 

  timer.expires = jiffies + (HZ/100);//内核定时器的时间；jiffies当前时间。

  add_timer(&timer);

 

  static void s3cbutton_timer_handler(unsigned long data)

{

  int flag;

 

  /* power */

  flag = gpio_get_value(S5PV210_GPH0(1));

  if(flag != s3c_button_history[0])

  {

​    if(flag)

​    {

​      input_report_key(input, s3c_Keycode[0], 0);//向上层上报按键信息

​    }

​    else

​    {

​      input_report_key(input, s3c_Keycode[0], 1);

​    }

​    s3c_button_history[0] = flag;

  }

   …

   …

  /* Kernel Timer restart */

  mod_timer(&timer,jiffies + HZ/100);//更新定时器，又会重新开始计时

}

 

(2)input_allocate_device

(3)input_register_device

(4)timer

 

### 5.8.9.4、上下拉电阻

**注：上拉就是在****gpio****引脚接一个电阻，电阻一侧接高电平，相当于在外部输入电流，使引脚处于高电平状态**

**数字电路有三种状态：高电平、低电平和高阻状态。但有些场合却不希望出现高阻状态，通过上拉电阻或者下拉电阻就可以使电路处于稳定的状态，具体视设计要求而定。输出高电**平是要有足够的电流给后面的输入口，输出低电平要限制住吸入电流的大小上下拉电阻的应用道理类似，下面就以上拉电阻为例说明：

1.上拉电阻的作用

① 当前端逻辑**输出驱动输出的高电平**低于后级逻辑电路输入的**最低高电平时**，就需要在前级的输入端接上拉电阻，以提高输出高电平的值；同时提高芯片输入信号的噪声容限，以增强抗干扰能力。

②为加大高电平输出时引脚的驱动能力，有的单片机引脚上也常使用上拉电阻。

③OC门必须加上上拉电阻是引脚悬空有确定的状态，实现“线与”功能。

④在CMOS芯片上，为了防止静电造成损坏，不用的引脚不能悬空，一般都要接上上拉电阻降低输入阻抗，提供泄荷通路。

⑤引脚悬空比较容易受到外界电磁干扰，加上拉电阻可以提高总线的抗电磁干扰能力。

⑥长线传输中电阻不匹配容易引起反射波阻抗，加上下拉电阻是电阻匹配，有效的抑制反射波干扰

下拉电阻：和上拉电阻的原理差不多，只是拉到GND去而已，那样电平就会被拉低。 下拉电阻一般用于设定低电平或者是阻抗匹配(抗回波干扰)。

拉电阻是用来解决总线驱动能力不足时提供电流的。一般说法是拉电流，下拉电阻是用来吸收电流的，也就是灌电流。

 

## 5.8.11_12.中断方式按键驱动实战1、2

### 5.8.11.1、模板

​    模板在： \Documentation\input\input-programming.txt文件中

这个文件告诉我们input下面的驱动如何编写的。

(1)input类设备驱动模式非常固定，用参考模版修改即可

(2)新建驱动项目并粘贴模版内容

### 5.8.11.2、模板驱动的解析

request_irq(unsigned int irq, irq_handler_t handler, unsigned long flags,

​    const char *name, void *dev)

{

  return request_threaded_irq(irq, handler, NULL, flags, name, dev);

}

//irq中断号用来确认，voide*dummy用来做共享中断的，有好多个按钮用一个中断号，就是用

//dummy来区分那个按键的。

static irqreturn_t button_interrupt(int irq, void *dummy)

{

  input_report_key(button_dev, BTN_0, inb(BUTTON_PORT) & 1);

  input_sync(button_dev); //报一个同步包数据；

  return IRQ_HANDLED; //操作系统中断需要返回值，有区别与裸机没有返回值。操作系统中断程序为什么要返回值呢？是因为操作系统需要对这类资源进行管控

}

 

​    第一个参数：是中断号，中断号在\arch\arm\mach-s5pv210\include\mach\irqs.h文件中；

​    第二个是：中断处理程序

​    第三个是：标志位，上升沿触发，下降沿触发

​    第四个是： 名字，和gpio一样，给它一个名字，将来查用这个名字查就可以了。

​    第五个是：传递参数，如果不需要传给个NULL就行，传的这个参数其实就是给 void*dummy的。

### 5.8.11.3、着手移植驱动

\#include <linux/input.h>

\#include <linux/module.h>

\#include <linux/init.h>

 

\#include <asm/irq.h>

\#include <asm/io.h>

\#include <mach/irqs.h>

\#include <linux/interrupt.h>

\#include <linux/gpio.h>

 

\#define   BUTTON_IRQ   IRQ_EINT2

 

/*

 \* X210:

 *

 \* POWER -> EINT1  -> GPH0_1

 \* LEFT  -> EINT2  -> GPH0_2 √

 \* DOWN  -> EINT3  -> GPH0_3

 \* UP   -> KP_COL0 -> GPH2_0

 \* RIGHT -> KP_COL1 -> GPH2_1

 \* MENU  -> KP_COL3 -> GPH2_3 (KEY_A)

 \* BACK  -> KP_COL2 -> GPH2_2 (KEY_B)

 */

 

static struct input_dev *button_dev;

//irq中断号用来确认，voide*dummy用来做共享中断的，有好多个按钮用一个中断号，就是用

//dummy来区分那个按键的。

static irqreturn_t button_interrupt(int irq, void *dummy)

{

  int flag;

 

  s3c_gpio_cfgpin(S5PV210_GPH0(2), S3C_GPIO_SFN(0)); //输入模式

  flag = gpio_get_value(S5PV210_GPH0(2));

  s3c_gpio_cfgpin(S5PV210_GPH0(2), S3C_GPIO_SFN(0xf)); //eint_2模式

 

  input_report_key(button_dev, KEY_LEFT, !flag);

  input_sync(button_dev);

  return IRQ_HANDLED;

}

 

static int __init button_init(void)

{

  int error;

 

  error = gpio_request(S5PV210_GPH0(2), "GPH0_2");

  if(error)

​    printk("button-x210: request gpio GPH0(1) fail");

  s3c_gpio_cfgpin(S5PV210_GPH0(2), S3C_GPIO_SFN(0xf)); //eint_2模式

  

  //申请一个中断，

  if (request_irq(BUTTON_IRQ, button_interrupt, 

​    IRQF_TRIGGER_RISING |IRQF_TRIGGER_FALLING, "button-x210", NULL)) {

​        printk(KERN_ERR "key-s5pv210.c: Can't allocate irq %d\n", BUTTON_IRQ);

​        return -EBUSY;

​    }

 

  button_dev = input_allocate_device();

  if (!button_dev) {

​    printk(KERN_ERR "key-s5pv210.c: Not enough memory\n");

​    error = -ENOMEM;

​    goto err_free_irq;

  }

 

​    //BIT_MASK比较老式的方法， 

  /*      新方法

  set_bit(EV_KEY, button_dev.evbit);

  set_bit(BTN_0, button_dev.keybit);

  */

  button_dev->evbit[0] = BIT_MASK(EV_KEY);

  button_dev->keybit[BIT_WORD(KEY_LEFT)] = BIT_MASK(KEY_LEFT);

​    

  error = input_register_device(button_dev);

  if (error) {

​    printk(KERN_ERR "key-s5pv210.c.c: Failed to register device\n");

​    goto err_free_dev;

  }

 

  return 0;

 

 err_free_dev:

  input_free_device(button_dev);

 err_free_irq:

  free_irq(BUTTON_IRQ, button_interrupt);

  return error;

}

 

static void __exit button_exit(void)

{

​    input_unregister_device(button_dev);

  free_irq(BUTTON_IRQ, button_interrupt);

}

 

module_init(button_init);

module_exit(button_exit);

MODULE_LICENSE("GPL");

MODULE_AUTHOR("Sheridan <jay4xie@qq.com>");

MODULE_DESCRIPTION("Keyboard driver for x210 button.");

MODULE_ALIAS("platform:x210-button");

 

 

 

测试实践,说明起作用了，按下去值是1，弹起来值是0，code105,代表KEY_LEFT

 

 

 

 

 

 

 

 



 

 

 

# 第九章 触摸屏驱动移植实战

## 5.9.0 章节概要

5.9.1.触摸屏驱动概览

​    本节主要介绍了2种触摸屏的驱动差别，本课程用到的三个版本开发板的触摸屏差别以及学习触摸屏驱动的关键点和学习方法。

5.9.2.内核中的竞争状态和互斥1

​    本节将前面课程中提到，但是没有详细总结的内核竟态处理方法如原子操作、互斥锁、自旋锁等做了详细系统的总结。

5.9.3.内核中的竞争状态和互斥2

​    本节将前面课程中提到，但是没有详细总结的内核竟态处理方法如原子操作、互斥锁、自旋锁等做了详细系统的总结。

5.9.4.中断的上下半部1

​    本节主要讲了linux内核中中断上下半部的思路，以及三种下半部解决方案。

5.9.5.中断的上下半部2

​    本节主要通过代码实例来演示tasklet、workqueue等中断下半部的处理，目的是希望大家掌握这套处理流程，在后面分析触摸屏驱动时看到这些代码能更容易的理解。

5.9.6.linux内核的I2C子系统详解1

​    本节主要对I2C总线的特征做了个汇总，并专门就一些理解的关键点指出说明。

5.9.7.linux内核的I2C子系统详解2  

​    本节对内核的I2C子系统的构建和组成部分做了整体说明。

5.9.8.linux内核的I2C子系统详解3

​    本节讲了I2C系统的四个关键结构体，理解这四个结构体及其关联对理解I2C系统的工作原理至关重要。

5.9.9.linux内核的I2C子系统详解4

​    本节带大家分析i2c_core.c的源码，该文件主要为其他部分提供各种注册接口，属于I2C子系统的关键性代码块。

5.9.10.linux内核的I2C子系统详解5

​    本节以S5PV210芯片的i2c_adapter驱动为例，详细分析了主机SoC的I2C控制器部分的驱动源码及其实现细节。

5.9.11.linux内核的I2C子系统详解6

​    本节分析了i2c_driver部分的细节，包括driver的匹配方式、probe函数调用的过程分析等

5.9.12.linux内核的I2C子系统详解7

​    本节分析了i2c_client的来源，包括board_info如何在mach文件注册，如何通过i2c_new_device接口将其转为i2c_client结构体。

5.9.13.gslX680驱动的移植实践

​    本节将提供的gslX680的驱动源码移植到老版本内核中并且添加必要的配置，使之正常工作，这种移植工作以后在工作中用到的可能性很大。

5.9.14.gslX680驱动源码分析2

​    本节开始分析gslX680的驱动源码，主要是i2c_driver和client端的匹配、i2c_client的创建和数据传递这些。

5.9.15.gslX680驱动源码分析2

​    本节接着分析gslX680的驱动源码，主要是probe函数中的操作，以及中断下半部中上报坐标数据等内容。

5.9.16.老版本触摸屏的驱动 

​    本节对老版本开发板中ft5x05的驱动源码进行了分析，以及移植方面的实践操作。通过2款触摸屏的驱动对比，让大家思路更加宽广。

## 5.9.1.触摸屏驱动概览

### 5.9.1.1、常用的2种触摸屏

(1)电阻触摸屏。驱动一般分2种：一种是SoC内置触摸屏控制器，一种是外置的专门触摸屏控制芯片，通过I2C接口和SoC通信。

(2)电容触摸屏。驱动只有一种，外接专用的电容式触摸屏控制芯片，I2C接口和SoC通信。

### 5.9.1.2、X210使用的触摸屏

(1)X210V3使用的触摸屏：ft5x06

(2)X210V3S使用的触摸屏：gslX680

### 5.9.1.3、学习触摸屏驱动的关键点

(1)input子系统相关知识

(2)中断上下半部

(3)I2C子系统

(4)触摸屏芯片本身知识

### 5.9.1.4、竞争状态和同步

 

 

## 5.9.2_3.内核中的竞争状态和互斥1_2

### 5.9.2.1、一些概念

(1)竞争状态（简称竟态）

​    由并发引起的危险现象。有一段代码中间被打断了可能会出问题。

(2)临界段、互斥锁、死锁

临界段：这段代码有可能被打断出事。在临界段代码都要加些互斥条件来保护它。临界段一般加互斥锁来保护。互斥锁的使用不对把自己坑死了叫做死锁，A有一把钥匙，B有一把钥匙。A拿着A的钥匙在B的房间里，B拿着B的钥匙在A的房间里。

(3)同步（多核CPU、多任务、中断）

所谓同步就是并行执行，并发。

多核CPU：比如说4核CPU同时处理4条指令，这4条指令没有上下逻辑关系是没有问题的，如果有下上逻辑关系就有问题。

多任务：逻辑关联的代码

### 5.9.2.2、解决竟态的方法

(1)原子操作 automic_t

原子英文里有不可分割的意思，原子操作意思就是说这个操作是不可以被分割，一旦执行就执行到底。原子操作在不同的CPU中实现是不一样的。

(2)信号量、互斥锁

在运行这段代码时候将它保护起来，同时只让一个线程来访问。互斥锁是一种特殊的信号量。一个线程不能长时间拥有互斥锁，你的临界段要尽可能短，不然可能把正在等待的线程给坑了。当我们持有一个信号量的时候可以休眠的。当代码运行的时候在等待一个条件时，可以把CPU交出来进入休眠状态，当等待的条件到时再来执行。

(3)自旋锁

自旋锁等待条件时是不会进入休眠状态，会不停在那原地打转。

 

### 5.9.2.3、自旋锁和信号量的使用要点

(1)自旋锁不能递归（拿到了A自旋锁，还想再次拿A自旋锁）

(2)自旋锁可以用在中断上下文（中断上下文不参与CPU调度的）（信号量不可以，因为可能睡眠），但是在中断上下文中获取自旋锁之前要先禁用本地中断。为什么要禁用本地中断?因为怕被嵌套中断，在处理中断处理程序的时候又发生的中断。

(3)自旋锁的核心要求是：拥有自旋锁的代码必须不能睡眠，要一直持有CPU直到释放自旋锁

(4)信号量和读写信号量适合于保持时间较长的情况，它们会导致调用者睡眠，因此只能在进程上下文使用，而自旋锁适合于保持时间非常短的情况，它可以在任何上下文使用。如果被保护的共享资源只在进程上下文访问，使用信号量保护该共享资源非常合适，如果对共享资源的访问时间非常短，自旋锁也可以。但是如果被保护的共享资源需要在中断上下文访问（包括底半部即中断处理句柄和顶半部即软中断），就必须使用自旋锁。自旋锁保持期间是抢占失效的，而信号量和读写信号量保持期间是可以被抢占的。自旋锁只有在内核可抢占或SMP（多处理器）的情况下才真正需要，在单CPU且不可抢占的内核下，自旋锁的所有操作都是空操作。

 

 

## 5.9.4.中断的上下半部1

### 5.9.4.1、中断处理的注意点

(1)中断上下文，不能和用户空间数据交互。主要原因是因为和用户层进行交互的时候有可能会造成休眠。

(2)不能交出CPU（不能休眠、不能schedule（调度函数））

(3)ISR运行时间尽可能短，越长则系统响应特性越差。系统响应特性：当系统发生事件后多长时间才能反映过来。

### 5.9.4.2、中断下半部2种解决方案

​    中断处理程序处理时间要尽可能地短，但是有些中断处理程序无法做到很短，解决方法就是将中断处理程序分为两部分，上半部和下半部。

(1)为什么要分上半部（top half，又叫顶半部）和下半部（bottom half，又叫底半部）。

(2)下半部处理策略1：tasklet（小任务），主要负责登记中断（就是硬件的状态标志位被挂起，我们需要清理这些状态标志位），负责调度下半部。

(3)下半部处理策略2：workqueue（工作队列）真正的干活。

CPU在处理上半部处理完可以不直接去执行下半部的，可以跳转到别的地方去执行更加紧急的任务。

### 5.9.4.3、tasklet使用实战

(1)tasklet相关接口介绍， 像软件中断

(2)实战演示tasklet实现下半部

static struct input_dev *button_dev;

 

//下半部函数

void func(unsigned long data)

{

   int flag;

  printk("button-x210: This is bottom half!\n");

  s3c_gpio_cfgpin(S5PV210_GPH0(2), S3C_GPIO_SFN(0)); //输入模式

  flag = gpio_get_value(S5PV210_GPH0(2));

  s3c_gpio_cfgpin(S5PV210_GPH0(2), S3C_GPIO_SFN(0xf)); //eint_2模式

 

  input_report_key(button_dev, KEY_LEFT, !flag);

  input_sync(button_dev);

}

 

DECLARE_TASKLET(mytasklet, func, 0); //申明一个下半部函数

 

//irq中断号用来确认，voide*dummy用来做共享中断的，有好多个按钮用一个中断号，就是用

//dummy来区分那个按键的。

static irqreturn_t button_interrupt(int irq, void *dummy)

{

  printk("button-x210: This is top half!\n");

  tasklet_schedule(&mytasklet); //调用下半部

  return IRQ_HANDLED;

}

 

 

 

## 5.9.5.中断的上下半部2

### 5.9.5.1、workqueue实战演示

(1)workqueue的突出特点是下半部会交给worker thead，因此下半部处于进程上下文，可以被调度，因此可以睡眠。Workqueue是参与系统调度的。

(2)include/linux/workqueue.h

 

### 5.9.5.2、中断上下半部处理原则

(1)必须立即进行紧急处理的极少量任务放入在中断的顶半部中，此时屏蔽了与自己同类型的中断，由于任务量少，所以可以迅速不受打扰地处理完紧急任务。

(2)需要较少时间的中等数量的急迫任务放在tasklet中。此时不会屏蔽任何中断（包括与自己的顶半部同类型的中断），所以不影响顶半部对紧急事务的处理；同时又不会进行用户进程调度，从而保证了自己急迫任务得以迅速完成。

(3)需要较多时间且并不急迫（允许被操作系统剥夺运行权）的大量任务放在workqueue中。此时操作系统会尽量快速处理完这个任务，但如果任务量太大，期间操作系统也会有机会调度别的用户进程运行，从而保证不会因为这个任务需要运行时间将其它用户进程无法进行。

(4)可能引起睡眠的任务放在workqueue中。因为在workqueue中睡眠是安全的。在需要获得大量的内存时、在需要获取信号量时，在需要执行阻塞式的I/O操作时，用workqueue很合适。

 

 

## 5.9.6_7.linux内核的I2C子系统详解1_2

### 5.9.6.1、I2C总线汇总概览

(1)三根通信线：SCL、SDA、GND

(2)I2C特点：同步、串行、电平、低速、近距离

(3)总线式结构，支持多个设备挂接在同一条总线上

(4)主从式结构，通信双方必须一个为主（master）一个为从（slave），主设备掌握每次通信的主动权，从设备按照主设备的节奏被动响应。每个从设备在总线中有唯一的地址（slave address），主设备通过从地址找到自己要通信的从设备（本质是广播）。

(5)I2C主要用途就是主SoC和外围设备之间的通信，最大优势是可以在总线上扩展多个外围设备的支持。常见的各种物联网传感器芯片（如gsensor、温度、湿度、光强度、酸碱度、烟雾浓度、压力等）均使用I2C接口和主SoC进行连接。

(6)电容触摸屏芯片的多个引脚构成2个接口。一个接口是I2C的，负责和主SoC连接（本身作为从设备），主SoC通过该接口初始化及控制电容触摸屏芯片、芯片通过该接口向SoC汇报触摸事件的信息（触摸坐标等），我们使用电容触摸屏时重点关注的是这个接口；**另一个接口**是电容触摸板的管理接口，电容触摸屏芯片通过该接口来控制触摸板硬件。该接口是电容触摸屏公司关心的，他们的触摸屏芯片内部固件编程要处理这部分，我们使用电容触摸屏的人并不关心这里。

### 5.9.6.2、linux内核的I2C驱动框架总览

(1)I2C驱动框架的主要目标是：让驱动开发者可以在内核中方便的添加自己的I2C设备的驱动程序，从而可以更容易的在linux下驱动自己的I2C接口硬件

(2)源码中I2C相关的驱动均位于：drivers/i2c目录下。linux系统提供2种I2C驱动实现方法：

**第一种叫i2c-dev**：对应drivers/i2c/i2c-dev.c，这种方法只是封装了主机（I2C master，一般是SoC中内置的I2C控制器）的I2C基本操作，并且向应用层提供相应的操作接口，应用层代码需要自己去实现对slave的控制和操作，所以这种I2C驱动相当于只是提供给应用层可以访问slave硬件设备的接口，本身并未对硬件做任何操作，应用需要实现对硬件的操作，因此写应用的人必须对硬件非常了解，其实相当于传统的驱动中干的活儿丢给应用去做了**，所以这种I2C驱动又叫做“应用层驱动”**。

这种方式并不主流，它的优势是把差异化都放在应用中，这样在设备比较难缠（尤其是slave是非标准I2C时）时不用动驱动，而只需要修改应用就可以实现对各种设备的驱动。这种驱动在驱动层很简单（就是i2c-dev.c）我们就不分析了。

**第二种I2C驱动**：是所有的代码都放在驱动层实现，直接向应用层提供最终结果。应用层甚至不需要知道这里面有I2C存在，譬如电容式触摸屏驱动，直接向应用层提供/dev/input/event1的操作接口，应用层编程的人根本不知道event1中涉及到了I2C。这种是我们后续分析的重点。

 

 

## 5.9.8.linux内核的I2C子系统详解3

### 5.9.8.1、I2C子系统的4个关键结构体

(1)struct i2c_adapter          I2C适配器

  这个结构体代表I2C主控设备

(2)struct i2c_algorithm          I2C算法

  这个结构体用来描述主机和从机之间通讯的算法。主机如何通过I2C和从机通信（读写…）就是算法。这个包含在struct i2c_adapter 适配器里面。

(3)struct i2c_client          I2C（从机）设备信息（device）

(4)struct i2c_driver         I2C（从机）设备驱动

### 5.9.8.2、关键文件

(1)/drivers/i2c/i2c-core.c  内核开发者实现的。

(2) /drivers/i2c/busses目录  本身支持的i2c_adapter,

(3) /drivers/i2c/algos目录  一些算法

 

 

## 5.9.9.linux内核的I2C子系统详解4

### 5.9.9.1、i2c-core.c初步分析

(1)smbus代码略过

(2)模块加载和卸载:bus_register(&i2c_bus_type);

### 5.9.9.2、I2C总线的匹配机制

(1)match函数

(2)probe函数

总结：I2C总线上有2条分支：i2c_client链和i2c_driver链，当任何一个driver或者client去注册时，I2C总线都会调用match函数去对client.name和driver.id_table.name进行循环匹配。如果driver.id_table中所有的id都匹配不上则说明client并没有找到一个对应的driver，没了；如果匹配上了则标明client和driver是适用的，那么I2C总线会调用自身的probe函数，自身的probe函数又会调用driver中提供的probe函数，driver中的probe函数会对设备进行硬件初始化和后续工作。

### 5.9.9.3、核心层开放给其他部分的注册接口

(1)i2c_add_adapter/i2c_add_numbered_adapter       注册adapter的

(2)i2c_add_driver                          注册driver的

(3)i2c_new_device                          注册client的

 

 

## 5.9.10.linux内核的I2C子系统详解5

### 5.9.10.1、adapter模块的注册

static struct platform_driver s3c24xx_i2c_driver = {

  .probe   = s3c24xx_i2c_probe,

  .remove   = s3c24xx_i2c_remove,

  .id_table  = s3c24xx_driver_ids, //如果过定义id_table，就不匹配driver.name

  .driver   = {  

​    .owner = THIS_MODULE,

​    .name  = "s3c-i2c",

​    .pm = S3C24XX_DEV_PM_OPS,

  },

};

(1)平台总线方式注册

(2)找到driver和device，并且确认其配对过程

(3)probe函数

### 5.9.10.2、probe函数分析

(1)填充一个i2c_adapter结构体，并且调用接口去注册之

(2)从platform_device接收硬件信息，做必要的处理（request_mem_region & ioremap、request_irq等）。

(3)对硬件做初始化（直接操作210内部I2C控制器的寄存器）。

### 5.9.10.3、i2c_algorithm

/* i2c bus registration info */

 

static const struct i2c_algorithm s3c24xx_i2c_algorithm = {

  .master_xfer    = s3c24xx_i2c_xfer,

  .functionality   = s3c24xx_i2c_func,

};

 

 

(1)i2c->adap.algo  =  &s3c24xx_i2c_algorithm;

(2)functionality

(3)s3c24xx_i2c_doxfer

 

 

## 5.9.11_12.linux内核的I2C子系统详解6_7

### 5.9.11.1、i2c_driver的注册

(1)以gslX680的驱动为例

(2)将驱动添加到内核SI项目中

(3)i2c_driver的基本分析：name和probe


 i2c_adator 结构体中的中断是I2c控制器收到通讯所产生的中断，是SOC内部中断。Struct

i2c_board_info 中的irq是触摸屏产生的中断，是接到210Eint触角中的，当你按下触摸屏的时候，通知i2c控制器触摸屏有反应，要发送坐标过来。

 

### 5.9.11.2、i2c_client从哪里来

(1)直接来源：i2c_register_board_info

smdkc110_machine_init

​    i2c_register_board_info

​    

struct i2c_board_info {

  char    type[I2C_NAME_SIZE];      // 设备名

  unsigned short flags;           // 属性

  unsigned short addr;            // 设备从地址

  void    *platform_data;         // 设备私有数据

  struct dev_archdata *archdata;

\#ifdef CONFIG_OF

  struct device_node *of_node;

\#endif

  int   irq;                // 设备使用的IRQ号，对应CPU的EINT

};

 

 

(2)实现原理分析

内核维护一个链表 __i2c_board_list，这个链表上链接的是I2C总线上挂接的所有硬件设备的信息结构体。也就是说这个链表维护的是一个struct i2c_board_info结构体链表。

真正的需要的struct i2c_client在别的地方由__i2c_board_list链表中的各个节点内容来另外构建生成。

 

函数调用层次：

\kernel_jiuding\drivers\i2c\i2c-core.c

i2c_add_adapter/i2c_add_numbered_adapter

​    i2c_register_adapter

​       i2c_scan_static_board_info

​           i2c_new_device

​              device_register

总结：I2C总线的i2c_client的提供是内核通过i2c_add_adapter/i2c_add_numbered

_adapter接口调用时自动生成的，生成的原料是mach-x210.c中的i2c_register_board_info(1, i2c_devs1, ARRAY_SIZE(i2c_devs1));

 

 

## 5.9.13.gslX680驱动的移植实践

### 5.9.13.1、初步移植实验

(1)源码获取

(2)源码加入内核中

(3)mach文件中添加board_info

(4)编译后内核去启动

### 5.9.13.2、在内核配置中添加CONFIG项

(1)定义一个宏名，譬如CONFIG_TOUCHSCREEN_GSLX680

(2)在代码中使用宏来条件编译

(3)在Makefile中使用宏来条件配置

(4)在Kconfig项目中添加宏的配置项

(5)make menuconfig并选择Y或者N

 

 

### 5.9.14_15.gslX680驱动源码分析1_2

static int __devinit gsl_ts_probe(struct i2c_client *client,

​      const struct i2c_device_id *id)

{

 

 

## 5.9.16.老版本触摸屏的驱动

### 5.9.16.1、ft5x06驱动移植实践

### 5.9.16.2、ft5x06驱动源码分析



 

# 第十章 块设备驱动介绍

## 5.10.0 章节介绍

5.10.1.正确理解块设备驱动的概念

​    本节着重讲块设备驱动和字符设备驱动的差异，并且讲了扇区、块、页等块设备驱动中重要搞的概念。

5.10.2.块设备驱动框架简介

​    本节讲述块设备驱动的整体框架，先打通上下脉络再后面分析的时候就不会迷失。

5.10.3.块设备驱动案例分析1

​    本节开始块设备驱动案例分析，本节主要是进行实践演示，教大家如何编译安装格式化挂载一个块设备，驱动源码是我提供好的。

5.10.4.块设备驱动案例分析2

​    本节开始分析提供的块设备驱动源码，这个驱动来自于LDD3，做了一些移植与修改。

5.10.5.块设备驱动案例分析3

​    本节接着分析提供的块设备驱动源码，这个驱动来自于LDD3，做了一些移植与修改。

 

## 5.10.1.正确理解块设备驱动的概念

### 5.10.1.1、块设备和字符设备的差异

(1)块和字符是两种不同的访问设备的策略

(2)同一个设备可以同时支持块和字符两种访问策略

有些设备可以按照字符设备来访问也可以按块设备访问。例如可以用脚踩的电动车，如果用人来踩就是自行车，如果用电来驱动就是电动车。

(3)设备本身的物理特性决定了哪一种访问策略更适合

 (4)块设备本身驱动层支持缓冲区，而字符设备驱动层没有缓冲

  块设备在驱动层实现了一层缓冲区。因为块设备一次只能按块来读写，如果不实现缓冲区，比如每次只操作一个字节的读写，这样效率就非常低。

(5)块设备驱动最适合存储设备

  硬盘，SD卡，NandFlash。

### 5.10.1.2、块设备驱动的特点

(1)字符设备只能顺序访问（如串口发送数据顺序），而块设备可以随机访问（不连续块访问）。

(2)传统的机械式块设备（如硬盘、DVD）虽然可以随机访问，但是连续访问效率更高，因此块设备驱动中有排序逻辑将用户的随机访问重新调整成尽量连续访问以提升效率。

(3)Nand、SD卡等随机访问效率等同于顺序访问。

### 5.10.1.3、块设备相关的几个单位

(1)扇区（Sector），概念来自于早期磁盘，在硬盘、DVD中还有用，在Nand/SD中已经没意义了，扇区是块设备本身的特性，大小一般为512的整数倍，因为历史原因很多时候都向前兼容定义为512Byte.

(2)块（block），概念来自于文件系统，是内核对文件系统数据处理的基本单位，大小为若干个扇区，常见有512B、1KB、4KB等。他是扇区的整数倍。

(3)段（Section），概念来自于内核，是内核的内存管理中一个页或者部分页，由若干个连续为块组成。

(4)页（Page），概念来自于内核，是内核内存映射管理的基本单位。linux内核的页式内存映射名称来源于此。

总结：块设备驱动对下以Sector为单位管理块设备，对上以Block为单位和文件系统交互。

注意：块设备驱动和字符设备驱动不同，应用层对块设备驱动的访问一般不是直接操作设备文件（/dev/block/xxx，或者/dev/sdax），而是通过文件系统来简洁操作。（思考裸机阶段时刷机烧录SD卡时说过的对SD卡的2种访问：文件系统下访问和扇区级访问）。

 

 

## 5.10.2.块设备驱动框架简介

### 5.10.2.1、块设备驱动框图

(1)VFS

  

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

  虚拟文件系统就是对文件系统的一个抽象，对上可以对接各种文件系统，都会把各种对文件的操作映射到VFS。各种文件系统都是调用VFS的，这样我们的不同的文件系统就可以跟驱动无关了。

(2)通用块层

​    关键是接口

(3)IO调度层（电梯算法）

​    电梯算法有好多种，这种算法是用来解决电梯问题。电梯的向上向下就好像读和写，当前有3个write就先把write处理了，当前有3个read就先把read处理了。

(4)块设备驱动层（真正硬件操作部分）

### 5.10.2.2、重点结构体

(1)struct request             对设备的每一次操作（譬如读或者写一个扇区）

​    应用层要发送一个请求，就有一个结构体

(2)struct request_queue           request队列

​    一个个读写请求组合成一个队列，IO调度处会通过处理来拿出队列里最适合的请求给驱动层。

(3)struct bio                通用块层用bio来管理一个请求

​    如何操作这个完成这个用户请求

(4)struct gendisk            表示一个磁盘设备或一个分区

​    用来表示一个磁盘的，表示一个磁盘的分区。

 

## 5.10.3.块设备驱动案例分析1

### 5.10.3.1、块设备驱动案例演示

(1)驱动简单介绍

(2)编译

(3)模块安装

(4)查看信息       cat /proc/devices  cat /proc/partitions  ls /dev/  lsmod

​      查看安装前和安装后的差异

(5)挂载测试

Ramdisk 内存虚拟出来的一个磁盘。LDD3这本书，这本书太老了。

### 5.10.3.2、块设备驱动简单分析

(1)如何证明块设备驱动真的工作了: 格式化、挂载

  格式化：mkfs.ext2 /dev/my_ramblock

  挂载： mount -t ext2 /dev/my_ramblcok /tmp

(2)注意各种打印信息

(3)体会块设备驱动的整体工作框架

 

 

## 5.10.4_5.块设备驱动案例分析2_3

\#include <linux/module.h>

\#include <linux/slab.h>

\#include <linux/errno.h>

\#include <linux/interrupt.h>

\#include <linux/mm.h>

\#include <linux/fs.h>

\#include <linux/kernel.h>

\#include <linux/timer.h>

\#include <linux/genhd.h>

\#include <linux/hdreg.h>

\#include <linux/ioport.h>

\#include <linux/init.h>

\#include <linux/wait.h>

\#include <linux/blkdev.h>

\#include <linux/blkpg.h>

\#include <linux/delay.h>

\#include <linux/io.h>

\#include <asm/system.h>

\#include <asm/uaccess.h>

\#include <asm/dma.h>

 

\#define RAMBLOCK_SIZE (1024*1024)        // 1MB，2048扇区

 

static struct gendisk *my_ramblock_disk;    // 磁盘设备的结构体

static struct request_queue *my_ramblock_queue; // 等待队列

static DEFINE_SPINLOCK(my_ramblock_lock);

static int major;

static unsigned char *my_ramblock_buf;     // 虚拟块设备的内存指针

 

static void do_my_ramblock_request(struct request_queue *q)

{

 

  struct request *req;

  static int r_cnt = 0;      //实验用，打印出驱动读与写的调度方法

  static int w_cnt = 0;

  

  req = blk_fetch_request(q);

  

  while (NULL != req)

  {

​    unsigned long start = blk_rq_pos(req) *512;  //当前正在操作的扇区*512

​    unsigned long len = blk_rq_cur_bytes(req); //有多少个扇区

 

​    

​    if(rq_data_dir(req) == READ)

​    {

​      // 读请求

​      memcpy(req->buffer, my_ramblock_buf + start, len); //读操作，

​      printk("do_my_ramblock-request read %d times\n", r_cnt++);

​    }

​    else

​    {

​      // 写请求

​      memcpy( my_ramblock_buf+start, req->buffer, len);  //写操作

​      printk("do_my_ramblock request write %d times\n", w_cnt++);

​    }

 

​    if(!__blk_end_request_cur(req, 0)) 

​    {

​      req = blk_fetch_request(q);

​    }

  }

}

 

static int blk_ioctl(struct block_device *dev, fmode_t no, unsigned cmd, unsigned long arg)

{

  return -ENOTTY;

}

 

static int blk_open (struct block_device *dev , fmode_t no)

{

  printk("11111blk mount succeed\n");

  return 0;

}

static int blk_release(struct gendisk *gd , fmode_t no)

{

  printk("11111blk umount succeed\n");

  return 0;

}

 

static const struct block_device_operations my_ramblock_fops =

{

  .owner   = THIS_MODULE,

  .open    = blk_open,

  .release  = blk_release,

  .ioctl   = blk_ioctl,

};

 

static int my_ramblock_init(void)

{

  major = register_blkdev(0, "my_ramblock");

  if (major < 0)

  {

​    printk("fail to regiser my_ramblock\n");

​    return -EBUSY;

  }

  

  // 实例化,分配内存。

  my_ramblock_disk = alloc_disk(1);    //次设备个数 ，表示要分区个数 +1

  

  //分配设置请求队列，提供读写能力

  my_ramblock_queue = blk_init_queue(do_my_ramblock_request, &my_ramblock_lock);

  //设置硬盘属性 

  my_ramblock_disk->major = major;

  my_ramblock_disk->first_minor = 0;

  my_ramblock_disk->fops = &my_ramblock_fops;

  sprintf(my_ramblock_disk->disk_name, "my_ramblcok");    // /dev/name//这是个数组不能指直接赋值，需要借助sprintf

  my_ramblock_disk->queue = my_ramblock_queue;

  set_capacity(my_ramblock_disk, RAMBLOCK_SIZE / 512);//设备的容量

  /* 硬件相关操作 */

  my_ramblock_buf = kzalloc(RAMBLOCK_SIZE, GFP_KERNEL);//GFP_KERNEL表示内核里的分配

  add_disk(my_ramblock_disk);       // 向驱动框架注册一个disk或者一个partation的接口

  

  return 0;

}

 

static void my_ramblock_exit(void)

{

  unregister_blkdev(major, "my_ramblock");

  del_gendisk(my_ramblock_disk);

  put_disk(my_ramblock_disk);

  blk_cleanup_queue(my_ramblock_queue);

  kfree(my_ramblock_buf); 

}

 

module_init(my_ramblock_init);

module_exit(my_ramblock_exit);

 

MODULE_LICENSE("GPL"); 

 

 

### 5.10.4.1、源码分析

(1)register_blkdev(kernel/block/genhd.c)，内核提供的注册块设备驱动的注册接口，在块设备驱动框架中的地位，等同于register_chrdev在字符设备驱动框架中的地位。

(2)blk_init_queue 用来实例化产生一个等待队列，将来应用层对本块设备所做的所有的读写操作，都会生成一个request然后被加到这个等待队列中来。

(3)blk_init_queue函数接收2个参数，第一个是等待队列的回调函数，这个函数是驱动提供的用来处理等待队列中的request的函数（IO调度层通过电梯算法从等待队列中取出一个request，就会调用这个回调函数来处理这个请求），第二个参数是一个自旋锁，这个自旋锁是要求我们驱动提供给等待队列去使用的。

(4)blk_fetch_request函数是IO调度层提供的接口，作用是从request_queue中（按照电梯算法）取出一个（算法认为当前最应该去被执行的一个请求，是被算法排序、合并后的）请求，取出的请求其实就是当前硬件（块设备）最应该去执行的那个读写操作。

 

 

 



 

# 第十一章 网络设备驱动介绍

## 5.10.0章节介绍

5.11.1.网络设备驱动概述

​    本节对网络设备驱动做个概要描述，主要是讲了这种驱动模型和字符设备块设备的区别，最后讲了下本部分的学习方法。

5.11.2.虚拟网卡驱动分析1

​    本节来分析一个简化编写的虚拟网卡的驱动源码，主要目的是对网络设备驱动的框架进行理解和分析。

5.11.3.虚拟网卡驱动分析2

​    本节来分析一个简化编写的虚拟网卡的驱动源码，主要目的是对网络设备驱动的框架进行理解和分析。  

5.11.4.DM9000驱动源码分析1

​    本节来分析DM9000这个真实硬件网卡的驱动源码，并没有详细分析硬件操作，主要是验证网络设备驱动框架，并且将之前课程中学到的一些技巧运用进来。

5.11.5.DM9000驱动源码分析2

​    本节来分析DM9000这个真实硬件网卡的驱动源码，并没有详细分析硬件操作，主要是验证网络设备驱动框架，并且将之前课程中学到的一些技巧运用进来。

## 5.11.1.网络设备驱动概述

### 5.11.1.1、什么是网络设备

(1)网络设备

​    在驱动框架里面，设备也是一个软件，驱动也是一个软件。

(2)物理网卡： 真正的硬件网卡设备

​    

### 5.11.1.2、网络设备接口

(1)/dev下没有设备文件，也不通过/sys下的属性文件访问。直观看来，应用层都是通过一些特殊的命令（如ifconfig、ping等）来访问网卡硬件（调用驱动）的。本质上应用调用驱动的方法可以通过分析ping、ifconfig等命令的实现来得知。实际就是通过：socket、bind、listen、connect、send、recv等API来实现的。

(2)网络设备被抽象成一个能够发送和接收数据包的“网络接口”。

(3)struct net_device来管理所有网络接口。

### 5.11.1.3、学习方法

(1)注意网络设备的访问方法和前两种不同

(2)2个数据结构（net_device和sk_buff）

(3)一个虚拟网卡案例代码分析 + DM9000驱动源码分析

 

 

## 5.11.2.虚拟网卡驱动分析1

### 5.11.2.1、虚拟网卡安装、卸载、打开、关闭、设置IP地址等实践

安装之后执行ifconfig -a 来查看

### 5.11.2.2、代码分析

\#include <linux/module.h>

\#include <linux/sched.h>

\#include <linux/kernel.h> /* printk() */

\#include <linux/slab.h> /* kmalloc() */

\#include <linux/errno.h> /* error codes */

\#include <linux/types.h> /* size_t */

\#include <linux/interrupt.h> /* mark_bh */

\#include <linux/in.h>

\#include <linux/netdevice.h>  /* struct device, and other headers */

\#include <linux/etherdevice.h> /* eth_type_trans */

\#include <linux/ip.h>     /* struct iphdr */

\#include <linux/tcp.h>     /* struct tcphdr */

\#include <linux/skbuff.h>

\#include <linux/if_ether.h>

\#include <linux/in6.h>

\#include <asm/uaccess.h>

\#include <asm/checksum.h>

\#include <linux/platform_device.h>

 

// 如果需要随机MAC地址则定义该宏

\#define MAC_AUTO

 

static struct net_device *astonnet_devs;    

 

//网络设备结构体,作为net_device->priv

struct astonnet_priv {

  struct net_device_stats stats;   //有用的统计信息

  int status;             //网络设备的状态信息，是发完数据包，还是接收到网络数据包

  int rx_packetlen;          //接收到的数据包长度

  u8 *rx_packetdata;         //接收到的数据

  int tx_packetlen;          //发送的数据包长度

  u8 *tx_packetdata;         //发送的数据

  struct sk_buff *skb;        //socket buffer结构体，网络各层之间传送数据都是通过这个结构体来实现的

  spinlock_t lock;          //自旋锁

};

 

//网络接口的打开函数

int astonnet_open(struct net_device *dev)

{

  printk("astonnet_open\n");

  

\#ifndef MAC_AUTO

  int i;

  for (i=0; i<6; i++)

​    dev->dev_addr[i] = 0xaa;

\#else

  random_ether_addr(dev->dev_addr); //随机源地址

\#endif

  netif_start_queue(dev);   //打开传输队列，这样才能进行数据传输

  

  return 0;

}

 

int astonnet_release(struct net_device *dev)

{

  printk("astonnet_release\n");

  //当网络接口关闭的时候，调用stop方法，这个函数表示不能再发送数据

  netif_stop_queue(dev);

  

  return 0;

}

 

//接包函数,我们这不可能执行，因为真实的需要来一个中断程序来调用这个函数

void astonnet_rx(struct net_device *dev, int len, unsigned char *buf)

{  

  struct sk_buff *skb;

  struct astonnet_priv *priv = (struct astonnet_priv *) dev->ml_priv;

  skb = dev_alloc_skb(len+2);//分配一个socket buffer,并且初始化skb->data,skb->tail和skb->head

  if (!skb) {

​    printk("gecnet rx: low on mem - packet dropped\n");

​    priv->stats.rx_dropped++;

​    return;

  }

  skb_reserve(skb, 2); /* align IP on 16B boundary */ 

  memcpy(skb_put(skb, len), buf, len);//skb_put是把数据写入到socket buffer

  /* Write metadata, and then pass to the receive level */

  skb->dev = dev;

  skb->protocol = eth_type_trans(skb, dev);//返回的是协议号

  skb->ip_summed = CHECKSUM_UNNECESSARY; //此处不校验

  priv->stats.rx_packets++;//接收到包的个数＋1

  

  priv->stats.rx_bytes += len;//接收到包的长度

  printk("astonnet rx \n");

  netif_rx(skb);//通知内核已经接收到包，并且封装成socket buffer传到上层

  return;

}

 

//真正的处理的发送数据包

//模拟从一个网络向另一个网络发送数据包

void astonnet_hw_tx(char *buf, int len, struct net_device *dev)

 

{

  struct net_device *dest;//目标设备结构体，net_device存储一个网络接口的重要信息，是网络驱动程序的核心

  struct astonnet_priv *priv;

 

  if (len < sizeof(struct ethhdr) + sizeof(struct iphdr)) 

  {

​    printk("astonnet: Hmm... packet too short (%i octets)\n", len);

​    return;

  }

 

  dest = astonnet_devs;

  priv = (struct astonnet_priv *)dest->ml_priv;    //目标dest中的priv

  priv->rx_packetlen = len;

  priv->rx_packetdata = buf;

 

  printk("astonnet tx \n");

  dev_kfree_skb(priv->skb);

}

 

//发包函数

int astonnet_tx(struct sk_buff *skb, struct net_device *dev)

{

  int len;

  char *data;

  struct astonnet_priv *priv = (struct astonnet_priv *)dev->ml_priv;

 

  if (skb == NULL)

  {

​    printk("net_device %p, skb %p\n", dev, skb);

​    return 0;

  }

 

  len = skb->len < ETH_ZLEN ? ETH_ZLEN : skb->len;//ETH_ZLEN是所发的最小数据包的长度

  data = skb->data;//将要发送的数据包中数据部分

  priv->skb = skb;

  astonnet_hw_tx(data, len, dev);//真正的发送函数

  

  return 0; 

}

 

//设备初始化函数

int astonnet_init(struct net_device *dev)

{

  printk("astoncnet_init\n");

 

  ether_setup(dev);//填充一些以太网中的设备结构体的项

  

  /* keep the default flags, just add NOARP */

  dev->flags      |= IFF_NOARP;

 

//为priv分配内存

  dev->ml_priv = kmalloc(sizeof(struct astonnet_priv), GFP_KERNEL);

  if (dev->ml_priv == NULL)

​    return -ENOMEM;

  memset(dev->ml_priv, 0, sizeof(struct astonnet_priv));

  spin_lock_init(&((struct astonnet_priv *)dev->ml_priv)->lock);

  

  return 0;

}

 

static const struct net_device_ops astonnet_netdev_ops = {

  .ndo_open    = astonnet_open,      // 打开网卡 对应 ifconfig xx up

  .ndo_stop    = astonnet_release,     // 关闭网卡 对应 ifconfig xx down

  .ndo_start_xmit = astonnet_tx,       // 开启数据包传输

  .ndo_init    = astonnet_init,      // 初始化网卡硬件

};

 

static void aston_plat_net_release(struct device *dev)

{

  printk("aston_plat_net_release\n");

}

 

static int __devinit aston_net_probe(struct platform_device *pdev)

{

  int result=0;

 

  astonnet_devs = alloc_etherdev(sizeof(struct net_device));

  astonnet_devs->netdev_ops = &astonnet_netdev_ops;

 

  strcpy(astonnet_devs->name, "astonnet0");

  if ((result = register_netdev(astonnet_devs)))

​    printk("astonnet: error %i registering device \"%s\"\n", result, astonnet_devs->name);

  

  return 0;

}

 

static int __devexit aston_net_remove(struct platform_device *pdev)  //设备移除接口

{

​    kfree(astonnet_devs->ml_priv);

​    unregister_netdev(astonnet_devs);

​    return 0;

}

 

static struct platform_device aston_net= {

  .name  = "aston_net",

  .id   = -1,

  .dev  = {

​    .release = aston_plat_net_release,

  },

};

 

static struct platform_driver aston_net_driver = {

  .probe = aston_net_probe,         

  .remove = __devexit_p(aston_net_remove),  

 

  .driver = {

​    .name ="aston_net",

​    .owner = THIS_MODULE,

  },

};

 

static int __init aston_net_init(void)

{

  printk("aston_net_init \n");

  

  platform_device_register(&aston_net);

  return platform_driver_register(&aston_net_driver );    

}

 

static void __exit aston_net_cleanup(void)

{

  platform_driver_unregister(&aston_net_driver );   

  platform_device_unregister(&aston_net);

}

 

module_init(aston_net_init);

module_exit(aston_net_cleanup);

 

MODULE_LICENSE("GPL");

 

 

 

## 5.11.3.虚拟网卡驱动分析2

 

 

## 5.11.4.DM9000驱动源码分析1

 

## 5.11.5.DM9000驱动源码分析2