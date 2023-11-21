---
title: "使用hugo构建静态网站遇到的一些问题"
date: 2023-11-06T11:24:46+08:00
draft: false
image: images/blog/blog-post-woman.jpg
feature_image: images/blog/blank.jpg
draft: flase
author: Soluty
tag: "golang"
---

很早以前就想在网上搞个地方写一些原创的内容, 因为个人喜欢用golang, 所以选择了hugo,
当时在github和gitee上都部署了一遍, 但是后来由于996工作的原因, 弄好以后就一直没有再继续更新过博客了.


之前使用了一个最简单的模板,模板只有一个博客列表, 并将其改造成可以发布go modules. 但是现在既然想要做一个小店, 那么模板
就不太适用了. 因此重新选择了一个模板并且将应用过程中碰到的几个问题记录下来.


### 1. 部署报错

`Error: Error building site: TOCSS: failed to transform "style.scss" (text/x-scss). Check your Hugo installation; you need the extended version to build SCSS/SASS with transpiler set to 'libsass'.`

这个错误是因为有些模板使用了SASS或者SCSS作为预处理器来处理css, hugo提供了一个内置的sass预处理器,
所以最简单的解决方案是按照hugo的文档,安装`hugo extend`版本. 在hugo的网站上有下面一段内容


> Hugo is available in two editions: standard and extended.
>
> With the extended edition you can:
> - Encode to the WebP format when processing images. You can decode WebP images with either edition.
> - Transpile Sass to CSS using the embedded LibSass transpiler. The extended edition is not required to use the Dart Sass transpiler.

可以看到, 扩展的hugo版本可以编解码webp格式的图像,并且提供了libsass预处理器. 扩展版本的安装需要1.19版本的go语言和c语言的编译器gcc,安装的命令为`CGO_ENABLED=1 go install -tags extended github.com/gohugoio/hugo@latest`

### 2. 通过hugo server无法对博客进行预览

由于我现在把我的所有的开发全都迁移到了docker内部, 并且目前我的docker还没有安装浏览器,所以如果需要顺利的在宿主机预览需要进行
两处关键的设置.

- docker需要将端口映射到宿主机,hugo默认的端口为1313,所以通过docker的`-p 1313:1313`参数可以将docker映射到主机.
- hugo server直接启动的时候只绑定了127.0.0.1端口, 需要通过`hugo server --bind 0.0.0.0`绑定所有的端口.
