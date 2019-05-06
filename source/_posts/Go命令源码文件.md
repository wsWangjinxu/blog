---
title: Go 命令源码文件
date: 2019-05-05 21:44:01
category: Go
tags: Go语言学习笔记
---

## 命令源码文件是什么，怎样编写它？

如果一个源码文件声明属于 main 包，并且包含一个无参数声明且无结果声明的 main 函数，那就是命令源码文件。

命令源码文件是程序的入口，通过构建和安装可生成对应的可执行文件，后者一般会与该命令源码文件的直接父目录同名。

```
// 简单的命令源码文件格式

package main

import "fmt"

func main() {
  fmt.Println("Hello World!")
}
```

当需要模块化编程时，会将代码拆分到多个文件，甚至拆分到不同的代码包中。但对于一个独立的程序来说命令源码文件永远只会也只能有一个。如果有与命令源码文件同包的源码文件，也应该声明属于 main 包。

## 命令源码文件怎样接收参数

首先，Go 语言标准库中有一个代码包专门用于接收和解析命令参数，这个代码包的名字叫 flag。可以通过 flag 包的 StringVar 函数来接收命令行参数。

```
// 可以接收 go run demo.go -name "wangjinxu" 后面的参数
flag.StringVar(&name, "name", "everyone", "The greeting object.")
```

函数 flag.StringVar 接受 4 个参数：
+ 第一个参数是用于存储该命令参数值的地址，具体到上面的例子就是变量 name 的地址，由 `&name` 表达式表示。
+ 第二个参数是为了指定该命令的名称，这里是 name。
+ 第三个参数是为了指定在未追加该命令参数时的默认值，这里是 everyone。
+ 第四个参数是该命令参数的简短说明。在输入`go run file.go --help`打印命令说明的时候会出现。

还有一个与 flag.StringVar 函数类似的函数，叫 flag.String。这两个函数的区别是，后者会直接返回一个已经分配好的用于存储命令参数值的地址。

```
// name 是存储命令参数值的地址
var name = flag.String("name", "everyone", "The greeting object.")
```

接收完参数以后需要解析参数，解析参数需要使用 `flag.parse()`。对该函数的调用必须在所有命令参数存储载体的生命和设置之后，并且在读取任何命令参数值之前进行，所以需要把 `flag.Parse()` 放在 main 函数体的第一行。

下面是完整的代码：

```
package main

import (
  "flag"
  "fmt"
)

var name string

func init() {
  flag.StringVar(&name, "name", "everyone", "The greeting object.")
}

func main() {
  flag.Parse()
  fmt.Printf("Hello, %s\n", name)
}
```

## 怎样自定义命令源码文件的参数使用说明

这有很多种方法，最简单的一种方式就是对变量 flag.Usage 重新赋值。flag.Usage 的类型是 func()，即一种无参数声明且无结果声明的函数类型。

对 flag.Usage 的赋值必须在调用 flag.Parse 函数之前。

```
flag.Usage = func() {
  fmt.Fprintf(os.Stderr, "Usage of %s:\n", "question")
  flag.PrintDefaults()
}

// 运行 go run demo.go --help 结果如下

Usage of question:
  -name string
    The greeting object.(defalut "everyone")
exit status 2
```

在深入一层，在调用 flag 包中的一些函数(比如 StringVar、Parse 等等)的时候，实际上是在调用 flag.CommandLine 变量的对应方法。

falg.CommandLine 相当于默认情况下的命令参数容器。所以，通过对 flag.CommandLine 重新赋值，可以更深层次的定制当前命令源码文件的参数使用说明。

注销掉 main 函数中的 flag.Usage 变量赋值语句，然后在 init 函数体的开始处添加如下代码：

```
flag.CommandLine = flag.NewFlagSet("", flag.ExitOnError)
flag.CommandLine.Usage = func() {
  fmt.Fprintf(os.Stderr, "Usage of %s:\n", "question")
  flag.PrintDefaults()
}
```

再运行命令`go run demo.go --help`后，期输出会与上一次的输出一致。不过后面哦这种定制的方法更加灵活。比如，当将赋值的那条语句改为：

```
flag.CommandLine = flag.NewFlagSet("", flag.panicOnError)
```
再运行`go run demo.go --help`命令就会产生另一种输出效果。这里传给`flag.NewFlagSet`函数的第二个参数值是`flag.PanicOnError`。

+ `flag.ExitOnError`的含义是，告诉命令参数容器，当命令后跟`--help`或者参数设置不正确的时候，在打印命令参数使用说明后以状态码 2 结束当前程序。状态码 2 代表用户错误地使用了命令。
+ `flag.PanicOnError`的含义是抛出“运行时恐慌”

下面更进一步，不用全局的 flag.CommandLine 变量，转而创建一个私有的命令参数容器：

```
var cmdLine = flag.NewFlagSet("question", flag.ExitOnError)
```
然后对 flag.StringVar 的调用替换为对 cmdLine.StringVar 调用，再把 `flag.Parse()` 替换为 `cmdLine.Parse(os.Args[1:])`。这样做就完全脱离了 flag.CommandLine。这样做的好处依然是更灵活的定制命令参数容器。但是定制完全不影响全局变量 flag.CommandLine。

## 参考内容
[Go 语言命令源码文件](https://studygolang.com/articles/14209)