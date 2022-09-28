---
title: Windows右键新建Markdown文件教程
categories: [Tutorial, Windows 10]
tags: [skills, windows 10]
date: 2022-09-28T21:09:55+800
last_modified_at: 
pin: false
---


# 步骤

1. 新建文本文档
2. 输入以下内容

```
Windows Registry Editor Version 5.00
[HKEY_CLASSES_ROOT\.md]
@="Typora.md"
"Content Type"="text/markdown"
"PerceivedType"="text"
[HKEY_CLASSES_ROOT\.md\ShellNew]
"NullFile"=""
```

3. 再将其后缀改为`reg`

**BINGO**

