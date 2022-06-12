---
title: JAVA知识点记录博客
categories: [JAVA]
tags: [java, syntax]
date: 2022-06-12T23:14:04+800
last_modified_at: 
pin: true
---

# JAVA只有值传递，没有引用传递

SEE [HERE](http://www.itwanger.com/java/2020/01/03/java-string-reference-pass.html)

**赋值是给变量绑定一个新对象，而不是改变对象。**

举例：

```java
public static void main(String[] args) {
    String x = new String("沉默王二");
    change(x);
    System.out.println(x);  // "沉默王二"
}

public static void change(String x) {
    x = "沉默王三";
}
```

**直接改变对象内容。**

举例：

```java
public static void change(A a) {
    a.name = "bbb";
}
public static void main(String[] args) {
    A a = new A();
    a.name = "aaa";
    change(a);
    System.out.println(a.name); // "bbb"
}
```


