---
title: Gitflow学习笔记
category: Git
tags: Git
date: 2020-04-07 17:35:53
---
2010年的时候Vincent Driessen曾经写过一篇题目为[”A successful Git branching model”](https://nvie.com/posts/a-successful-git-branching-model/)的文章，gitflow 的工作流程就是从这里来的。gitflow遵循严格的分支管理模型。对于我们平时开发分支的管理很有借鉴意义。它可以使得版本库的演进保持简洁，主干清晰，各个分支各司其职、井井有条。
## 分支模型
+ master
	master 分支都是可以直接发布的稳定版本，每次版本发布都会在 master 上打标签，也就是说master分支上的代码都是可以直接发布的稳定版本
+ develop
	develop 分支保存所有的版本历史，往往代码也是最新的
+ feature（临时分支）
	当有新的需求要开发新的功能的时候，就从develop分支迁出一个新的feature分支开发新的功能。当功能开发完成以后可以将feature分支合并回develop分支，此时可以进行code review和代码评审。完成这些以后可以合并到develop并删除feature分支
+ release（临时分支）
	这里是预发布的版本，当从develop分支迁出一个准备发布的版本，主要用于文档整理，功能测试 。完善以后可以发布到master分支，并用版本号打上标签，同时需要再合并到develop分支。完成上述操作以后可以将release分支删除。
+ hotfix（临时分支）
	这是用来修复bug的分支。当线上master分支遇到bug需要修复的时候，可以从master分支迁出hotfix分支修复。修复完成以后合并回master分支和develop分支。此时的develop分支还在继续迭代中。修复的bug会在下次发布的时候被修复。
    
## 用故事来举个栗子
小何和小刘是公司前端的得力干将。当前有个项目已经用脚手架搭建完成，并且自动生成了默认的master分支。
### 迁出develop分支
团队商量决定使用gitflow的分支管理方案。于是乎小何一顿操作基于master分支建立了develop分支。
```bash
# 建立 develop 分支
git checkout -b develop master
# 推到远程仓库
git push -u origin develop
```
这时候develop和master分支的代码都是一样的。程序员做事情那都是说干就干，下面立马开始了新功能的开发。小何和小刘一顿商量，如此如此，这般这般就把任务给分配了。
### 在feature分支上开发新的功能
小刘负责了一个A功能，于是他在命令行里输入了如下命令：
```bash
# 创建开发 A 功能的分支，以 feature-A 来命名
git checkout -b feature-A develop
```
完事以后，扑到编辑器里一顿操作。于此同时，小何的分支也如法炮制的创建好了。两位老哥不遗余力的耕耘了多少个日夜。
虽然新的功能还没有完全开发完，但是分支的代码还是可以保留在远程。这样就不怕自己电脑出问题了。
```bash
git add .
git commit 
git push -u origin feature-A
```
小刘的A功能开发完事以后，就可以进行code review和代码评审了。评审通过以后就可以合并到develop分支了。
```bash
# 将 feature-A 分支合并到 develop 分支
git checkout develop
git merge --no-ff feature-A
# 删除 feature-A 分支
git branch -d feature-A
```
没想到这时候公司新来的小胡已经提交了好几个feature（develop分支一直的积累新的功能）了。feature-A分支合并完成以后，小刘依依不舍的删除了自己的分支，回想起无数个寂寞的夜，嘴里还叼着那半根没有抽完的烟……
### 准备发布新版本了
当develop分支的功能积累的差不多的时候了，该发布新版本了，于是乎小刘基于develop分支迁出了一个新的release分支：
```bash
git checkout -b release-0.1 develop
```
这个分支是专门用于发布前的准备，包括一些清理工作、全面的测试、文档的更新以及任何其他的准备工作。它与功能开发的分支相似，不同之处在于是专门为产品发布服务的。准备工作完成以后，就可以合并到master和develop分支了。
```bash
# 合并到 master 分支
git checkout master
git merge --no-ff master
# 对合并生成的新节点，做一个标签
git tag -a 0.1
# 合并到 develop 分支
git checkout develop
git merge --no-ff release-0.1
# 删除预发布分支
git branch -d release-0.1
```
小刘的A功能终于上线了。
### 出Bug了
虽然经过了严格的测试但是还是出现了bug。可能是功能的逻辑太复杂，也可能是测试用例没有覆盖到。总是是出了bug。小刘开心的笑出了声，原来我平时开发的网站真的有人在用（手动狗头）。打开命令行就是一顿操作：
```bash
# 迁出一个 hotfix 分支修改 bug
git checkout -b hotfix-0.1 master
```
小刘使出了九牛二虎之力，终于把bug给修复了。露出了欣慰的笑容。
```bash
# 将修改的结果合并到 master 上
git checkout master
git merge --no-ff fixbug-0.1
# 打上标签
git tag -a 0.1.1
# 再合并到 develop 分支上
git checkout develop
git merge --no-ff hotfix-0.1
# 删除 hotfix 分支
git branch -d hotfix-0.1
```
## 参考文献
+ [Gitflow 工作流程](https://www.cnblogs.com/jeffery-zou/p/10280167.html)
+ [Git 分支管理策略](http://www.ruanyifeng.com/blog/2012/07/git.html)
+ [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/)
