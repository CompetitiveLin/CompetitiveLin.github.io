---
title: Kubernetes
categories: []
tags: [backend]
date: 2023-08-31T10:23:34+800
last_modified_at: 
pin: false
---

Sidecar 模式：

通常情况下一个 Pod 只包含一个容器，但是 Sidecar 模式是指为主容器**提供额外功能（例如监控）** 从而将其他容器加入到同一个 Pod 中。再例如[Istio实现sidecar自动注入](https://juejin.cn/post/6951567887664414757)。