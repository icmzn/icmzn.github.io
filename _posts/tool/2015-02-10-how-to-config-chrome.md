---
layout: post
title: 如何配置chrome浏览器进行正常的google搜索
category: 工具
tags: Blog
keywords: Chrome,Google,博客
description: 
---

 Google 的浏览器方便好用，但是新安装的chrome,却不能进行正常的google搜索，之前一直习惯用chrome浏览器，如果不能用google的搜索服务，在信息获取检索上感觉非常不习惯，简单的配置chrome浏览器，使之能获取google所搜服务，步骤如下：

### 1.修改host 文件

win7系统host文件位置：C:\Windows\System32\drivers\etc 找到host文件 用记事本工具打开；

### 2.获取www.google.com 对应的ip 地址映射

在开始栏，输入cmd 打开控制端，使用ping命令 评出域名的ip地址

		ping www.google.com

根据现实信息得到对应的ip:173.194.116.227

<img src="/assets/images/tool/ping_google.png" width="50%" height="50%">

将此ip以下图形式添加的host文件中：

<img src="/assets/images/tool/t_host.png" width="50%" height="50%">

这两部基本上可以获取google服务了，如果访问www.google.com 失败原因可以进行如下操作

---

##### 1.关于chrome的设置()，可能是你之前设置的代理的操作
打开chrome，在地址栏输入：chrome://settings/ 按下图设置

<img src="/assets/images/tool/t_chrome_config.png" width="50%" height="50%">

##### 2.或者是你的浏览器有DNS缓存还是设置的映射关系，需要清理chrome的缓存
打开chrome，在地址栏输入：chrome://net-internals/#dns 按下图设置

<img src="/assets/images/tool/t_clear_dns.png" width="50%" height="50%">

基本上能够进行正常的google服务请求了

---

如果想访问youtobe，facebook， twiter等网站，目前还不能进行，需要进行翻墙设置，如何翻墙，步骤稍后奉上 ··· 


一片很好的git指令学习教程[gitorder][] 阮一峰

  [gitorder] http://www.ruanyifeng.com/blog/2014/06/git_remote.html
