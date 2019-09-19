---
title: RESTful架构详解
date: 2019-01-20 21:22:27
category: API
tags: RESTful
---


## 1. 什么是REST

**`REST`全称是Representational State Transfer, 中文意思是表述性状态转移。指的是一组架构约束条件和原则。**如果一个架构符合REST的约束条件和原则，我们就可以称它为`RESTful`架构。

## 2. 理解RESTful

结合`REST`原则，围绕资源展开讨论，从资源的**定义、获取、表述、关联、状态变迁**等角度，列举一些关键概念并加以解释。

  + 资源与URl
  + 统一资源接口
  + 资源的表述
  + 资源的链接
  + 状态的转移

### 2.1 资源与URl

`REST`全称是表述性状态转移，那究竟指的是什么的表述？其实指的就是资源的。**任何事物，只要有被引用到的必要，它就是一个资源。资源可以是实体（例如手机号码），也可以只是一个抽象概念（例如价值）**。下面是一些资源的例子：

  + 某用户的手机号码
  + 某用户的个人信息
  + 最多用户订购的GPRS套餐
  + 两个产品之间的依赖关系
  + 某用户可以办理的优惠套餐
  + 某手机号码的潜在价值
  
要让一个资源可以被识别，需要有个唯一的标识，再Web中这个唯一标识就是URl(Uniform Resource Identifier)。URl既可以看成是资源的地址，也可以看成是资源的名称。如果某些信息没有使用`URl`来表示，那它就不能算是一个资源，只能算是资源的一些信息而已。`URl`的设计应该遵循可寻址性原则，具有自描述性，需要在形式上给人以直觉上的关联。

  + https://github.com/git
  + https://github.com/git/git
  + https://github.com/git/git/blob/master/block-sha1/sha1.h
  + https://github.com/git/git/commit/e3af72cdafab5993d18fae056f87e1d675913d08
  + https://github.com/git/git/pulls
  + https://github.com/git/git/pulls?state=closed
  + https://github.com/git/git/compare/master…next

`URl`设计上的一些技巧：

  + 使用`_`或`-`来让`URl`可读性更好
  + 使用`/`来表示资源的层级关系
  + 使用`?`用来过滤资源
  + 使用`,`或`;`可以用来表示同级资源的关系

#### 统一资源接口

**`RESTful`架构应该遵循统一接口原则，统一接口包含了一组受限的预定义操作，不论什么样的资源，都是通过使用相同的接口进行资源的访问。**接口应该使用标准的`HTTP`方法如，`GET`，`PUT`和`POST`，并遵循这些方法的语义。

如果按照`HTTP`方法的语义来暴露资源，那么接口将会拥有安全性和幂等性（简单来说一个操作多次执行与一次执行产生的结果一致）的特性，例如`GET`和`HEAD`请求都是安全的，无论请求多少次，都不会改变服务器状态。而`GET`、`HEAD`、`PUT`和`DELETE`请求都是幂等的，无论对资源操作多少次，结果总是一样的，后面的请求并不会产生比第一次更多的影响。

下面列出了`GET`，`DELETE`，`POST`和`PUT`的经典用法：

##### GET

+ 安全且幂等
+ 获取表示
+ 变更时获取表示（缓存）
+ 200（OK）- 表示已经在响应中发出
+ 204（无内容）- 资源有空表示
+ 301（Moved Permanently）- 资源的URl已被更新
+ 303（See Other）- 其他（如，负载均衡）
+ 304（not modified）- 资源未更改（缓存）
+ 400（bad request）- 指代坏请求（如，参数错误）
+ 404（not found）- 资源不存在
+ 406（not acceptable）- 服务端不支持所需表示
+ 500（internal server error）- 通用错误响应
+ 503（Service Unavailable）- 服务端当前无法处理请求

##### POST

+ 不安全且不幂等
+ 使用服务端管理的（自动产生）的实例号创建资源
+ 创建子资源
+ 部分更新资源
+ 如果没有被修改，则不更新资源（乐观锁）
+ 200（OK）- 如果现有资源已被更改
+ 201（created）- 如果新资源被创建
+ 202（accepted）- 已经接受处理请求但尚未完成（异步处理）
+ 301（Moved Permanently）- 资源的URl被更新
+ 303（See Other）- 其他（如，负载均衡）
+ 400（bad request）- 指代坏请求
+ 404（not found）- 资源不存在
+ 406（not acceptable）- 服务端不支持所需表示
+ 409（conflict）- 通用冲突
+ 412（Precondition Failed）- 前置条件失败（如执行条件更新时的冲突）
+ 415（unsupported media type）- 接受到的表示不受支持
+ 500（internal server error）- 通用错误响应
+ 503（Service Unavailable）- 服务当前无法处理请求

##### PUT

+ 不安全但幂等
+ 用客户端管理的实例号创建一个资源
+ 通过替换的方式更新资源
+ 如果未被修改，则更新资源（乐观锁）
+ 200（OK）- 如果已存在资源被更改
+ 201（created）- 如果新资源被创建
+ 301（Moved Permanently）- 资源的URl已更改
+ 303（See Other）- 其他（如，负载均衡）
+ 400（bad request）- 指代坏请求
+ 404（not found）- 资源不存在
+ 406（not acceptable）- 服务端不支持所需表示
+ 409（conflict）- 通用冲突
+ 412（Precondition Failed）- 前置条件失败（如执行条件更新时的冲突）
+ 415（unsupported media type）- 接受到的表示不受支持
+ 500（internal server error）- 通用响应错误
+ 503（Service Unavaliable）- 服务当前无法处理请求

##### DELETE

+ 不安全但幂等
+ 删除资源
+ 200（OK）- 资源已被删除
+ 301（Moved Permanently）- 资源的URl已更改
+ 303（See Other）- 其他，如负载均衡
+ 400（bad request）- 指代坏情去
+ 404（not found）- 资源不存在
+ 409（conflict）- 通用冲突
+ 500（internal server error）- 通用响应错误
+ 503（Service Unavaliable）- 服务器当前无法处理请求

下面我们来看一些实践中常见的问题：

+ `POST`和`PUT`用于创建资源时有什么区别？
  `POST`和`PUT`在创建资源的区别在于，所创建的资源的名称（URl）是否由客户端决定。例如为我的博文增加一个java的分类，生成的路径就是分类名`/categories/java`，那么就可以采用`PUT`方法。不过很多人直接把`POST`、`GET`、`PUT`、`DELETE`直接对应上`CRUD`，例如在一个典型的`rails`实现的`RESTful`应用中就是这么做的。

+ 客户端不一定都支持这些`HTTP`方法吧？
  的确有这种情况，特别是一些比较古老的基于浏览器的客户端，只能支持`GET`和`POST`两种方法。在实践上，客户端和服务端都可能需要做一些妥协。

+ 统一接口是否意味着不能扩展带特殊语义的方法？
  统一接口不阻止你扩展方法，只要方法对资源的操作有着具体的、可识别的语义即可，并能够保持整个接口的统一性。

+ 统一资源接口对URl有什么指导意义？
  统一资源接口要求使用标准的`HTTP`方法对资源进行操作，所以`URl`只应该来表示资源的名称，而不应该包括资源的操作。通俗来说，`URl`不应该使用动作来描述。例如，下面是一些不符合接口要求的`URl`：
  - GET/getUser/1
  - POST/createUser
  - PUT/updateUser/1
  - DELETE/deleteUser/1
  

+ 如果`GET`请求增加计数器，这是违反安全性？
  安全性不代表请求不产生副作用，例如像很多API开发平台，都对请求流量做限制。但客户端不是为了追求副作用而发出这些`GET`或`HEAD`请求的，产生副作用是服务端自做主张的。另外，服务端在设计时，也不应该让副作用太大，因为客户端认为这些请求是不会产生副作用的。

+ 直接忽视缓存可取吗？
即使你按各个动词的原本意图来使用它们，你仍可以轻易禁止缓存机制。最简单的做法就是在你的`HTTP`响应里增加这样一个报头：`Cache-control:no-cache`。但是，同时，你也对失去了高效的缓存与再验证的支持（使用Etag等机制）。对于客户端来说，在为一个`REST`式服务实现程序客户端时，也应该充分利用现有的缓存机制，以免每次都重新获取表示。

+ 响应代码的处理有必要吗？
  HTTP的响应代码可用于应付不同场合，正确使用这些状态码意味着客户端与服务器可以在一个具备较丰富语义的层次上进行沟通。例如：201（"Created"）响应代码表明已经创建了一个新的资源，其URl在Location响应报头里。
  假如你不利用HTTP状态代码丰富的应用语义，那么你将错失提高重用性、增强互操作性和提升松耦合性的机会。

### 2.3 资源的表述

**客户端获取的只是资源的表述而已。资源在外界的具体呈现，可以有多种表述（或称为表现、表示）形式，在客户端和服务端之间传送的也是资源的表述，而不是资源本身。**例如文本资源可以采用html、xml、json等格式，图片可以使用png、或JPG展现出来。

资源的表述包括数据和描述数据的元数据，例如HTTP头`Content-Type`就时这样一个元数据属性。

那么客户端如何知道服务端提供那种表述形式哪？

答案时可以通过HTTP内容协商，客户端可以通过`Accept`头请求一种特定格式的表述，服务端则通过`Content-Type`告诉客户端资源的表述形式。

下面来看一些实践上的常见设计：

+ 在URL里面带上版本号，例如：
  http://api.example.com/1.0/foo
  http://api.example.com/1.2/foo
  **因为不同的版本可以理解成同一种资源的不同表现形式，所以应该采用同一个`URl`。**版本号可以在HTTP请求头信息的`Accept`字段中进行区分。
  Accept: vnd.example-com.foo+json; version=1.0
  Accept: vnd.example-com.foo+json; version=1.1
  Accept: vnd.example-com.foo+json; version=2.0

+ 使用`URl`后缀来区分表述格式
  像rails框架，就支持使用/users.xml或/users.json来区分不同的格式。 这样的方式对于客户端来说，无疑是更为直观，但**混淆了资源的名称和资源的表述形式**。还是应该优先使用内容协商来区分表述格式。

+ 如何处理不支持的表述格式
  当服务器不支持所请求的表述格式，它应该返回一个HTTP 406响应，表示拒绝处理该请求。

### 2.4 状态的转移

访问一个网站，就代表了客户端和服务器的一个互动过程。在这个过程中，势必涉及到数据和状态的变化。

互联网通信协议HTTP协议，是一个无状态协议。这意味着，所有的状态都保存在服务器端。因此，**如果客户端想要操作服务器，必须通过某种手段，让服务器端发生"状态转化"（State Transfer）。而这种转化是建立在表现层之上的，所以就是"表现层状态转化"**。

客户端用到的手段，只能是HTTP协议。具体来说，就是HTTP协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。它们分别对应四种基本操作：**GET用来获取资源，POST用来新建资源（也可以用于更新资源），PUT用来更新资源，DELETE用来删除资源**。

综合上面的内容，总结一下什么是**RESTful**架构：

+ **每一个`URl`代表一种资源**
+ **客户端和服务器之间，传递这种资源的某种表现层**
+ **客户端通过四个HTTP动词，对服务器资源进行操作，实现“表现层状态转移”**。

### 2.5 参考内容

+ [RESTful API 最佳实践](http://www.ruanyifeng.com/blog/2018/10/restful-api-best-practices.html)
+ [理解RESTful架构](http://www.ruanyifeng.com/blog/2011/09/restful.html)
+ [RESTful架构详解](https://www.runoob.com/w3cnote/restful-architecture.html)
