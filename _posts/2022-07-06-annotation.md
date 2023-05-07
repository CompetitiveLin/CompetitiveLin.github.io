---
title: Java注解
categories: []
tags: []
date: 2022-07-06T16:05:12+800
last_modified_at: 
pin: false
---

## @Controller 和 @RestController

@RestController 相当于 @Controller + @ResponseBody

@ResponseBody的作用在于使函数返回文本，而不是.jsp等页面

## @Transactional

相同类的不同方法之间的事务嵌套：取决于最外层方法是否有加@Transactional注解

不同类的不同方法之间的事务嵌套：取决于各自方法是否有加@Transactional注解

前提都是子事务抛出异常。