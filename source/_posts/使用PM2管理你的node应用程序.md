---
title: 使用 PM2 管理你的 node 应用程序
date: 2020-04-04 13:19:12
tags: node
category: JavaScript
---

PM2 是守护程序进程管理器，主要有三个核心点：进程守护，当系统崩溃的时候能够自动重启；能够启动多进程，充分利用 CPU 和内存；自带日志记录功能。
## 安装
可以通过 npm 和 yarn 安装最新的 pm2 版本
```bash
npm install pm2 -g
# or
yarn global add pm2
```
## 三个核心功能
### 进程守护
当你使用 `node app.js`和`nodemon app.js`启动 node 应用程序的时候，遇到程序出现 bug，进程就会崩溃，这样你的服务器就会宕机。在 pm2 中使用`pm2 start app.js`这种最简单的方式启动你的node 应用。就能够守护和监视你的应用。当程序遇到错误崩溃的时候，系统会自动重启，保证系统没有问题的部分仍然是可用的。
### 集群模式
集群模式允许联网的 node 应用在不用修改代码的情况下，根据可用的 CPU 数量进行缩放。这将大大提高应用程序的性能和可靠性。由于 node 单核可用内存是有限的，使用 pm2 可以在资源允许的情况下启动多个子进程。如果想要这样做，你只需要在配置文件中写入`instances: "max",exec_mode: "cluster"`即可。当然，你也可以按个人需求配置对应的实例数量。
### 日志管理
在 pm2 中可以实时显示来自所有应用程序的日志，刷新并重新加载它们。你还通过编写配置文件的形式将日志保存并分隔在不同的文件中，这些操作你都不需要修改代码中的任何内容。如果你需要实时显示日志，可以在命令行中输入`pm2 logs <AppName>`。
## 常用命令
```bash
# 在文件更改时自动重启应用
pm2 start app.js --watch
# 管理应用程序状态
pm2 restart app_name
pm2 reload app_name
pm2 stop app_name
pm2 delete app_name
# 列出托管的应用程序
pm2 ls 
# 显示日志 
pm2 logs
# 显示基于终端的实时仪表板
pm2 monit
```
## ecosystem 文件
以上只是关于 pm2 入门的概述和一些日常使用命令的介绍，想要获得更多的功能可以使用ecosystem 配置文件，生成配置文件的命令是`pm2 ecosystem`，配置文件的详细配置项可以参考[这里](https://pm2.keymetrics.io/docs/usage/application-declaration/)。这是只是做一个简单的概述。
## 总结
pm2 的[文档](https://pm2.keymetrics.io/docs/usage/pm2-doc-single-page/)写的非常详细，而且还有很多很多功能，最重要的是上手还很容易，如果你不幸看到了我写的这么简陋的文章，请帮忙点个赞，激励我写出更多高质量的文章来，写文章不易，你看我已经很久没写了，今天好不容易又提起兴趣来了（手动狗头）。
## 参考文献
我能说我基本都是照抄[官网](https://pm2.keymetrics.io/docs/usage/pm2-doc-single-page/)的吗？什么？你不想看英文？…