

[TOC]

------



# 6.1.项目展示与整体规划

## 6.1.1.关于做项目不得不说的事儿

（1）真实项目与实训项目

​    公司里真正的产品是真实的项目，实训项目就是我们用来练习提升自己功力的项目；

(2)何选择项目课题：噱头还是实质

 课程选择偏实质

（3）如何完美讲解一个项目过程：正统流程or解剖流程

​    正统流程：在所有书上看到的一个流程；

  解剖流程：一边写一边调试，按着解剖流程去做；

## 6.1.2本课程将如何演绎一个项目 

 （1）目的：从程序员的角度出发，经历一个项目从无到有的过程，同时锻炼编程工地和发现并解决问题的能力，提升实战功力。

 （2）做法：实训项目、选题常见、重实质、解剖流程办事。

## 6.1.3.项目展示

(1)项目从哪里来的

(2)项目实现的基本效果浏览

(3)项目规划文档简介

## 6.1.4.应该如何学做项目

(1)要动手，练而不是只听

(2)学写程序的关键：聚沙成塔集腋成裘

(3)做项目的核心关注点：1、用代码实现自己的构想；2、解决过程中遇到的问题

 

 

# 6.2.环境搭建和基础确认

## 6.2.1.开发环境

(1)硬件：PC（主机Win7 X64，虚拟机ubuntu14.04） + 开发板（X210）

(2)软件：linux（直接基于linux API）

## 6.2.2.需要用到的基础环境

(1)开发板uboot（uboot可以在iNand中，也可以在外部SD卡中）

(2)移植好的内核（zImage在tftp服务器中，或者zImage直接fastboot方式烧录到iNand中）

建议使用九鼎提供的移植好的源码包来自己修改、编译后得到zImage。

(3)自己做的rootfs（其实就是第2部分移植课程中制作rootfs时制作的那个）

(4)主机ubuntu中tftp服务器

(5)主机ubuntu中nfs服务器

## 6.2.3.其他小细节

(1)代码编辑器：SourceInsight

(2)代码管理：Makefile

(3)调试流程：Windows共享文件夹编辑、虚拟机ubuntu中编译、make cp到nfs格式的rootfs中在开发板上运行

(4)开发板标准：以V3S（1024*600）为准，V3版本的请自行根据原理进行调整

 

 

# 6.3.开始动手写代码

## 6.3.1.Makefile介绍

(1)这是一个通用的项目管理的Makefile体系，自己写的分子文件夹组织的项目可以直接套用这套Makefile体系

(2)包含三类：顶层Makefile、Makefile.build、子文件夹下面的Makefile

  顶层主要申明一些全局变量，环境变量等。

 

(3)可以参考：http://www.cnblogs.com/lidabo/p/4521123.html

## 6.3.2.SI建立工程

 

 

# 6.4.framebuffer基本操作代码

 

# 6.5.图片显示原理和实践

## 6.5.1.图片显示原理

(1) 概念1：像素

(2) 概念2：点阵

​    有像素点构成的一个点阵，屏幕就是一个点阵。

(3) 分辨率（物理分辨率、显示分辨率）、清晰度和分辨率和像素间距有关；

​    像素间距相同时，分辨率越大越清晰；分辨率相同是，像素间距越小越清晰；

(3) bpp (RGB565 RGB888) 像素深度，每个像素用几个bit来表示。

(4) 颜色序 （RGB、BGR），写程序一定要搞清楚颜色序，不然显示的时候颜色就反了。

(5) 一副图片就是一个数组。

## 6.5.2图片点阵数据获取

（1）Image2LCD 软件提取，用这个软件来讲图片转换成数据。

 (2) 通过图片、视频文件直接代码方式获取

## 6.5.3图片显示编码与实践

# 6.6.图片数据提取和显示

## 6.6.1 Image2LCD提取图片数据

## 6.6.2.图片显示编码与实践

# 6.7图片显示的高级话题

## 6.7.1 RGB顺序调整

（1）一个是imagetolcd中的RGB顺序要和代码中的所设置的RGB顺序要一致（数组左移多少位）。

 *p++ = ((pData[cnt] << 16) | (pData[cnt+1] << 8) | (pData[cnt+2] << 0));

## 6.7.2 显示函数的其他写法

# 6.8 其他显示细节问题

##  6.8.1任意分辨率大小图片显示

​    （1）图片比屏幕分辨率大，这种情况下多出来的部分肯定是没办法显示的。处理方法直接在显示函数中把多余不能显示的部分丢掉。

（2）图片大小比屏幕的大小要小，这种情况下图片知识填充屏幕中的一部分，神域部分仍然保持原来的底色。

​       

 （2）任意起点位置图片显示

 

# 6.9_10 任意起点位置图片显示

## 6.9.1 小图片人一起点(整个图片显示没有超出屏幕范围)

//测试将图片显示到起点（x0,y0）；处

void fb_draw_pic3(unsigned int x0, unsigned int y0)

{  

  unsigned int x, y;

  unsigned int *p =pfb;

  unsigned int cnt, cnt_Data = 0;

  const unsigned char *pData = gImage_pic_dog; //指向图像数组； 

​    

  for(y = y0; y < y0+281 ; y++)

​    for(x= x0; x < x0+500; x++){

​      cnt = y * WIDTH + x;

​      *(p + cnt)= ((pData[cnt_Data] << 16) | (pData[cnt_Data+1] << 8) | \

​      (pData[cnt_Data+2] << 0));

​      cnt_Data += 3; 

​    }

}

 

## 6.9.2 起点导致图片超出屏幕外

//测试将图片显示到起点（x0,y0）；处

void fb_draw_pic3(unsigned int x0, unsigned int y0)

{  

  unsigned int x, y;

  unsigned int *p =pfb;

  unsigned int cnt, cnt_Data = 0;

  const unsigned char *pData = gImage_pic_dog; //指向图像数组； 

​    

for(y = y0; y < y0+281; y++){

​    if(y >= HEIGHT)

​      break;

​    for(x= x0; x < x0+500 ; x++){

​      if(x >= WIDTH)     //不显示超出的部分，让下标不赋值给显示指针

​        cnt_Data += 3; 

​      else{

​        cnt = y * WIDTH + x;

​        *(p + cnt)= ((pData[cnt_Data] << 16) | (pData[cnt_Data+1] << 8) | \

​        (pData[cnt_Data+2] << 0));

​        cnt_Data += 3; 

​      }

​    }

  }

}

 

 

# 6.11_~_15.BMP图片的显示

## 6.9.11.1 图片文件的本质

(1)二进制文件

(2)不同格式的图片文件的差别

(3)BMP（bitMap）图片的基本特征：违背压缩的元素位图格式

## 6.11.2 BMP图片详解

(1)BMP文件如何识别

​    一个完整的图片数据不仅包含组成图片的数据还包含有效信息（图片的大小分辨率这些信息）。BMP图片的特征就是以0x424D开头的。

(2)BMP文件组成

## [BMP图像数据格式详解](https://www.cnblogs.com/l2rf/p/5643352.html)

一．简介

BMP(Bitmap-File)图形文件是Windows采用的图形文件格式，在Windows环境下运行的所有图象处理软件都支持BMP图象文件格式。Windows系统内部各图像绘制操作都是以BMP为基础的。Windows 3.0以前的BMP图文件格式与显示设备有关，因此把这种BMP图象文件格式称为设备相关位图DDB(device-dependent bitmap)文件格式。Windows 3.0以后的BMP图象文件与显示设备无关，因此把这种BMP图象文件格式称为设备无关位图DIB(device-independent bitmap)格式（注：Windows 3.0以后，在系统中仍然存在DDB位图，象BitBlt()这种函数就是基于DDB位图的，只不过如果你想将图像以BMP格式保存到磁盘文件中时，微软极力推荐你以DIB格式保存），目的是为了让Windows能够在任何类型的显示设备上显示所存储的图象。BMP位图文件默认的文件扩展名是BMP或者bmp（有时它也会以.DIB或.RLE作扩展名）。

 

二．BMP格式结构

BMP文件的数据按照从文件头开始的先后顺序分为四个部分：

◆ 位图文件头(bmp file header)： 提供文件的格式、大小等信息

◆ 位图信息头(bitmap information)：提供图像数据的尺寸、位平面数、压缩方式、颜色索引等信息

◆ 调色板(color palette)：可选，如使用索引来表示图像，调色板就是索引与其对应的颜色的映射表

◆ 位图数据(bitmap data)：图像数据区

 

BMP图片文件数据表如下：

 

| 数据段名称                            | 大小（byte）       | 开始地址 | 结束地址 |
| ------------------------------------- | ------------------ | -------- | -------- |
| 位图文件头(bitmap-file header)        | 14                 | 0000h    | 000Dh    |
| 位图信息头(bitmap-information header) | 40                 | 000Eh    | 0035h    |
| 调色板(color table)                   | 由biBitCount决定   | 0036h    | 未知     |
| 图片点阵数据(bitmap data)             | 由图片大小和颜色定 | 未知     | 未知     |

 

 

 

 

三．BMP文件头

BMP文件头结构体定义如下：

typedef struct tagBITMAPFILEHEADER

{ 

UINT16 bfType;    //2Bytes，必须为"BM"，即0x424D 才是Windows位图文件

DWORD bfSize;     //4Bytes，整个BMP文件的大小

UINT16 bfReserved1;  //2Bytes，保留，为0

UINT16 bfReserved2;  //2Bytes，保留，为0

DWORD bfOffBits;   //4Bytes，文件起始位置到图像像素数据的字节偏移量

} BITMAPFILEHEADER;

 

 

 

 

 

 

 

BMP文件头数据表如下：

 

| 变量名      | 地址偏移 | 大小   | 作用说明                                                     |
| ----------- | -------- | ------ | ------------------------------------------------------------ |
| bfType      | 0000h    | 2Bytes | 文件标识符，必须为"BM"，即0x424D 才是Windows位图文件  ‘BM’：Windows  3.1x, 95, NT,…　　‘BA’：OS/2  Bitmap Array　　‘CI’：OS/2 Color  Icon 　  ‘CP’：OS/2 Color  Pointer 　‘IC’：OS/2  Icon 　  ‘PT’：OS/2 Pointer  因为OS/2系统并没有被普及开，所以在编程时，你只需判断第一个标识“BM”就行 |
| bfSize      | 0002h    | 4Bytes | 整个BMP文件的大小（以位B为单位）                             |
| bfReserved1 | 0006h    | 2Bytes | 保留，必须设置为0                                            |
| bfReserved2 | 0008h    | 2Bytes | 保留，必须设置为0                                            |
| bfOffBits   | 000Ah    | 4Bytes | 说明从文件头0000h开始到图像像素数据的字节偏移量（以字节Bytes为单位），以为位图的调色板长度根据位图格式不同而变化，可以用这个偏移量快速从文件中读取图像数据 |

 

 

 

四．BMP信息头

BMP信息头结构体定义如下：

typedef struct _tagBMP_INFOHEADER

{

DWORD  biSize;  //4Bytes，INFOHEADER结构体大小，存在其他版本I NFOHEADER，用作区分

LONG  biWidth;  //4Bytes，图像宽度（以像素为单位）

LONG  biHeight;  //4Bytes，图像高度，+：图像存储顺序为Bottom2Top，-：Top2Bottom

WORD  biPlanes;  //2Bytes，图像数据平面，BMP存储RGB数据，因此总为1

WORD  biBitCount;     //2Bytes，图像像素位数

DWORD biCompression;   //4Bytes，0：不压缩，1：RLE8，2：RLE4

DWORD biSizeImage;    //4Bytes，4字节对齐的图像数据大小

LONG  biXPelsPerMeter;  //4 Bytes，用象素/米表示的水平分辨率

LONG  biYPelsPerMeter;  //4 Bytes，用象素/米表示的垂直分辨率

DWORD biClrUsed;     //4 Bytes，实际使用的调色板索引数，0：使用所有的调色板索引

DWORD biClrImportant;   //4 Bytes，重要的调色板索引数，0：所有的调色板索引都重要

}BMP_INFOHEADER;

 

BMP信息头数据表如下：

 

| 变量名          | 地址偏移 | 大小   | 作用说明                                                     |
| --------------- | -------- | ------ | ------------------------------------------------------------ |
| biSize          | 000Eh    | 4Bytes | BNP信息头即BMP_INFOHEADER结构体所需要的字节数（以字节为单位） |
| biWidth         | 0012h    | 4Bytes | 说明图像的宽度（以像素为单位）                               |
| biHeight        | 0016h    | 4Bytes | 说明图像的高度（以像素为单位）。这个值还有一个用处，指明图像是正向的位图还是倒向的位图，该值是正数说明图像是倒向的即图像存储是由下到上；该值是负数说明图像是倒向的即图像存储是由上到下。大多数BMP位图是倒向的位图，所以此值是正值。 |
| biPlanes        | 001Ah    | 2Bytes | 为目标设备说明位面数，其值总设置为1                          |
| biBitCount      | 001Ch    | 2Bytes | 说明一个像素点占几位（以比特位/像素位单位），其值可为1,4,8,16,24或32 |
| biCompression   | 001Eh    | 4Bytes | 说明图像数据的压缩类型，取值范围为：  0     BI_RGB  不压缩（最常用）  1     BI_RLE8  8比特游程编码（BLE），只用于8位位图  2     BI_RLE4  4比特游程编码（BLE），只用于4位位图  3     BI_BITFIELDS比特域（BLE），只用于16/32位位图  4 |
| biSizeImage     | 0022h    | 4Bytes | 说明图像的大小，以字节为单位。当用BI_RGB格式时，总设置为0    |
| biXPelsPerMeter | 0026h    | 4Bytes | 说明水平分辨率，用像素/米表示，有符号整数                    |
| biYPelsPerMeter | 002Ah    | 4Bytes | 说明垂直分辨率，用像素/米表示，有符号整数                    |
| biClrUsed       | 002Eh    | 4Bytes | 说明位图实际使用的调色板索引数，0：使用所有的调色板索引      |
| biClrImportant  | 0032h    | 4Bytes | 说明对图像显示有重要影响的颜色索引的数目，如果是0，表示都重要。 |

 

 

五．BMP调色板

BMP调色板结构体定义如下：

typedef struct _tagRGBQUAD

{

BYTE  rgbBlue;    //指定蓝色强度

BYTE  rgbGreen;   //指定绿色强度

BYTE  rgbRed;    //指定红色强度

 BYTE  rgbReserved;  //保留，设置为0

} RGBQUAD;

 

1，4，8位图像才会使用调色板数据，16,24,32位图像不需要调色板数据，即调色板最多只需要256项（索引0 - 255）。

颜色表的大小根据所使用的颜色模式而定：2色图像为8字节；16色图像位64字节；256色图像为1024字节。其中，每4字节表示一种颜色，并以B（蓝色）、G（绿色）、R（红色）、alpha（32位位图的透明度值，一般不需要）。即首先4字节表示颜色号1的颜色，接下来表示颜色号2的颜色，依此类推。

颜色表中RGBQUAD结构数据的个数有biBitCount来确定，当biBitCount=1,4,8时，分别有2,16,256个表项。

当biBitCount=1时，为2色图像，BMP位图中有2个数据结构RGBQUAD，一个调色板占用4字节数据，所以2色图像的调色板长度为2*4为8字节。

当biBitCount=4时，为16色图像，BMP位图中有16个数据结构RGBQUAD，一个调色板占用4字节数据，所以16像的调色板长度为16*4为64字节。

当biBitCount=8时，为256色图像，BMP位图中有256个数据结构RGBQUAD，一个调色板占用4字节数据，所以256色图像的调色板长度为256*4为1024字节。

当biBitCount=16，24或32时，没有颜色表。

 

五．BMP图像数据区

位图数据记录了位图的每一个像素值，记录顺序是在扫描行内是从左到右,扫描行之间是从下到上。位图的一个像素值所占的字节数:

当biBitCount=1时，8个像素占1个字节;

当biBitCount=4时，2个像素占1个字节;

当biBitCount=8时，1个像素占1个字节;

当biBitCount=24时,1个像素占3个字节;

Windows规定一个扫描行所占的字节数必须是4的倍数(即以long为单位),不足的以0填充，

一个扫描行所占的字节数计算方法:

DataSizePerLine= (biWidth* biBitCount+31)/8;

// 一个扫描行所占的字节数

DataSizePerLine= DataSizePerLine/4*4; // 字节数必须是4的倍数

位图数据的大小(不压缩情况下):

DataSize= DataSizePerLine* biHeight;

 

颜色表接下来位为位图文件的图像数据区，在此部分记录着每点像素对应的颜色号，其记录方式也随颜色模式而定，既2色图像每点占1位（8位为1字节）；16色图像每点占4位（半字节）；256色图像每点占8位（1字节）；真彩色图像每点占24位（3字节）。所以，整个数据区的大小也会随之变化。究其规律而言，可的出如下计算公式：图像数据信息大小=（图像宽度*图像高度*记录像素的位数）/8。

 

(3)BMP头文件信息

(4)图片有效数据区

第一步： 打开BMP图片

第二步： 判断图片格式是否真是BMP

第三步：解析头星系，得到该BMP图片的详细信息

第四步：根据第三步得到的信息，去合适位置提取真正的有效图片信心

第五步：将得到的有效数据丢到fb中去显示

## 6.14.1BMP头信息结构体注意项

//BMP文件头结构体定义如下：

typedef struct tagBITMAPFILEHEADER

{ 

  //UINT16 bfType;    //2Bytes，必须为"BM"，即0x424D 才是Windows位图文件

  DWORD bfSize;     //4Bytes，整个BMP文件的大小

  UINT16 bfReserved1; //2Bytes，保留，为0

  UINT16 bfReserved2; //2Bytes，保留，为0

  DWORD bfOffBits;   //4Bytes，文件起始位置到图像像素数据的字节偏移量

} BITMAPFILEHEADER;

 

调用的时候我们必须注意结构体对齐的问题，必须按照1字节对齐，但是加了1字节对齐还是不行，现在我们就将第一个bfType取消掉来解决。因为fd在is_bmp（）函数当中已经read了2字节，所以在bmp_analyze（）函数中就不用跳过2个字节了。

 

 

 

# 6.16_~_18 及时规整才能写出好项目

## 6.16.1 再次强调规范问题

​    (1) 函数、变量起名字好合法合理

(2) 要写注释

(3) 函数长短要适合

(4) 多文件长短要合适

## 6.16.2 为什要规整项目

​    (1) 完全自由写项目是不可能一步到位，只能先重功能和内容，后不调理和规范；

(2) 规整的过程也是一个梳理逻辑和分析加过的过程；

## 6.16.3 一些重构代码的技巧

​    （1）不用的代码先不要删掉，把他注释掉；

（2）用 #if 0    #endif 来屏幕不需要的代码，不要用/* */

 

## 6.16.4 对本项目进行规整

(1)添加DEBUG宏以控制调试信息输出

\#undef DEBUG

 

\#ifdef DEBUG

  \#define debug(...) {fprintf(stderr, "debug(%s, %s(), %d): ", \

​              __FILE__, __FUNCTION__, __LINE__);\

​          fprintf(stderr, __VA_ARGS__);}

\#else

  \#define debug(...) 

\#endif

Debug宏添加好后，要是能输出可以有2中方式：

第一种： 就是在debug洪涤因之前定义DEBUG宏

第二种： 在编译参数中添加-DDEBUG编译选项

(2)图片信息用结构体封装传递

typedef struct pic_info

{

  char* pathname; //图片的路径加名字

  LONG  biWidth; 

  LONG  biHeight; 

  UINT16  biBitCount;

  unsigned char * pData; //图片像素数据  

}pic_info;

 

 

# 6.19_20.jpg 图片的显示原理分析

## 6.19.1认识jpg图片

  （1）有固定的识别特征

（2）jpg图片是经过压缩的图片格式

## 6.19.2 jpg图片如何显示

（1）jpg图片中的二进制数并不对应像素数据

（2）LCD显示器的街头仍然是framebuffer

（3）要显示jpg图片必须先解码（解压缩）jpg得到相应的位图数据

## 6.19.3 如何解码jpg图片

(1) 图片编码和解码对应着压缩和解压缩

(2) 编码和解码其实就是一些数学运算(压缩度、算法复杂度、时间、清晰度)

(3) 软件编码解码和硬件编解码

(4) 不同的图片格式其实就是编解码的算法不同，结果是图片特征不同

![img](file:///C:/Users/ADMINI~1/AppData/Local/Temp/msohtmlclip1/01/clip_image002.jpg)

  Jpg网速很慢的时候是先一段一段刷新出来的，每一段都是和原图一样清晰，而png是先刷新处一个模糊的轮廓然后再填充细节。

(5) 编程实战：使用开源解码库

 

# 6.21. libjpeg介绍及开源库的使用方法

## 6.21.1 libjpeg介绍

  (1)基于linux的开源软件

  (2)c语言编写(gcc、Makefile管理)

  (3) 提供JPEG图片的编解码算法实现

## 6.21.2 libjpeg版本及下载资源

  (1) 经典版本v6b：

https://sourceforge.net/projects/libjpeg/files/libjpeg/6b/jpegsrc.v6b.tar.gz/download

  (2) 最新版本v9b：www.ijg.org

## 6.21.3 开源库的使用方法

  (1) 移植（源码下载、解压、配置、修改Makefile、编译或交叉编译）。移植的目的是由源码得到三个东西：动态链接库.so、静态链接库.a，头文件.h

(2) 部署（部署动态so、部署静态库）动态链接库.so、静态链接库.a，头文件.h 这三个东西放在该放的地方。

动态库是运行时环境需要，编译时不需要；

静态库是静态连接时需要，动态链接是不需要；

头文件.h是在编译程序时使用的，运行时不需要;

总结：静态库和头文件这两个东西，是在编译链接过程中需要的；而动态库是运行时需要的。

所以动态库so文件是要放到开发板文件系统中去的（放的过程就叫部署），把静态库.a文件和头文件.h放到ubuntu文件系统去。

（3） 注意三个编译选项：-I -l -L

**-I** **是编译选项（准确的说是预处理选项,CFLAGS或者是CPPFLAGS），用来指定处理室查找头文件的范围的。**

**-l** **是链接选项（LDFLAGS）,用来指定链接额外的库（比如我们用到了数学函数，就用-lm，连接器就会去链接libm.so; 那么我们使用了libjpeg，对应的库名就叫libjpeg.so，就需要用-ljpeg选项链接）**

**-L** **是连接选项（LDFLAGS中指定），用来告诉连接器到哪个路径下面去找动态链接库。**

**总结：-l是告诉连接器要链接的动态库的名字，而-L是告诉连接器库的路径。**

 

 

# 6.22_23 libjpeg的移植实战

第一部分: libjpeg& libpng&lbgi库的移植
 1、 libjpeg库移植
 2、下载源码(htt://www.photopost.com/pegsrc.v6b.tar.gz)与准备交叉工具链
 3、解压源码并进入解压后的目录。
 4、配置./configure --prefix=/root/libgpeg/jpegdecode --exec-prefix=/root/libgpeg/jpegdecode --enable-shared --enable-static -build=i386 -host=arm
 5、修改 Makefile
 CC=BCC
 改为CC= arm-linux-gCC
 AR=arc改为AR= arm-linux-ar rc
 AR2=anib改为AR2= arm-linux-ranlib
 6、确认是否存在/root/libgpeg/jpegdecode/include与/root/libgpeg/jpegdecode/bin两个目录,若不存在则创建。

16, make & make install-lib

安装完成后,可以在/opt/ decode/ include目录中找到 libjpeg的头文件,
 以后在编译包含了 libipeg的工程时,必须指定1/opt/ libdecode/ include,
 同时在链接时需指定L/opt/ libdecode/ib,若为动态链接,则必须将 

 

## ubuntu中的libtool问题

时总是报错，libtool的原因

解决方法

sudo apt-get install aptitude 

sudo aptitude install libtool 

 

 

## make: ./libtool：command not found

 

./libtool --mode=compile gcc -O2 -I. -c ./jcapimin.c

 

make: ./libtool：命令未找到

make: *** [jcapimin.lo] 错误 127

./libtool --mode=compile gcc -O2 -I. -c ./cjpeg.c

make: ./libtool：命令未找到

make: *** [cjpeg.lo] 错误 127

 

问题原因:没有安装 libtool 

 

解决办法:首先看有没有安装libtool 及 libtool-ltdl-devel 

 

rpm -qa | grep libtool  

 

下面安装libtool：

 

libtool见附件或者我的资源http://download.csdn.net/detail/qq361294382/9444925

 

 

./configure

make

make install 

 

\#cd jpeg-6b

\#cp /usr/share/libtool/config/config.sub .

\#cp /usr/share/libtool/config/config.guess . 

把 libtool里面的两个配置文件拿来覆盖掉jpeg-6b目录下的对应文件

make clean 再重新configure（切记必须重新configure,否则仍提示这个错误）

没有权限的时候先建立对应的文件夹，再次make install就行了

然后进入jpeg-6b的源码目录，然后执行以下步骤，切记！COPY到当前目录注意后面的点(.)

网上好多都把config.sub和config.guess的路径弄错了，应该是在/usr/share/libtool/config/下，而不是在

/usr/share/libtool/下

————————————————

版权声明：本文为CSDN博主「admithhq」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。

原文链接：https://blog.csdn.net/qq361294382/article/details/50749932

 

 

# 6.24~28.使用libjpeg解码显示jpg图片

## 6.24~28.1.如何使用新的库

​    (1)网络上找别人使用过后写的一些文档、博客等作为参考

​    (2)看库源码自带的文档（说明文档和示例代码）

## 6. 24~28.2 libjpeg说明文档和示例代码

​    (1) 说明文档：REDEME 和libjpeg.doc

​    (2) 示例代码：example.c

## 6. 24~28.3 结合说明文档和示例代码实践

## 6.27.4 完成example.c中代码到项目中的移植

​    （1）测试代码，先试图读取jpg图片的头信息

 

## 6.28.5 动态库的部署

​    (1) 第一种情况：放到/lib或者/user/lib下，这样不需要给系统指定库路径，就能自动找到。强调一下是开发板的根文件系统的路径。

(2) 第二种情况：有时候对于一些不常用的库，我们不愿意将它放到lib或sur/lib目录下去，而是找个自定义的第三方目录，单独放置这些不常用的库，然后用环境变量告诉操作系统这个路径下加载这个库。将该自定义第三方目录导出到环境连梁LD_LIBRARY_PATH下即可。

示例： EXPORT LD_LIBRARY_PATH=/（你的库的路径）:$LD_LIBARARY_PATH

 

# 6.29 解决显示不正确错误

问题的分析及解决记录：

(1)更具LCD上错误的显示状态，分析有可能是显示函数的宽高有误，在fb_draw添加了一个函数打印发没有出错。

(2)怀疑解析出来的数据出错，打印数据发现都是0，返现数据真的是错的。初步怀疑解码出错，也有可以是拿出来的时候出错的。调试发现在

 jpeg_read_scanlines(&cinfo, buffer, 1);

这里读出来就是0，那么这里就出错了。

(3) 有这么几种肯能：

​      1.lib库本身就有错 

​    2 部署有问题

​      3 写的代码由错误  （极有可能）

​     发现就是代码有错，buffer 申请有问题；

   (4) 无法解决，走投无路了怎么办？

​       1 去网络找相关的文章看看别人是怎么解决的；

​       2 去看libjpeg源码中查看；

 

# 6.32 解码显示png图片

## 6.32.1 思路分析

​    (1)png更像jpg而不像是bmp

​    (2)png和jpg都是压缩格式的图片，都是二进制文件，不同之处是压缩和解压缩的伏安法不同。

​    (3)通过libjpeg来编码jpg图片，那么同样有一个libpng用来编解码png图片。

(4)工作思路和顺序：找到并一直部署libpng，然后查readme和其他文档示例代码等来使用libpng提供的API对png图片进行解码，并将解码出来的数据丢到framebuffer中去显示。 

## 6.32.2 zlib库的移植

1、源码下载,这里下载了最新版1.2.8,网址为:http//www.zlib.net

2、解压并进入目录# tar -xvf zlib-128argz

3、导出CC以确定配置时为 arm-linux,# export CC=arm-linux-gcc

配置lib库,得到 Makefile, ./ configure -shared --prefix=/root/libgpeg/jpegdecode/

4 make & make install

指定库安装目录为/opt/ libdecode,交叉编译后得到的tib共享库文件放到了/opt/ /libdecode/ 中,头文件放到了/root/libgpeg           /jpegdecode/include中,man手册放到了/root/libgpeg/jpegdecode//share/man 下

## 6.32.3libpng移植

​    (1)libpng库依赖于zlib库。

​    (2)下载libpng库；

​    (3)解压、配置、修改Makefile, 编译，部署；

​     1、ipng源码下载,目前最新的是166,下载地址为

ftp: //ftp. simplesystems. org/pub/libpng/png/src/libpng16/



2、解压并进入目录,# tar-xvf libpng-16.6 tar. gz

3、配置

export LDFLAGS="-L/root/libgpeg/jpegdecode/lib"

export CFLAGS="-I/root/libgpeg/jpegdecode/include"

export CPPFLAGS="-I/root/libgpeg/jpegdecode/include"

./configure --host=arm-linux --enable-shared --enable-static --prefix=/root/libgpeg/jpegdecode/

4.make & make install

如果在进行配置前并未按照2中所述安装zib,则配置运行了一部分后,会报错:

configure: error: Zlib not installed

 

## 6.33.4 解码显示png图片

###     6.33.1 参考源码包自带资料

(1)REEDME

(2) libpng-manual.txt

(3) example.c和 pngtest.c

# 6.37 图片文件的管理和检索

## 6.37.1 图片文件的管理

(1)在物理磁盘存储层次上：用一个文件夹来管理；在程序中，用数据结构来管理

(2)用数组来管理

(3)用链表管理

## 6.37.2 图片信息的自动检索

(1)读取文件类型

(2)普通文件和文件夹的分类处理

(3)普通文件区分，将其中的图片按格式存储到图片管理数组/链表中

 

# 6.40 添加触摸翻页功能

## 6.40.1 读取触摸坐标数据

## 6.40.2 使用触摸坐标判断并判断执行翻页操作

 

# 6.41 回顾与总结

## 6.41.2 项目总结

(1)项目描述

​    1.软件平台是什么

\2. 项目主要完成了什么功能

​       基于linuxAPI直接操作ramebuff完成了 png jpg pwn

​       图片的管理和检索

​       使用触摸屏用来翻页

​    3.项目过程当中完成了什么问题？

​       Libgpeg 解码不对， 

​       -I -l -L 的编译链接

​       相关库文件的部署

​       Opendir 的局限性

(2)重点和难点

   第三方库的移植和部署，库的使用，如何对图片文件的检索

## 6.41.2 项目总结

(1)划屏翻页  图片放大与缩小  动画  开机动画 背景音乐；