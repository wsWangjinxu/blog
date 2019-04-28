---
title: 使用Git时的常见场景（维护自己的分支）
tags: Git
---

### 怎么删除不需要的分支？

在日常开发中，我们通常赋予有意义的分支名，Git判断本分支没有和任何分支合并，如果没有，不能被直接删除。如果merge到其它分支，但之后又在其上做了开发，还是不能删除。这意味着删除存在风险。它也提供我们`-D`的方式。如果确定无风险就可以用-D删除。

```
git branch -d <branch-name> // 删除一个分支
git branch -D <branch-name> // 强制删除一个分支
```

### 怎么修改最新的commit的message？

```
git commit --amend // 对最新一次提交的message修改
```

### 怎么修改老旧的commit的message？

```
git rebase -i <has_value> // 变基操作，根据命令提示进行操作，hash_value是需要变更的记录的父记录，在弹出来的内容中选择r前缀的命令
```

### 怎么把连续的多个commit整理成1个？

有多个功能单元统一的commit，可以整理成一个commit

```
git rebase -i <has_value> // 在弹出来的内容中选择squash前缀的命令
```

### 怎么把间隔的几个commit整理成1个？

```

```

### 怎么比较暂存区和HEAD文件的差异？

```
git diff --cached // 比较暂存区和HEAD指针指向文件的差别
```

### 怎么比较工作区和暂存区所含文件的差异？

```
git diff -- <filename> // 比较工作区和暂存区的文件的差别
```

### 如何让暂存区的文件恢复为和HEAD一样？

```
git reset HEAD // 取消暂存区的所有的内容的暂存
```

git reset 有三个参数
--soft 这个只是把 HEAD 指向的 commit 恢复到你指定的 commit，暂存区 工作区不变
--hard 这个是 把 HEAD， 暂存区， 工作区 都修改为 你指定的 commit 的时候的文件状态
--mixed 这个是不加时候的默认参数，把 HEAD，暂存区 修改为 你指定的 commit 的时候的文件状态，工作区保持不变


### 如何让工作区的文件恢复为和暂存区的一样？

```
git checkout -- <filename>
```

### 怎样取消暂存区部分文件的更改？

```
git reset HEAD -- [filename1 filename2]
```

### 消除最近的几次提交

```
git reset --hard <hash_value> // 回退到某一次commit，清除工作区和暂存区的内容（慎重使用）
```

### 看看不同提交的指定文件的差异

```
git diff [commit1/branch1] [commit2/branch2] -- filename 比较不同commit或不同分支的文件的差异
```

### 正确删除文件的方法

```
git rm filename // 正确删除某个文件
```

### 开发中临时加塞了紧急任务

```
git stash // 存储内容的堆栈
git stash list // 查看所有堆栈的内容
git stash apply // 把之前存放的内容应用于工作区和暂存区，不会删除stash里的内容
git stash pop // 把之前存放的内容应用于工作区和暂存区，删除stash里的内容
```

### 如何指定不需要Git管理的文件？

经过构建产生的文件，这些文件是可以通过构建复现的。
通过.gitignore来完成，而且这个文件只对没有加入暂存区的文件有效。

### 如何将Git仓库备份到本地？

常用的传输协议

| 常用传输协议 | 语法格式 | 说明 |
| ----------- | ------- | ----- |
|本地协议1 | /path/to/repo.git | 哑协议 |
|本地协议2 | file:///path/to/repo.git | 智能协议 |
|http/https协议 | http://git-server.com:port/path/to/repo.git <br> https://git-server.com:port/path/to/repo.git  | 平时接触到的<br>都是智能协议 |
|ssh协议 | user@git-server.com:path/to/repo.git | 工作中最常用<br>的智能协议 |

哑协议与智能协议

直观区别：呀协议传输进度不可见；智能协议传输进度可见。
传输速度：智能协议比哑协议传输速度快。

多点备份

备份
```
git clone --bare (地址) rename // 备份

git remote add filename (地址) // 添加远端分支

git push --set-upstream (远端名称)

```


## git与github的简单同步

### 配置公私钥



