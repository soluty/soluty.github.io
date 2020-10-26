---
title: "viper-config"
description: "gomodule"
date: 2019-05-12T12:14:34+06:00
---

#### 介绍
使用viper的配置编写和读取小工具，映射一个大的Config对象, 简化viper读取配置的编写

#### 安装
go install soluty.cc/viper-config

#### 使用说明
假设在config目录中有一个config.go文件， 其中定义一个类型为Config如下
```
type Config struct{
    A int
    B string
}
```

那么在该目录中调用viper-config config -p ${package} 即可生成config_gen_Config.go

其它地方调用生成的Init方法即可。为了方便，也可以放在go:generate中调用。


#### wishlist
1. 目前不能直接映射time.Time结构体
2. 没有封装写方法