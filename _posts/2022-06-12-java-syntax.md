---
title: JAVA知识点记录博客
categories: [JAVA]
tags: [java, syntax]
date: 2022-06-12T23:14:04+800
last_modified_at: 
pin: true
---

# 容器

![](/images/posts/2022-06-19-22-51-30.png)

[HERE](https://zhuanlan.zhihu.com/p/29421226)

```java

Deque<Integer> stack = new LinkedList<>();  // stack

Queue<String> queue = new LinkedList<>();   // queue

PriorityQueue<Integer> queue = new PriorityQueue<>();   // priority queue

Map<String, Object> map = new HashMap<>();  // map



```


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

# length为属性，length()为方法

- **数组属性**：length
- **字符串方法**：length()
- **集合方法**：size()

# 红黑树的时间复杂度为: O(logn)

一棵含有n个节点的红黑树的高度至多为2log(n+1).See [Here](https://www.cnblogs.com/skywang12345/p/3245399.html).


# 同步容器

同步容器主要包括2类：

- Vector、Stack、HashTable

- Collections类中提供的静态工厂方法创建的类

同步容器的所有操作并不都是线程安全的。[HERE](https://blog.csdn.net/qq1169091731/article/details/83067124)

所谓“线程安全”，并不包括**多个操作之间**的“原子性”支持。

100%的情况下不要用 StringBuffer

99% 的情况下不要用 Vector

那么那剩下的 1% 用 Vector 的情况在哪呢？

熟悉Swing的都知道： 现有的一些 model 的类里面用了 Vector，假如你去定制它们，有时不可避免要用到 Vector。


