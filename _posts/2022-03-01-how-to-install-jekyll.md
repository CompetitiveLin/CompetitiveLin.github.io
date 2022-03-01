---
title: &title 如何在Windows安装Jekyll
excerpt: 分为安装Ruby、安装RubyGems、安装Jekyll三步。
tagline: 
categories: ["Jekyll Tutorial"]
tags: Jekyll Tutorial Windows
date: 2022-03-01T21:40:53+800
header:
  overlay_image: /assets/images/unsplash-image-1.jpg
show_date: true
toc: true
toc_label: *title
toc_sticky: true
last_modified_at:
---
## 1. Install Ruby
在Windows上使用RubyInstaller安装比较方便，在[Ruby官网](https://rubyinstaller.org/downloads/)下载最新版本的RubyInstaller **WITH DEVKIT**。注意32位和64位版本的区分。

[![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/1-1.png){: .align-center}]({{ site.url }}{{ site.baseurl }}/assets/images/1-1.png)

安装：使用默认路径即可，避免出错；勾选添加到PATH，就不用手动添加环境变量了

[![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/1-2.png){: .align-center}]({{ site.url }}{{ site.baseurl }}/assets/images/1-2.png)

安装完成如图：

[![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/1-3.png){: .align-center}]({{ site.url }}{{ site.baseurl }}/assets/images/1-3.png)

这里需要勾选`Run 'ridk install'`，在弹出来的安装界面中选择3，安装`MSYS2 and MINGW development toolchain`：

[![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/1-4.png){: .align-center}]({{ site.url }}{{ site.baseurl }}/assets/images/1-4.png)

## 2. Install RubyGems

[![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/1-5.png){: .align-center}]({{ site.url }}{{ site.baseurl }}/assets/images/1-5.png)

在[RubyGems官网](https://rubygems.org/pages/download)下载ZIP格式的安装包，下载后解压到任意路径。进入解压目录，在终端输入命令：`ruby setup.rb` 或者直接双击`setup.rb`文件即可。

## 3. Install Jekyll

打开cmd输入以下命令并等待安装完成即可。

```
gem install jekyll
gem install jekyll-paginate
gem install bundler
```

## 4. Check installation

```
jekyll -v
bunlder -v
```

输出版本信息则代表安装没问题。


## 5. Create a repo

安装完成，我们可以用jekyll命令创建一个博客模板，进入一个目录，打开命令行执行：

```
jekyll new testblog
cd testblog
jekyll serve
```

**大功告成！**

## PS：遇到的问题

### A. cannot load such file -- webrick (LoadError)

问题描述：执行 `bundle exec jekyll serve` 时出现 `cannot load such file -- webrick (LoadError)` 错误，如下图所示

[![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/1-6.png){: .align-center}]({{ site.url }}{{ site.baseurl }}/assets/images/1-6.png)

解决方法：终端输入`bundle add webrick`.See [here](https://github.com/jekyll/jekyll/issues/8523).

### B.


## Reference
  
  1. [windows安装jekyll](https://www.cnblogs.com/mingyue5826/p/11533978.html)
  2. [搭建个人博客：Jekyll + Github Pages + VSCode](https://zjpzhao.github.io/posts/jekyll-githubpages/)