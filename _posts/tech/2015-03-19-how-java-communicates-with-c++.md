---
layout: post
title: Java 调用动态链接库Dll
category: 技术
tags: [java, C++, JNI, DLL]
keywords: JNI,Conflict,DLL,Java,C++
description: 
---

最近接触项目中遇到一个需求，即java需要和C++进行通信，借用JNI（Java Native Interface）技术调用其生成的动态链接库DLL。整个过程遇到不少问题，虽然在解决的过程中花费了不少精力去解决问题，但是现在感觉蛮有意义的！我把整个实现这个需求过程中遇到的问题以及要注意的实现按照以需求实现的过程，展示给有同样需求的辛苦奋斗于一线的通道门，希望可以避免很多弯路，加快项目开发进程！
	
---


##Java如何"借势"？


		  本文由icmzn精心整理，转载请注明出处(http://www.icmzn.com)！

JNI是Java本地调用接口，所谓的native就是调用C/C++的程序。由于操作系统提供的驱动一般都是C接口，Java语言并不具备操作这些驱动的能力。对于计算量比较大，处理数据比较多的模块，Java的效率没有C高，所以希望用C去完成。对于理解JNI的调用实现，对于理解Android的源码有帮助，因为Android系统中大量的使用了JNI。

本流程只介绍java调用C/C++单项调用，因项目只有这方面的需求哈！反向的调用没有涉及 ：）

本机环境Win7（64bit）,JRE(64bit)


##按照JNI调用流程


	1. 项目双方首先要商定好Java与C/C++交互的接口规范
	2. 用Java写好包含native接口的Java类（HelloWorld）的java代码
	3. 编译生成class
	4. 产生*.h的头文件
	5. 用C/C++实现C头文件的方法实体
	6. 讲.h和.cpp实体文件编译产生可以被Java调用DLL文件
	7. 运行.class文件，执行调用

### 1.1 定义关联模型

Java文件：HelloWorld.java

<pre><code>
class HelloWorld
{
    public native void displayHelloWorld();//调用Dll文件中的方法名
    static {
        System.loadLibrary("hello");//加载文件Dll文件
    }
   
    public static void main(String[] args) {
        new HelloWorld().displayHelloWorld();//方法调用
    }
}
</code></pre>

### 1.2 编译HelloWorld.class文件

转到当前类文件目录下，执行如下命令：
	
	javac HelloWorld.java

### 1.3 产生编译HelloWorld.h头文件

同样在当前目录下执行如下命令：

	javah HelloWorld//注意Java执行class文件不用带扩展名


### 1.4 用C/C++实现HelloWorld.h中声明的方法


**well，there comes a question？**用C/C++写程序这不是要用VC++6.0IDE的节奏吗？将近有一年的时间没有碰VC6.0了，连怎么建工程和调试程序以及工程文件接口都忘的差不多了···不过还好，本科毕竟做过一些项目，开发过一些VC应用程序，多多少少还有些印象···

目前研究生阶段是进行JavaWeb开发的，掐指一算，目测电脑上的VC软件早卸载了，装上了myeclipse。开发工具不用，难免生疏些···从本科的移动硬盘中拷贝出VC6.0,多少有1G多吧，吭哧吭哧，影视装了几次才装上，好了，怎么实现这个头文件···（尼玛，苦恼中）网上又搜了一片C语言指针电子书，有花了几分钟回味了一番C的奥秘···

引头文件,编译工程···
	
<pre><code>
#include < jni.h>
#include "HelloWorld.h"
#include < stdio.h>

JNIEXPORT void JNICALL
Java_HelloWorld_displayHelloWorld(JNIEnv *env, jobject obj)
{
    printf("Hello world!/n");
    return;
}

</code></pre>

本来生疏，VC6又因为版本兼容性问题，经常崩溃···来来回回，花了大半天折腾了一下午
先提示缺少文件，编译不通过，然后报错，什么问题都有，最后忍无可忍··决定将其卸载，，，
网上下载了强大的VisualStudio2010将近2.5G这么大，算了，直接找同学拷贝了一个···

VisualStudio2010之前没用，没有算则用户自定义安装，怕因为后续少装影响功能，就选择了全功能安装，装花了一段时间时间有点长，打开后，感觉来了，果然用户体验不错，，，高大上，但是，有点卡，上网搜了篇[优化设置][youohua]，优化后，再使用感觉不错, nice!

[youohua]: http://blog.csdn.net/fengbingchun/article/details/8990408
 "VS2010运行速度优化汇总"


### 1.5 VisualStudil2010编译工程


###### * 关于添加工程步骤 

可以参考这篇博客[《创建一个动态链接库 (DLL)，使用VS2010》][new_project],感觉讲的还满细致的。

[new_project]: http://www.cnblogs.com/fickleness/archive/2013/06/25/3154665.html
"个人感觉还不错"

可能会遇到问题：

###### * 编译工程报错

	1. fatal error C1083: Cannot open include file: 'jni.h': No such file or directory 
	解决：（个人觉得合理解决方式）
	讲tomcate的安装文件，拷贝到vs2010中的“**\VSStudio2010\VC\include”目录下
	\jdk\include\jni.h
	\jdk\include\win32\jawt_md.h
	\jdk\include\win32\jni_md.h

	2. LINK : fatal error LNK1123: 转换到 COFF 期间失败: 文件无效或损坏
	解决：（个人觉得合理解决方式）
	项目\属性\配置属性\清单工具\输入和输出\嵌入清单：原来是“是”，改成“否”。
	但是，这种方法每个工程均需要修改配置。
	 的


终于可以通过编译，产生HelloWorld.dll文件，好了以为一起搞定了，把次文件放在了测试的目录之下，执行如下命令，调用dll文件
	
### 1.6 执行Dll调用

调用Dll,执行如下命令

	java HelloWorld

###### * 调用Dll异常

	1.  Can't load IA 32-bit .dll on a AMD 64-bit platform

<img src="/assets/images/tech/dll_32_64_jre.jpg" width="60%" height="40%">

问题描述是32bit的dll文件不能运行在64bit的环境下运行？？？？？

Why，然后查了一下我机器的环境，果然问题出在这里（在我的机器上），如在开篇之前交代了环境配置信息：

	1. java version "1.7.0_51" ：
	Java(TM) SE Runtime Environment (build 1.7.0_51-b13)
	Java HotSpot(TM) 64-Bit Server VM (build 24.51-b03, mixed mode)
	2. VisualStudio2010编译动态Dll文件，默认是Win32，所以编译出来的动态链接库是32bit


搜了一下解决方案

1. 重新下载一个32位的虚拟机，例如：jre-6u23-windows-i586.exe 
	
	32bitJRE可以调用32bit的Dll。可能会对系统造成影响。要调整myeclipse下的jre，以及本地环境jre保持一致，以及tomcate下的jre配置，都要保持一致。

2. tcnative-1.dll文件的64bit版本异常

	下载32位的tcnative-1.dll文件，替换Tomcat安装目录bin下的tcnative-1.dll文件。貌似也可以解决，但是也不是太合理

3. Windows 7 64bit和Visual Studio 2010下生成64位程序

	解决：（个人觉得合理解决方式）
	不需要调整整个系统，解铃还须系铃人呀···
	
	参考文档： [《如何：设置正确的目标框架和 CPU》][set_64bit_dll]

[set_64bit_dll]:http://msdn.microsoft.com/zh-cn/library/ff407621.aspx 
"详细步骤"
 	
7. 运行.class文件，执行调用
	
### 到此为止，可以正常用java程序去调用C/C++产生的DLL文件了，关于带参数的调用，说明稍后奉上···

参考文档：

1. [Jni中C++和Java的参数传递][JNI_0]
2. [Java Native Interface (JNI)][JNI_1]
3. [Windows平台上的JNI学习][JNI_2]
4. [System.load 和 System.loadLibrary详解][JNI_3]

 [JNI_0]: http://www.cnblogs.com/qinjunni/archive/2012/02/22/2362734.html
	"一指流砂"
 [JNI_1]:https://www3.ntu.edu.sg/home/ehchua/programming/java/JavaNativeInterface.html "专业讲述"
 [JNI_2]: http://www.cnblogs.com/mizhongqin/archive/2013/05/09/window_jni.html
	"抹去浮华,沉淀深度"
 [JNI_3]: http://m.blog.csdn.net/blog/yujina2008/19040783
 "yujina2008的专栏"

**··························（还想补充一下）·································**

**想说明一点，网上关于技术的帖子太乱了，并且都是大量重复的，有好的技术帖子，也有误导人的。推荐搭建关注国外的技术问答网站:[stackoverflow][nice_tec]**

[nice_tec]:http://stackoverflow.com/
 "很原汁原味的技术帖子"

还想补充的是上述问题 产生HelloWorld.dll文件的问题：因为不同的环境，对不熟悉VisualStudio2010的人来说（包括我在内），很难一步生成适合的DLL文件 如：

操作产生适合64bit虚拟机java执行环境调用的64bit的Dll vc2010配置结果图如下所示：


<img src="/assets/images/tech/Dll_01.jpg" width="60%" height="40%">

（1）配置项目输出清单为“否”

<img src="/assets/images/tech/Dll_02.jpg" width="60%" height="40%">

（2）配置解决方案的环境平台为“x64”和输出平台位x64

<img src="/assets/images/tech/Dll_03.jpg" width="60%" height="40%">

（3）生成解决方案，在对等层次产生一个“x64”文件夹

<img src="/assets/images/tech/Dll_04.jpg" width="60%" height="40%">

（4）点开“x64”文件夹，在其下产生位64bit环境执行的dll文件


---


看时间，已经是凌晨四点，休息休息岁吧，明天还要去实验室···苦逼的研究生生活···


想起了小时候看的动画片“一休 一休，休息 ~ 休息 ~ ”


<img src="/assets/images/life/l_be_U_true.jpg" width="100%" height="60%">

"修行的路总是孤独的，因为智慧必然来自孤独！"

虽然我不全然认同这句话，但是这句话也并无不没有道理!


COME ON!





	


