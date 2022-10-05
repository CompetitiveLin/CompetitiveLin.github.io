---
title: JAVA知识点记录博客
categories: [JAVA]
tags: [java, syntax]
date: 2022-06-12T23:14:04+800
last_modified_at: 
pin: true
---

# 运算符优先级

优先级 |运算符
:-----:|:------:
1 | ( )　[ ] 　.
2 | ! 　~　 ++　 --
3 | *　 /　 %
4 | +　 -
5 | << 　>>　 <<<  >>>
6 | < 　<=　 > 　>=　 instanceof
7 | == 　!=
8 | &
9 | ^
10 | \|
11 | &&
12 | \|\| 
13 | ? :
14 | = 　+= 　-= 　*=　 /=　 %=　 &=　 \|=　 ^=　 ~= 　<<= 　>>=　 >>>=
15 | ，

总结：括号级别最高，逗号级别最低，单目 > 算术 > 位移 > 关系 > 逻辑 > 三目 > 赋值。



# 容器(集合类)

![](/images/posts/2022-06-19-22-51-30.png)

[HERE](https://zhuanlan.zhihu.com/p/29421226)

```java

Deque<Integer> stack = new ArrayDeque<>();  // stack

Deque<String> queue = new ArrayDeque<>();   // queue

PriorityQueue<Integer> queue = new PriorityQueue<>();   // priority queue

// Set

HashSet<String> unordered_set = new HashSet<>();  // 乱序，底层使用散列函数

LinkedHashSet<String> set2 = new LinkedHashSet<>(); // 以插入顺序排序

TreeSet<String> set = new TreeSet<>();  // 以字典序排序，底层使用红黑树

// Map

HashMap<String, Object> unordered_map = new HashMap<>();  // 乱序

LinkedHashMap<Integer, Integer> map = new LinkedHashMap<>();  // 以插入顺序排序

TreeMap<Integer, Integer> map = new TreeMap<>();  // 以字典序排序

TreeMap<Integer, Integer> map = new TreeMap<Integer, Integer>();  // 手动加泛型，多一些约束少一些出错。在运行期没有任何区别, java的泛型只在编译期有效。

```

**ArrayList:**

```java
public static void main(String[] args) {
    ArrayList<String> sites = new ArrayList<String>();
    sites.add("Google");
    sites.add("Runoob");
    sites.add("Taobao");
    sites.add("Weibo");
    System.out.println(sites.get(1));  // 访问第二个元素
    sites.set(2, "Wiki"); // 第一个参数为索引位置，第二个为要修改的值
    sites.remove(3); // 删除第四个元素
    Collections.sort(sites);  // 字母排序
    System.out.println(sites.isEmpty());    // 空判断
    System.out.println(sites);
    String[] arr = new String[sites.size()];    // 创建一个新的 String 类型的数组
    sites.toArray(arr); // 将ArrayList对象转换成数组
}
```

**LinkedList:**

```java

```

**HashMap:**

```java
public static void main(String[] args) {
    Map<String, Integer> numbers = new HashMap<>();
    numbers.put("One", 1);
    numbers.put("Two", 2);
    numbers.put("Three", 3);
    numbers.put("Four", 4);
    System.out.println(numbers.getOrDefault("Five", 0));
    numbers.remove("Two");
    numbers.replace("One",111);
    System.out.println(numbers);
    System.out.println(numbers.containsKey("Four"));
    System.out.println(numbers.containsValue(111));
    for (Map.Entry<String, Integer> entry : numbers.entrySet()) {
        System.out.println("key:" + entry.getKey() + ".value:" + entry.getValue());
    }
    for (String string : numbers.keySet()) {
        System.out.println("key:" + string + ".value:" + numbers.get(string));
    }
    Iterator<String> iterators = numbers.keySet().iterator();
    while (iterators.hasNext()) {
        String key = iterators.next();
        System.out.println("key:" + key + ".value:" + numbers.get(key));
    }
}
```

**ArrayDeque：**

```java
public static void main(String[] args) {
    ArrayDeque<String> animals= new ArrayDeque<>();
    /*
        *****Stack*****
    */
    animals.push("Pig");
    System.out.println("返回栈顶元素： " + animals.peek());
    System.out.println("返回栈顶元素并弹出： " + animals.pop()); 

    /*
        *****Queue*****
    */
    animals.add("Dog");
    animals.offer("Horse");
    System.out.println("返回出队元素： " + animals.peek()); 
    System.out.println("返回出队元素并弹出： " + animals.poll());
    /*
        *****Common*****
    */
    System.out.println("判断是否包含Dog： " + (animals.contains("Dog") ? "是" : "否"));
    System.out.println("转换成数组输出： " + Arrays.toString(animals.toArray()));
}
```






# 赋值和new的区别

赋值是创建常量，在编译期时就被确定了；new创建的对象不是常量，无法在编译期中确定。

![](/images/posts/2022-06-20-23-26-39.png)

```java

String s0 = "aaa" + "bbb";   // 常量，同"aaabbb"，放入常量池中。创建了1个对象
String s1 = "aaa" + new String("bbb");  //  非常量，因为new出来的字符串无法在编译期中确定。创建了4个对象
String s2 = new String("aaabbb");   // 同上。由于常量池中已经存在"aaabbb"，因此只创建了1个对象
System.out.println(s1.intern() == s0);  // true，intern()函数会在常量池里找是否相同的字符串，有则返回常量池的引用
System.out.println(s2.intern() == s0);  // true，同上
System.out.println(s0 == s1);   // false，虽然内容一样，但是new出来的地址肯定不一样
System.out.println(s1 == s2);   // false，同上
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


# StringBuilder

与StringBuffer相比，StringBuilder不是线程安全，所以单线程情况下效率更高。两者除线程安全方面之外无差别。

```java
StringBuilder sb = new StringBuilder();
sb.append("a".repeat(100)); // repeat方法用于构造重复String
```