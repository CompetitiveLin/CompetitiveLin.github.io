---
title: &title Github删除Commits记录
excerpt: 众所周知，已经Push到Github的Commits是不能撤销的。但是，我们可以通过创建分支并修改默认分支的方法让Repo回退至某个版本并删除该版本后的所有Commits记录。
categories: ["Github"]
tags: Github Branch Commits Github_Desktop
date: 2022-03-02T13:00:12+800
header:
  overlay_image: /assets/images/unsplash-image-1.jpg
show_date: true
toc: true
toc_label: *title
toc_sticky: true
last_modified_at: 2022-03-02T16:39:43+800
---

## 1. Description

删除已Push的Commits并回退至旧版本。如下图所示，删除**红框**中的Commits记录并回退至**箭头**所指的版本。

[![](/assets/images/2-1.png){: .align-center}](/assets/images/2-1.png)

Revert Commits也能回退版本。但是其不同之处在于，Revert Commits会保留所有的Commits。

## 2. Solution

本方法是在Windows的Github Desktop中操作的。但不管在哪个操作系统，解决该问题的思路是类似的。

首先选择要回退到的Commit并Create branch, Name branch, Publish branch. 如下三图所示：

[![](/assets/images/2-2.png){: .align-center}](/assets/images/2-2.png)

[![](/assets/images/2-3.png){: .align-center}](/assets/images/2-3.png)

[![](/assets/images/2-4.png){: .align-center}](/assets/images/2-4.png)

接着，在Github上设置默认分支[^1]，删除原分支，修改新建的分支名即可，如下三图所示：

[![](/assets/images/2-5.png){: .align-center}](/assets/images/2-5.png)

[![](/assets/images/2-6.png){: .align-center}](/assets/images/2-6.png)

[![](/assets/images/2-7.png){: .align-center}](/assets/images/2-7.png)

但是这样有个缺点：本地的代码需要删除并重新Clone，否则GitHub Desktop中会出现“混乱”。

## Reference
  
[^1]: [GitHub Docs:更改默认分支](https://docs.github.com/cn/repositories/configuring-branches-and-merges-in-your-repository/managing-branches-in-your-repository/changing-the-default-branch)