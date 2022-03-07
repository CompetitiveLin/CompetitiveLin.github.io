---
title: Windows配置Jekyll相关环境
categories: [Tutorial, Jekyll]
tags: [getting started, windows, installation]
date: 2022-03-01T21:40:53+800
last_modified_at: 2022-03-07T20:55:16+800
pin: false
---

## 1. Install Ruby
在Windows上使用RubyInstaller安装比较方便，在[Ruby官网][Ruby]下载最新版本的RubyInstaller **WITH DEVKIT**。注意32位和64位版本的区分。

![](/images/posts/1-1.png)

安装：使用默认路径即可，避免出错；勾选添加到PATH，就不用手动添加环境变量了

![](/images/posts/1-2.png)

安装完成如图：

![](/images/posts/1-3.png)

这里需要勾选`Run 'ridk install'`，在弹出来的安装界面中选择3，安装`MSYS2 and MINGW development toolchain`：

![](/images/posts/1-4.png)

## 2. Install RubyGems

![](/images/posts/1-5.png)

在[RubyGems官网][RubyGems]下载ZIP格式的安装包，下载后解压到任意路径。进入解压目录，在终端输入命令：`ruby setup.rb` 或者直接双击`setup.rb`文件即可。

## 3. Install Jekyll

打开cmd输入以下命令并等待安装完成即可。

```console
gem install jekyll
gem install jekyll-paginate
gem install bundler
```

## 4. Check installation

```console
jekyll -v
bunlder -v
```

输出版本信息则代表安装没问题。


## 5. Create a repo

安装完成，我们可以用jekyll命令创建一个博客模板，进入一个目录，打开命令行执行：

```console 
jekyll new testblog
cd testblog
jekyll serve
```

**大功告成！**

## PS：遇到的问题

### A. cannot load such file -- webrick (LoadError)

问题描述：执行 `bundle exec jekyll serve` 时出现 `cannot load such file -- webrick (LoadError)` 错误，如下图所示

![](/images/posts/1-6.png)

解决方法：终端输入`bundle add webrick`.See [here][webrick].

## Reference
  
  1. [windows安装jekyll](https://www.cnblogs.com/mingyue5826/p/11533978.html)
  2. [搭建个人博客：Jekyll + Github Pages + VSCode](https://zjpzhao.github.io/posts/jekyll-githubpages/)

[webrick]: https://github.com/jekyll/jekyll/issues/8523
[Ruby]: https://rubyinstaller.org/downloads/
[RubyGems]: https://rubygems.org/pages/download