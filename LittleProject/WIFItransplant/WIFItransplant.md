

# 项目积木1：USB WIFI网卡在X210上的移植和使用最全攻略

## 0.项目概要

1.**课程背景介绍

  本节介绍本项目课程的背景及后续课程规划。

**2.****本课程将带来哪些干货**

  本节介绍本课程的关键技术点及任务规划，看了本节就知道学习本课程能学到什么。

**3.****项目各项材料的准备和确认**

  本节讲解驱动移植前的准备工作，主要是环境的搭建和软硬件的信息确认。

**4.****驱动源码修改及编译**

 本节进行驱动源码的修改和编译，属于操作性课程，要跟着做。

**5.USB WIFI****网卡驱动源码简单分析**

  本节对源码做简单浏览和分析，目的是进一步清楚移植时需要修改的源码部分及其原理。

**6.WIFI****网卡的配置过程1**

  本节进行网卡的配置，主要讲解了iwconfig工具集及其使用方法、路由器的网络设想选项包括加密模式、加密算法等。

**7.WIFI****网卡的配置过程2**

  本节讲解并演示wpa_supplicant的配置过程及手动分配IP、连接路由器、ping通网关、设置dns并最终能够访问外网域名。

**8.WIFI****网卡的配置过程3**

  本节有2个技术点，一个是使用dhcp方式获取本地IP，另一个是使用interfaces文件来配置网卡信息。

**9.****在自己定制的rootfs****中移植网卡**

  本节演示如何在自己定制的最小rootfs中移植usb wifi驱动并做联网测试。

**10.****移植wpa_supplicant****并制作镜像1**

  本节讲解wpa_supplicant的移植和交叉编译，因为wpa_supplicant需要用到openssl而且版本有要求，所以移植过程需要注意很多细节。

**11.****移植wpa_supplicant****并制作镜像2**

  本节使用移植的wpa_supplicant进行联网，并且制作镜像，从镜像启动后进行各种配置使之最终能够ping通外网域名，课程完成。

 

## 1.   课程背景介绍

### 1.1、嵌入式课程的整体规划

(1)系统学习课程：《嵌入式linux核心课程》  已完结

(2)项目实训课程：《嵌入式项目库》      正当下

### 1.2、嵌入式项目库中2类项目

(1)大项目

(2)项目积木

### 1.3、本课程背景

(1)WIFI介绍

(2)IBM watson课程引起的开发板无线上网需求

(3)很多同学毕设、项目、竞赛等需求

### 1.4、未来展望

(1)项目积木：X210上QT库的移植和开发环境搭建

(2)大项目：基于海思HI3518E方案的网络摄像机无限扩展项目集

 

  

## 2.本课程将带来哪些干货

### 2.1、MT7601驱动的移植

(1)开发环境搭建和确认

(2)源码获取和解压

(3)配置、编译、安装

### 2.2、无线网卡的配置和使用

(1)常用无线网卡工具介绍：iwconfig、iwlist、iwpriv、wpa_supplicant(android used)

(2)常见WIFI加密格式介绍和路由器端查看：WEP、WPA

(3)wpa_supplicant使用详解

(4)配置本地IP地址、网关、dns等并确认外网连接

### 2.3、在自己定制的rootfs中移植USB WIFI

(1)自己定制rootfs

(2)移植wifi驱动并安装

(3)移植iwconfig工具集（LWE）

(4)移植wpa_supplicant

### 2.4、在自己定制的rootf中添加dhcp支持

(1)在内核和busybox配置中添加dhcp支持

(2)移植dhcpc和dhcpd并使用

 

 

## 3.项目各项材料的准备和确认

### 3.1、开发环境搭建和确认

(1)ubuntu14.04 

(2)内核源码树

(3)开发板提供的官方linux+QT4.8镜像

(4)nfs服务器和文件夹形式的rootfs

mount -t nfs -o nolock 192.168.1.141:/root/rootfs /opt

### 3.2、网卡驱动源码确认

(1)DPO_MT7601U_LinuxSTA_3.0.0.4_20130913.tar.bz2

### 3.3、USB WIFI网卡硬件确认

(1)网卡基本信息介绍

(2)lsusb查看网卡的VID和PID

Bus 001 Device 003: ID 148f:7601

148f VID 厂商ID ; 

  7601 PID 产品ID;

  驱动会比较VID和PID判断这个wifi芯片是否和自己兼容; 

 

## 4.驱动源码修改及编译

### 4.1、确认USB的VID和PID 

(1)源码包中rtusb_dev_id.c文件

USB_DEVICE_ID rtusb_dev_id[] = {

\#ifdef RT6570

  {USB_DEVICE(0x148f,0x6570)}, /* Ralink 6570 */

\#endif /* RT6570 */

  {USB_DEVICE(0x148f, 0x7650)}, /* MT7650 */

\#ifdef MT7601U

  {USB_DEVICE(0x148f,0x6370)}, /* Ralink 6370 */

  {USB_DEVICE(0x148f,0x7601)}, /* MT 6370 */ // 我们的模块就是这个

\#endif /* MT7601U */

  { }/* Terminating entry */

};

### 4.2、修改Makefile

(1)平台换成：三星

PLATFORM = SMDK

(2)内核源码树路径设置

LINUX_SRC = //linux内核源码树

(3)交叉工具链路径设置

CROSS_COMPILE = //交叉编译环境

### 4.3、修改网卡名字(可选)

(1)常用无线网卡名称：rax、wlanx

(2)修改include/rtmp_def.h文件

\#define INF_MAIN_DEV_NAME "ra"

\#define INF_MBSSID_DEV_NAME "ra"

### 4.4、添加wpa_supplicant支持

要使用wpa_supplicant工具就要确保config.mk文件中WPA_SUPPLICANT=y

### 4.5、编译生成驱动模块

(1)清理&编译

make clean && make -j2 

生成os/linux/mt7601Usta.ko就是驱动模块

 

 

## 5.USB WIFI网卡驱动源码简单分析

### 5.1、关键点1：把握深度适可而止

一、**GCC****编译**器中使用：
     -D macro=string，等价于在头文件中定义：#define  macro  string。

　　　　例如：-D TRUE=true，等价于：#define  TRUE  true
     -D macro，等价于在头文件中定义：#define  macro  1，实际上也达到了定义：#define  macro的目的。

　　　　例如：-D [Linux](http://lib.csdn.net/base/linux)，等价于：#define  LINUX  1（与#define  LINUX作用类似）。
     --define-macro  macro=string与-D macro=string作用相同。

我们在头文件和.c文件中都没有找到MT7601U的宏定义,原因是在os/linux/Makefile中WFLAGS +=-DMT7601

## 6.WIFI网卡的配置过程1

### 6.1、iwconfig工具集的介绍和使用演示

(1)linux下专用来配置无线网络的一些命令集

(2)因为iwconfig本身有一定限制，只支持一定的加密格式，在我们这里不用

(3)这些工具集算比较老的

(4)iwlist  scanning,可以看到有哪些可连接的无线网络;

### 6.2、路由器端基础知识

(1)路由器的WAN、LAN(ap)

(2)无线参数：SSID、频段、模式

(3)安全类型：WEP和WPA、WPA2 

(4)安全选项

(5)加密算法

(6)PSK密码

 

### 6.3启动网卡出错解决方法: ifconfig: SIOCSIFFLAGS: Operation not permitted

\# insmod mt7601Usta.ko 

rtusb init rt2870 --->
 usbcore: registered new interface driver rt2870

 

\# iwconfig
 lo     no wireless extensions.
 
 eth0    no wireless extensions.
 
 ra0    Ralink STA  ESSID:"11n-AP" Nickname:"MT7601STA"
      Mode:Auto  Frequency=2.412 GHz Access Point: Not-Associated  
      Bit Rate:1 Mb/s  
      RTS thr:off  Fragment thr:off
      Encryption key:off
      Link Quality=10/100  Signal level:0 dBm Noise level:0 dBm
      Rx invalid nwid:0  Rx invalid crypt:0 Rx invalid frag:0
      Tx excessive retries:0  Invalid misc:0  Missed beacon:0

 

\# ifconfig ra0 up
 unlink cmd rsp urb
 ifconfig: SIOCSIFFLAGS: Operation not permitted

 

原因：/etc/Wireless/RT2870STA/RT2870STA.dat 该文件找不到导致的。

 

解决：将驱动对应的dat文件拷贝对应目录，就没问题了

$cp RT2870STA.dat  /etc/Wireless/RT2870STA/RT2870STA.dat

 

 

 

## 7.WIFI网卡的配置过程2

### 7.1、wpa_supplicant配置文件

(1)wpa_supplicant简介

(2)/etc/Wireless/RT2870STA/RT2870STA.dat //网卡配置文件

(3)/etc/wpa_supplicant.conf       //wpa_supplicant.conf工具配置文件

 

 

// /etc/wpa_supplicant.conf文件中修改为

ctrl_interface=/var/run/wpa_supplicant

network={

 key_mgmt=WPA-PSK         //路由器的加密方式

 ssid="2lou"            //wifi的名字

 psk="15950156"          //wifi的密码

}

 

删除: iap_scan=1 这一句

### 7.2、网卡配置命令序列

insmod mt7601Usta.ko      //安装驱动程序

ifconfig ra0 up         //开启无线网卡

wpa_supplicant -B -c /etc/wpa_supplicant.conf -i ra0  //连接无线网络

wpa_cli -i ra0 status      //查看连接状态

ifconfig ra0 192.168.1.200  //手动配置ip，同一网段

route add default gw 192.168.31.1 dev ra0       //配置网关

ping 192.168.1.1      //ping 网关

ping 8.8.8.8        //ping 外网

 

vi /etc/resolv.conf      //配置dns

nameserver 192.168.1.1 

ping www.baidu.com  

 

**注意：**

1、ra0的配置和上网前，一定要先关掉eth0（ifconfig eth0 down），否则你ping或者设置等等都是默认使用的eth0而不是ra0.

2、当我们wifi网卡连接上路由器后，想要ping通路由器（网关），必须本地wifi网卡有一个和网关同一网段的ip地址才可以。这个本地的ip地址可以dhcp分配，也可以手工配置一个。

3、本地有了ip地址，并且wifi网卡通过wpa_supplicant配置连上路由器后，就能ping通网关了。但是这时还不能ping通外网，因为本地还没有添加网关配置。添加网关配置有2种方法：通过route命令动态添加，或者通过/etc/network/interfaces文件添加。

4、添加网关后就能ping通外网IP了，但是还ping不通www.baidu.com等域名，因为还没有dns。

 

 

## 8.WIFI网卡的配置过程3

### 8.1、使用interfaces文件静态配置

(1)

insmod mt7601Usta.ko      //安装驱动程序

ifconfig ra0 up         //开启无线网卡

wpa_supplicant -B -c /etc/wpa_supplicant.conf -i ra0  //连接无线网络

wpa_cli -i ra0 status      //查看连接状态

(2)interface文件

 

第一步，编辑好interface文件

第二步，使interface文件生效（重启网卡），方法是先ifdown ra0然后ifup ra0

总结：使用/etc/network/interfaces文件其实就是替代了手工配置时的ifconfig分配本地ip地址，和route添加网关这两步。

### 8.2、使用dhcp动态分配IP

(1)原理：路由器中有个dhcp服务器，本地有dhcp客户端, 

(2)前导步骤

insmod mt7601Usta.ko      //安装驱动程序

ifconfig ra0 up         //开启无线网卡

wpa_supplicant -B -c /etc/wpa_supplicant.conf -i ra0  //连接无线网络

wpa_cli -i ra0 status     //查看连接状态

(3)当前状态就是：wifi网卡已经启动并且连接上路由器了，但是本地没有ip地址，所以没法ping通路由器。怎么办？使用dhcp分配一个本地ip。

方法1：命令行使用udhcpc命令来分配

   udhcpc -i ra0

方法2：使用interface文件选择dhcp模式然后重启网卡

auto ra0

iface ra0 inet static

address 192.168.31.94

netmask 255.255.255.0

gateway 192.168.31.1

 

修改为:

auto ra0

iface ra0 inet dhcp

 

### 8.3、让开发板开机自动连上路由器上网

改/etc/init.d/rcS及其相关文件 ---- 

  自己创建一个S4* 开头的启动脚本  如 S41wifi

  注意:S是大写的;

  

 

 

**在配置脚本中添加:**

insmod /home/mt7601/mt7601Usta.ko

ifconfig ra0 up

wpa_supplicant -B -c /etc/wpa_supplicant.conf -i ra0

ifdown ra0

ifup ra0

 

## 9.在自己定制的rootfs中移植网卡

### 9.1、确认自己制作的rootfs并启动

(1)busybox交叉编译

(2)启动后直接nfs方式挂载文件夹形式的rootfs，测试ok后再做成镜像烧录

(3)挂载参数bootargs：setenv bootargs root=/dev/nfs nfsroot=192.168.1.141:/root/rootfs ip=192.168.1.10:192.168.1.141:192.168.1.1:255.255.255.0::eth0:off init=/linuxrc console=ttySAC2,115200 

 

set bootcmd 'tftp 30008000 zImage; bootm 30008000'

### 9.2、需要的工具集确认

(1)iwconfig工具集：另外移植的，不是busybox中的

(2)dhcp工具集：是busybox中集成的，确认busybox的menuconfig中配置支持了这个

(3)wpa_supplicant工具集：另外移植的，不是busybox中的

### 9.3、交叉编译iwconfig

(1)源码下载

  百度搜索:Wireless tools

(2)配置

Makefile 文件中修改 编译器gcc ar 等为arm-linux-gcc 的交叉编译工具链

添加PREFIX = /root/rootfs ,将所需要的工具和动态链接库安装到vfs文件底下去;

(3)交叉编译

(4)部署安装

(5)测试

 

 

## 10_11.移植wpa_supplicant并制作镜像1_2

### 10.1、交叉编译wpa_supplicant

(1)下载wpa_supplicant源码并配置编译。参考http://blog.csdn.net/hktkfly6/article/details/48949863

(2)下载配套版本的openssl并配置编译

(3)去掉配置NL相关的选项省去移植libnl。参考：http://www.cnblogs.com/helloworldtoyou/p/6145995.html

### 10.2、在nfs中测试wpa_supplicant使用

配置 wpa_supplicant -B -c /etc/wpa_supplicant.conf -i ra0 语句的时候出错：/var/run/wpa_suplicant.conf no such a director

**解决方法：**

在/var 底下创建 run 文件夹

Chmod -R 777 run 修改权限即可

 

### 10.3、制作ext2镜像并刷机测试

附：/etc/wpa_supplicant.conf文件内容：

ctrl_interface=/var/run/wpa_supplicant

 

network={

​    key_mgmt=WPA-PSK

​    ssid="zhulaoshi"

​    psk="www.zhulaoshi.org"

}

 

 

 

### 10.4 文件下载

Wpa_supplicant下载

http://w1.fi/wpa_supplicant/

Openssl1.0.1下载

https://www.openssl.org/source/old/1.0.1/

 

### 10.5 问题总结

   进入到Wpa_supplicant 底下执行sudo cp defconfig .config 

修改Wpa_supplicant 中的makefile cc 为arm-linux-gcc;**注意：需要将#ifndef CC   #endif** **去掉，不然默认是用个主机的gcc**

保存后编译出错：需要openssl库

下载之后需要安装opnssl库,通过阅读README文件可以看到wpa_supplicant2.6所使用的openssl库版号为1.0.1。

编译openssl库文件时，将相关的安装目录路径变量INSTALLTOP  OPENSSLDIR改为你自己创建的目录下；将cc ar ranlib nm 变量改为交叉编译工具链arm-linux-;

修改完编译完成后安装，安装出错，发现man手册编译失败，我们将Makefile中安装目标中的与man手册相关的install_docs依赖去掉 。

 

在gcc后面添加 -L/（openssl安装的路径）/lib

在CFLAGS 后面添加 -I/（openssl安装的路径）/include

添加LIBS = -L/（openssl安装的路径）/lib

 

保存编译发现出错，将./config中的 CONFIG_DRIVER_NL80211=y 这样注释掉；

编译成功；

[LUXSAHRE WORK](https://to-do.microsoft.com/sharing?InvitationToken=i00F8M9RxBE9cJ1FPln0PAGijx0KORPyEzJfojxpOSGRKhC68e4VCCp9t1PeG1Unw)