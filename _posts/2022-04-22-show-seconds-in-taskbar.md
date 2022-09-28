---
title: Windows任务栏时间显示秒
categories: [Tutorial, Windows 10]
tags: [skills, windows 10]
date: 2022-04-22T18:17:37+800
last_modified_at: 
pin: false
---


1. 使用 win + r 打开运行，再输入 `regedit`

2. 在打开的注册表中定位至`HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced`

3. 新建 **DWORD(32位)值**，命名为`ShowSecondsInSystemClock`，将其数值置为 `1` 并保存。

4. 打开**任务管理器**重新启动**Windows资源管理器**即可。