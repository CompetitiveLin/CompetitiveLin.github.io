---
title: Python实现点击图片获取HSV或BGR的值
categories: [Script, Python]
tags: [python, hsv, bgr, opencv]
date: 2022-03-04T13:35:49+800
last_modified_at: 2022-03-08T00:47:45+800
pin: false
---

单击图片即可得到HSV或BGR的值，代码如下：

```python
import cv2

def getposHsv(event, x, y, flags, param):
    if event == cv2.EVENT_LBUTTONDOWN:
        print("HSV is", HSV[y, x])

def getposBgr(event, x, y, flags, param):
    if event == cv2.EVENT_LBUTTONDOWN:
        print("BGR is", image[y, x])

image = cv2.imread('frame_B.jpg')
HSV = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
cv2.imshow("imageHSV", HSV)
cv2.imshow('image', image)
cv2.setMouseCallback("imageHSV", getposHsv)
cv2.setMouseCallback("image", getposBgr)
cv2.waitKey(0)
```
