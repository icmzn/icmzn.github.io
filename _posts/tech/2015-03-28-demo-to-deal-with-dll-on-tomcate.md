---
layout: post
title: java web 调用DLL demo
category: 工具
tags: Blog
keywords: Chrome,Google,博客
description: 
---

写本片博客的，是继续前面一片介绍，本项目的具体实施，重点放在了在部属项目到服务器上成功DLL中遇到的问题与解决方案

---

## 项目用例本地测试通过

### 1. 调用带参数的dll准备工作

####（1）JNIDLL.java文件：
<pre><code>
package com.tool;

public class JNIDLL {
   static {
      System.loadLibrary("packagemyjni"); //生成dll的文件名字
   }
   // 输入一个字符串，返回一个字符串
   private native String sayHello(String msg);
 
   public static void main(String args[]) {
	//调用
    String result = new JNIDLL().sayHello("Hello from Java");
	//输出打印结果
    System.out.println("In Java, the returned string is: " + result);
   }
}

</code></pre>

目录结构：

> com\tool\JNIDLL.java

#### （2）在根目录，即com对等层次 执行如下命令，生成class文件

	javac com\tool\JNIDLL.java
目录结构：
> com\tool\JNIDLL.java <br>
> com\tool\JNIDLL.class

#### (3) 在根目录，即com对等层次 执行如下命令，生成.h头文件

	javah com.tool.JNIDLL 

目录结构：
> com\tool\JNIDLL.java <br>
> com\tool\JNIDLL.class <br>
> com_tool_JNIDLL.h\

#### (4)用c/c++实现com_tool_JNIDLL.h文件

<pre><code>
#include < jni.h>
#include < stdio.h>
#include "com_tool_JNIDLL.h"

JNIEXPORT jstring JNICALL Java_com_tool_JNIDLL_sayHello(JNIEnv *env, jobject thisObj, jstring inJNIStr) {
	const char *inCStr = (*env).GetStringUTFChars( inJNIStr, NULL);//获取字符指针
	if (NULL == inCStr) return NULL;
	printf("In C, the received string is: %s\n", inCStr);//打印信息
	(*env).ReleaseStringUTFChars( inJNIStr, inCStr);  // 一定要释放资源
	jstring message = (*env).NewStringUTF(" ***Hello from John Lewis Demo **");
	//重新生成心得字符串信息，并返回
	return message;
}
</code></pre>

**注意：**前一篇博客介绍，一定要根据本机的环境，选择生成合适的dll文件类型
如本机：64bitWin7，选择x64平台解决方案生成dll文件如下(修改dll文件名字，与前面保持一致)：
**packagemyjni.dll**

目录结构：
> com\tool\JNIDLL.java <br>
> com\tool\JNIDLL.class <br>
> com_tool_JNIDLL.h\<br>
> packagemyjni.dll\


*目前为止，准备工作基本结束*

### 2. 本地调试

在控制台，转移到当前目录结构，（保持当前目录结构即可），执行以下命令：

	java com.tool.JNIDLL

如果顺利，那么恭喜你！我调试过程中出现的问题如下：

#### (1) 因为dll问题引起的异常
<pre><code>
D:\abc\abc>java com.tool.JNIDLL
Exception in thread "main" java.lang.UnsatisfiedLinkError: com.tool.JNIDLL.sayHe
llo(Ljava/lang/String;)Ljava/lang/String;
        at com.tool.JNIDLL.sayHello(Native Method)
        at com.tool.JNIDLL.main(JNIDLL.java:11)
</code></pre>

**对于异常，要学会透过现象看本质！**

使用工具：**DLL Export Viewer v1.63**，可以查看你的dll的对外接口形式，生成的接口是否规范等，查看信息如下：通过对比，我发现因为在使用c实现.h文件过程中，集成的.h头文件名，不一样，导致生成的dll文件函数名称不是有效的方法名，但是dll文件也能生成：当我调用的时候，总提示方法不到--**所以写程序，要细心，不然会因为找这个bug需要付出一定的精力与时间来换取**

<img src="/assets/images/tool/jni_dll_view.jpg" width="80%" height="60%">

如果c/c++有误，生成的dll如下图，可以看出，两个的在调用函数名称上，完全大相径庭，如果java能成功调用才怪！如下图：

<img src="/assets/images/tool/jni_dll_view_error.jpg" width="80%" height="60%">


**心得体会：只有保证每一个模块的功能正常，然后，步步为营，各个模块连在组成系统后，链接调试工作就会变得顺水推舟啦 ! ：)**

#### (2) 项目是否还需要其他类库支持？

我们这个项目因为需要实现一个图像识别的功能，同时也依赖调用了openVC相关的类库，虽然把实现部分的调用关联的dll文件都放在了类的加载路径智商了，但是还是包了错误···一时不知道，因为是实现部分少了其他关联的dll文件···
出现：依赖的库文件不存在。

**一定要注意，程序运行的环境的不同，Wins本地的类库是不一样完全的**

使用软件：**Dependency Walker 2.2** 可以查看，你的dll文件依赖的类库是否在当前系统中存在
，通过使用此原件，我的dll依赖的库情况如下图：

<img src="/assets/images/tool/jni_dll_los.jpg" width="80%" height="60%">

那么问题来了：如何解决这些lost or missing dll ? 

> 1. 根据缺失的dll文件名字，去网上直接去下载的；
> 可能网上的dll文件与你本机的文件不兼容，可以解决。

> 2. 找dll修复工具，自动修复；
> 修复工具如果不是官方绿色的软件，有可能含有木马病毒，入侵你的计算机。

  我用的是方法2，我计算机上360的安全卫士，使用360急救箱中有个dll修复功能，根据缺失的文件名字，直接修复了缺失的dll文件，方便，并且是官方软件。然后重启电脑。

*ok，现在本次测试通过了 *

---

## 迁移至项目并部署到tomcat服务器

### 1. 讲所有依赖的dll在tomcate服务器上的位置

讲文件放在tomcate\bin文件夹下面，因为在系统启动的时候，下面程序会自动从tomcat加载路径上获取dll等相关文件。

	System.loadLibrary("CoinImageRecognition");

可以通过以两个程序输出你本机的相关信息：
		
	System.out.println(System.getProperty("java.library.path"));
	//输出java的类加载路径
	System.out.println(System.getProperty("sun.arch.data.model"));
	//输出本机操作系统的bit（位）如：64即64位操作系统

### 2. 用上面测试依赖dll文件工具测试服务器系统环视，是否有完整的dll文件

如果测试通过，则可以实现部署了····

需要说明的是，期间我使用“百度管家”，有一个dll修复，但是可以下到对应名称的dll文件，但是，相应的dll文件cup格式是x84，额我的计算机cup是x64，所以是不兼容的，需要注意。

还有，在服务器上，有些确实的dll文件，并不影响服务器对dll调用，我们这个项目主机系统提示，缺少某些dll，但是在部属的时候，功能可以正常的调用。

---

这几天都在调试这个接口的问题，最后还是解决了，所以，碰到问题不要回避，碰到的问题一开始是没有思绪的，如果有思绪，那还叫问题吗？？？虽然这个过程中调研了很多方法，尝试了很多解决途径，最后看来，有一些确实是无用的尝试，但是，如果你不去尝试，你怎么知道这样是不可行的呢。

### 提高自己的处理问题的能力，很重要！


要经常思考问题，勤奋学习，脚踏实地

拨开现象，参透问题的本质，是关键！！！

Just do IT ! 

在一点就是，还要注意锻炼身体，每天晚上如果可以，去操场跑去跑一会~~~

<img src="/assets/images/life/web_pic.jpg" width="80%" height="60%">