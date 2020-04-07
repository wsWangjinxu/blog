---
title: Git基础
category: Git
tags: Git
date: 2019-04-7 14:19:53
---


## 1. 安装Git

下载地址：https://git-scm.com/downloads

根据不同的系统，选择下载，之后选择默认配置安装即可。

## 2. 使用之前的最小配置

配置user信息，一般配置如下：

```
// 配置全局用户信息
git config --global user.name 'your_name'
git config --global user.email 'your_email@domain.com'

// config的三个作用域
git config --local // 只对某个仓库有效，缺省等同于local（优先级大于global）
git config --global // 对当前用户的所有仓库有效
git config --system // 对系统所有登录的用户有效（不常用）
```

## 3. 建立代码仓库

一般有两种场景：
+ 把已有的项目代码纳入Git管理

```
cd 项目代码所在的文件夹
git init
```

+ 新建的项目直接用Git管理

```
cd 某个文件夹
git init your_project // 会在当前路径下创建和项目名称相同的文件夹
cd your_project
```

## 4. 工作区和暂存区

在工作目录中修改内容产生的变更通过`git add files`会进入暂存区，暂存区的内容已经被git所管理，暂存区的内容通过`git commit`命令提交以后就会被计入版本历史当中。

## 5. commit、tree和blob三个对象之间的关系

日常开发中，会通过`git add files`将开发完成的内容先放入暂存区中，暂存区中的内容会在一次提交以后计入版本历史中，这个操作通过`git commit`来完成。

当我们提交以后会Git会为我们生成一个提交对象。对象记录了我们这一次提交的状况，包括上一次提交是什么，下一次提交是什么，以及作者和提交人。其中tree对象表示我们提交的时的整个工作目录的一个文件快照，用来记录当前提交时的文件的内容，包括我们所作的变更。整个文件系统对应为一个tree结构，文件夹对应为一个tree对象，文件对应为一个blob对象，可以通过`git cat-file -t "hash value"`来查看文件的类型（包括：commit，tree，blob），通过`git cat-file -p "hash value"`来查看文件的内容。

**Git对于相同的文件只会存一个blob**。不同的commit的区别是commit、tree和有差异的blob，多数未变更的文件对应的blob都是相同的。这么设计对于版本管理系统来说可以节省很多存储空间。其次，Git还有增量存储的机制，针对差异很小的blob所设计。

## 6. 分离头指针

本质上，HEAD是某一次提交的引用，也就是一串哈希值。通常情况下，HEAD总是指向某个分支距离当前最近的一次提交。当通过`git checkout "hash value"`命令从某一次提交上尝试切换分支的时候，就会处于分离头指针（detached HEAD）的状态。在当前状态下的修改，会处于没有分支的状态，如果不创建分支，这些修改的代码会被Git当作垃圾清理掉，并且不会出现在版本历史中；如果想要保存修改的内容需要通过命令`git branch <new-branch-name>`创建一个分支。

**基于分支的变更提交以后会被保存下来，处于分离头指针状态的分支需要创建分支以后才会被保存下来。这种操作看似危险但是是很有必要的。因为在实际项目中我们很可能需要基于某一次提交做变更，这个时候需要特别注意，如果没有创建分支，那么变更将会丢失。**

## 7. 常用的命令与工具

```
git status // 查看当前目录下的文件的状态
git reset --hard //清除暂存区的内容
git mv <filename1> <filename2> //变更文件名，将文件filename1变更为filename2
git log -[n] // 查看最新的某几条日志
git log --graph // 查看图形化的日志
git diff // 用于比较差异
gitk // 图形化界面，用于查看版本的历史
git add files // 将文件加入到暂存区
git commit -m "message" // 提交
git push // 将当前的变更push到远程的代码仓库
git pull // 从远程的代码仓库拉取最新的代码
git checkout // 切换分支
git checkout -b <new-branch-name> <base-branch> // 基于某个分支创建一个新分支
git branch <new-branch-name> // 创建一个新的分支
```