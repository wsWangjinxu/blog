---
title: GOPATH 和工作区
date: 2019-04-21 10:27:51
category: Go
tags: Go语言学习笔记
---

## 环境变量

在成功安装自己对应操作系统的 go 语言安装包以后，还需要配置三个环境变量：
+ GOROOT：go 语言安装根目录的路径
+ GOPATH：若干工作区目录的路径，是自己定义的工作空间
+ GOBIN：go 程序生成的可执行文件的路径

go 命令依赖一个重要的环境变量：GOPATH。

GOPATH 允许有多个目录，当有多个目录时，请注意分隔符，多个目录的时候 windows 是分号`;`，当有多个 GOPATH 时，默认将`go get`获取的包存放在第一个目录下。

## 源码的组织方式

go 语言的源码是以代码包为基本组织单位的。在文件系统中，这些代码包其实是与目录一一对应的。通俗来讲就是一个目录下面只能有一个 package。由于目录可以有子目录，所以代码包也可以有子包。

一个代码包中可以包含任意个`.go`为扩展名的源码文件，这些源码文件都需要被声明属于同一个代码包。代码包的名称一般会与源码文件所在的目录名保持一致。但是 go 语言没有做强制规定，如果不同名，那么构建、安装的过程中会以代码包名称为准。 

每个代码包都会有导入路径，代码包的导入路径是其他代码在使用该包中的程序实体时，需要引入的路径。在工作区中，一个代码包的导入路径就是从 src 子目录开始，到该包的实际存储位置的相对路径。

## 代码目录结构规划

GOPATH 下的 src 目录就是接下来开发程序的主要工作目录，也就是我们的工作区，所有的源码都是放在这个目录下面的，一般的做法就是一个目录一个项目。

例如：`$GOPATH/src/mymath`表示 mymath 这个应用包或者可执行应用，这个根据 package 是 main 还是其他来决定，main 是可执行应用，其他就是应用包。

GOPATH 目录约定有三个子目录：

+ `src` 存放源代码（比如：.go .c .h .s等）按照go的默认约定，是`go run`、`go install`等命令的当前工作路径（即在此路径下执行上述命令）
+ `pkg` 编译生成的中间文件（归档文件，程序生成后的静态库文件）
+ `bin` 编译后生成的可执行文件（为了方便，可以把此目录加入到`$PATH`变量中，如果有多个 GOPATH，那么使用`${GOPATH//://bin:}/bin`添加所有的 bin 目录）

```
// 一个整体开发目录
go_project // go_project 为 GOPATH 目录
  -- bin
    -- myapp1
    -- myapp2
  --  pkg
  -- src
    -- myapp1 // project1
      -- models
      -- controllers
      -- others
      -- main.go
    -- myapp2 // project2
      -- models
      -- controllers
      -- others
      -- main.go
```

当我们执行`go install`命令来安装一个项目的源码以后，对应工作区的 src 子目录下的源码文件在安装后一般会被放置到当前工作区的 pkg 子目录下对应的目录中，或者直接放置到该工作区的 bin 子目录中。

安装过程中可能会产生编译后生成的中间文件，产生的中间文件与代码包的名字相同，放置它的相对目录就是代码包的导入路径的直接父级。

```
// 导入路径
github.com/project/init

// 中间文件的存放路径
github.com/project
```

中间文件的相对目录与 pkg 目录之间还有一级目录，叫做平台相关目录。平台相关目录的名称时由构建的目标操作系统、下划线和目标计算机架构的代码组成的。例如构建某个代码包时的目标操作系统是 Linux，目标计算架构是 64 位的，那么对应的平添相关目录就是 linux_amd64。

## 理解构建和安装 go 程序的过程

构建使用`go build`,安装使用`go install`。构建和安装代码包都会执行编译，打包操作，并且，这些操作生成的任何文件都会被先保存在某个临时的目录中。

如果构建的是库源码文件，那么操作后产生的结果文件只会存放于临时目录中。这里的构建的主要意义在于检查和验证。如果构建的是命令源码文件，那么操作的结果文件会被搬运到源码文件所在的目录中。

安装操作会先执行构建，然后还会进行链接操作，如果安装的是库源码文件，结果文件会被搬运到它所在的工作区的 pkg 目录下的子目录中；如果安装的是命令源码文件，那么结果文件会被搬运到它所在的工作区的 bin 目录中，或者环境变量 GOBIN 指向的目录中。

## GOPATH 多工作区问题

如果设置了多个工作区，那么查找依赖包是以怎样的顺序进行的？

例如 a 依赖 b，b 依赖 c。那么会先查找 c 包，那么在工作区是如何查找这个依赖包 c 的哪？首先在，查找依赖包的时候，总是会先查找 GOROOT 目录，也就是 go 语言的安装目录，如果没有找到依赖的包，才会到工作区区找相应的包。

在工作区中是按照设置的先后顺序来查找的，也就是会从第一个开始，依次查找，如果找到就不再继续查找，如果没有找到，就报错了。`go get`会下载代码到 src 目录，但是只会下载到第一个工作区目录。

在 go 语言程序中，每个包都有一个全局唯一的导入路径。导入语句中类似"github.com/xxxx/item"的字符串对应包的导入路径。go 语言的规范并没有定义这些字符串的具体含义或包来自哪里，它们是由构建工具来解释的。一个导入路径代表一个目录中的一个或多个 go 源文件。

## 参考内容
[工作区和GOPATH的注意事项](https://cloud.tencent.com/developer/article/1339642)
[Go 语言之讲解 GOROOT、GOPATH、GOBIN](https://cloud.tencent.com/developer/article/1200612)
[GOPATH有多工作区的问题](https://cloud.tencent.com/developer/article/1339789)


