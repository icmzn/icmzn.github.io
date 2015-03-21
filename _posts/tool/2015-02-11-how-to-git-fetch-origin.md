---
layout: post
title: 如何有步骤的处理github远程代码到本地并进行与git常用指令总结
category: 工具
tags: Blog
keywords: merge,github,remote,fetch
description: 
---

**github**方便代码管理，操作本地代码库指令简单，本文解决如何有步骤的讲本地代码库与远程代码库进行合并。

---

#介绍简单的基本命令作用

	    本文由icmzn精心整理，转载请注明出处(http://www.icmzn.com)！

##基本配置信息

	1. 产生本地公钥信息，用于提交远程身份验证
		$ ssh-keygen -t rsa -C "youremail@example.com"
	2. 设置本次的用户名 和 邮箱
		$ git config --global user.name "john"
		$ git config --global user.email "email@126.com"
	3. 设置变量 origin 和 remote 值
		$ git remote add origin git@github.com:aa/gg.git
	4. 删除变量 remote 值
		$ git remote rm origin
	5. 显示当前git的全局配置变量
		$git config --list
	6. 掌握仓库当前的状态
		$git status
	7. 可以指定文件修改内容，从编辑转台退出的指令
		$ git diff fileName （文件名称）
		$ W 
	8. 查看日志
		$ git log
		$ git log --pretty=oneline : 单行显示
	9. 将HEAD指针指向对应的版本号，同时修改仓库的环境
		$ git reset --hard HEAD^
	10. 让版本随意回退到任何一个版本（之前或之后都可以）
		$ git reflog 显示所有操作的信息（含版本id）
		$ git reset --hard commit_id (一般取id前7位)
	11. 让版本随意回退到任何一个版本（之前或之后都可以）
		$ git reset HEAD fileName
	12. git删除记录信息
		$ git rm fileName
		撤销 $ git checkout -- fileName
	13. 代码由“工作区”保存至“暂存区”
		$git add .
	14. 代码由“暂存区”保存到“工作区”
		$git commit -m "注释"

##本地代码管理


	1. 撤销“工作区”修改， 将“暂存区”中的最新版覆盖“工作区”
		$ git checkout --fileName
	2. 撤销“暂存区”修改， 将“本地仓库”最新版覆盖“工作区”和“暂存区”
		（1）$ git reset HEAD fileName 修复暂存区为版本库中的最新内容
		（2）$ git checkout --fileName 将“暂存区”中的最新版覆盖“工作区” 
		$git config --list
	3. 撤销“本地仓库”修改， 将回退之前的版本
		$ git reset HEAD 版本号（前7位即可）
		或
		$ git reset heard HEAD 或HEAD^ 
	4. 查看分支
		$ git branch
	5. 创建分支
		$ git branch <name>
	6. 切换分支
		$ git checkout <name>
	7. 创建+切换分支
		$ git checkout -b <name>
	8. 合并某分支到当前分支
		$ git merge <name>
	9. 删除分支
		$git branch -d <name>
    10. 查看本地分之的合并图形情况
        $ git log --graph --pretty=oneline --abbrev-commit

##远程代码管理

	1. 本地代码推送
		$ git remote add origin git@github.com:aa/gg.git
		$ git push -u origin master
		$git config --listv
		$git config --listv

	2. 克隆远程代码仓库
		$ git clone git@github.com:aa/gg.git
	3. 查看远程库信息
	 	$ git remote -v
	4. 从本地推送情况
		（1）git push origin master
		如果推送失败，先用git fetch抓取远程的新提交
		（2）git fetch origin
		本地如有冲突，远程文件放在本地 origin/master 之下
		（3）$git merge origin/master 将origin/master合并到本地分支上	
	5. 本地创建和远程分支对应的分支，本地和远程分支的名称最好一致
		$ git checkout -b branch-name origin/branch-name
	6. 建立本地分支和远程分支的关联
		$ git branch --set-upstream branch-name origin/branch-name；
	7. 查看本地分支和远程分支的差异
		$ git diff master origin/master
	8. 查看当前本地分支情况
		$ git branch 	
	9. 查看远程分支情况
		$ git branch -r

###注意问题：

	1. 删除文件，不能直接在本地文件夹中操作，需要使用git指令,这样不会破坏github的版本控制的完整性
	 $ git rm fileName 

	2. git 重命名指令对文件名进行修改，也不能直接修改，原因同上
	   将本地文件 README.txt 重命名为 README.md ；查看文件状态，然后提交到 repository 
	 $ git mv README.txt README.md
	 $ git add .
	 $ git commit -m "first rename filename"
	 执行 commit 的时候，其实是执行了两个步骤，把 README.txt 删除提交，把 README.md 提交；

---

   git中文学习：[clickme][zhongwenxuexi]

   git学习博客：[clickme][yuanyifeng]

  [yuanyifeng]:http://www.ruanyifeng.com/blog/2014/06/git_remote.html 
	"阮一峰"

  [zhongwenxuexi]: http://www.liaoxuefeng.com/wiki "git中文指南"
