---
layout: post
title: maven搭建项目，需要注意问题
category: 工具
tags: Blog
keywords: Chrome,Google,博客
description: 
---

 maven搭建项目，管理项目文件，jar包非常方便，关于如何使用maven搭建项目，这里不多介绍，主要介绍一些我在使用maven管理项目遇到的问题与采取的解决方案

### 1.maven clean 执行后出现很多类无法引用等问题

报错类型：XX cannot be resolved to a type

（1）jdk不匹配（或不存在）

项目指定的jdk为“jdk1.6.0_18”，而当前eclipse使用的是“jdk1.6.0_22”。需要在BuildPath | Libraries，中做简单调整。


（2）jar包缺失或冲突

 当找不到“XX”所在的jar包时，会报这个错。解决只需要找到（ctrl+点击，可能能看到jar包名称）对应jar包导入项目即可。 

讲项目所用的jar包maven库中没有的jar包，放在src目录下的lib文件夹下，然后添加到类的构建路径上即可。

（3））myeclipse查找项目类型策略所致

上述两条都对比过了，没有任何问题，可偏偏还报这错。这时，需要操作一下 Project | Clean... ，问题即可解决。原因是，机制所致。因为某些特殊原因，eclipse没能自动编译源代码到build/classes（或其他classes目录），导致类型查找不到。 

然后重新执行maven instal 即可恢复 


### 2.执行项目生成的jar包名称

修改pom文件，添加如下信息，即可将最终生成的war包名字设定为warName

	<build>
		<finalName>warName</finalName>
	</build>


## 待完善···

---
<img src="/assets/images/life/tool_life_02.jpg" width="80%" height="60%">


