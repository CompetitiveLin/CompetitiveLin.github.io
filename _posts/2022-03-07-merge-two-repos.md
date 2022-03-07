---
title: Github合并两个不同的仓库
categories: [Tutorial, Github]
tags: [github, branch, commits, git]
date: 2022-03-07T21:55:10+800
last_modified_at: 2022-03-08T00:47:45+800
pin: false
---

# Description

两个独立的仓库A、B，将仓库B合并至仓库A的分支，并保留A、B的所有commits

例如：将[dumped-CompetitiveLin.github.io][dumped]中的所有提交内容合并至[CompetitiveLin.github.io][merged]的another分支。

# Solution

## 1. 克隆主仓库代码

```console
git clone git@github.com:CompetitiveLin/CompetitiveLin.github.io
```

## 2. 添加需要合并远程仓库

```console
git remote add base git@github.com:CompetitiveLin/dumped-CompetitiveLin.github.io
```

此时查看remote: `git remote -v`，如下图所示：

![](/images/posts/4-1.png)

如果需要删除remote: `git remote rm base`

## 3. 把base远程仓库中数据抓取到本仓库

```console
git fetch base
```

## 4. 切换到base分支上，命名为dumped

```console
git checkout -b another base/dumped
```

查看branch分支：`git branch`

## 5. 切换到main分支

```console
git checkout main
```

## 6. 合并两个分支

```console
git merge another
```

此时可能出现类似下图`fatal: refusing to merge unrelated histories`的报错信息。

![](/images/posts/4-2.png)

解决方法：

```console
git merge another --allow-unrelated-histories
```

在合并时有可能两个分支对同一个文件都做了修改，这时需要解决冲突[^1]，在windows下可以使用Github Desktop解决冲突问题。

## 7. 提交

```console
git push origin another
```

# Problems

## A. Permission denied (publickey)

  原因：没有配置ssh-key，没有权限[^2]

  解决方法：
  
  ```console
  ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
  clip < ~/.ssh/id_rsa.pub
  ```

  See [here][ssh-key].

# Reference

  [^1]: [在 GitHub 上解决合并冲突](https://docs.github.com/cn/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts/resolving-a-merge-conflict-on-github)
  [^2]: [Permission denied (publickey)](https://docs.github.com/en/authentication/troubleshooting-ssh/error-permission-denied-publickey)

[ssh-key]: https://stackoverflow.com/questions/2643502/git-how-to-solve-permission-denied-publickey-error-when-using-git
[dumped]: https://github.com/CompetitiveLin/dumped-CompetitiveLin.github.io
[merged]: https://github.com/CompetitiveLin/CompetitiveLin.github.io