---
title: Ubuntu中关闭摄像头的自动曝光
categories: [Ubuntu, Camera]
tags: [camera, auto exposure]
date: 2022-05-21T13:31:32+800
last_modified_at: 
pin: false
---

## 目的

通过Python代码关闭杰锐微通摄像头的**自动曝光**功能。

## 尝试

查阅相关资料，有网友提出使用以下代码：
```python
capture.set(cv2.CAP_PROP_AUTO_EXPOSURE, 0.25)
```
**失败**。


## 解决方案

先通过在终端输入`v4l2-ctl --list-devices`得到摄像头列表。

![](/images/posts/2022-05-21-14-15-21.png)

接着输入`v4l2-ctl -d /dev/video2 --all`查看单个摄像头的参数。

![](/images/posts/2022-05-21-13-20-16.png)

发现最后几行中的`exposure_auto`的默认值为3，正好代表着**光圈优先**。将其设置为1，代表**手动模式**

因此在代码修改为
```python
capture.set(cv2.CAP_PROP_AUTO_EXPOSURE, 1)
capture.set(cv2.CAP_PROP_EXPOSURE, 50)
```
即可。

**曝光值参数可根据环境自主调节。**