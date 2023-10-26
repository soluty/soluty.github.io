---
title: "如何搭建自己域名的go模块"
date: 2023-10-26T19:46:18+08:00
draft: false
tag: "golang"
---

### 为什么不直接用github而需要搭建自己域名的go模块

从golang1.13以后，go mod基本上已经成为了go语言模块化的标准，越来越多的go项目都开始对go mod进行支持，及大的方便了go语言的应用开发以及代码共享。
有很多的开源项目都放在github上面做成go语言的模块，可以很方便的通过`go get github.com/*/*`来引用，但是还有一些情况下让我们没办法这样使用github来分享模块：

1. 如果是不开源的公司项目，恐怕没法分享到github上去，一般50人左右的公司，内网搭建一个小型的git服务是最方便的。
2. 即使是个人开源的项目，也不一定非要通过github开源，首先github在国内某些网络环境下访问龟速，比如长宽这种，我家里图便宜用长宽，在github上下包的速度都是10k起往下降，甚至一度会断掉，我最试过长下一个67m的picgo，花了我3个小时。
3. 绑定在个人域名下的项目，个人有极大的控制权，万一某gayhub被强了，或者某些代码库发疯封了你的代码（参考nodejs left-pad事件），你可以很方便的重建而不影响你的代码使用的客户。

### 怎么做

其实很简单，只需要在和你的模块名同名的网页的html的head标签中加上下面一句话即可。

`<meta name="go-import" content="${mod} git ${modurl}">`

例如我有一个日志模块`soluty.cc/xlog`，模块代码托管在gitee上，url为`https://gitee.com/soluty/xlog`。
那么就需要在页面`https://soluty.cc/xlog`的head标签中加上
`<meta name="go-import" content="soluty.cc/xlog git https://gitee.com/soluty/xlog">`

一定要注意，你的页面必须为https的，不要用http，不要用http，不要用http。
