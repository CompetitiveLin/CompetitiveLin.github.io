---
title: C++中vector的初始化及赋值方式
categories: [Syntax, C++]
tags: [c++, vector]
date: 2022-03-17T19:39:44+800
last_modified_at: 
pin: false
---

## 一维向量

### 1. 不带参数的构造函数初始化

```c++
//初始化一个size为0的vector
vector<int> abc;
```

### 2. 带参数的构造函数初始化

```c++
//初始化size,但每个元素值为默认值
vector<int> abc(10);        //初始化了10个默认值为0的元素
//初始化size,并且设置初始值
vector<int> cde(10, 1);    //初始化了10个值为1的元素
```


### 3. 通过数组地址初始化

```c++
int a[5] = {1, 2, 3, 4, 5};
//通过数组a的地址初始化，注意地址是从0到5（左闭右开区间）
vector<int> b(a, a + 5);
```

### 4. 通过同类型的vector初始化（深拷贝，即不共享数据）

```c++
vector<int> a(5, 1);
//通过a初始化
vector<int> b(a);
```


### 5. 通过insert初始化

```c++
//insert初始化方式将同类型的迭代器对应的始末区间（左闭右开区间）内的值插入到vector中
vector<int> a(6, 6);
vecot<int> b;
//将a[0]~a[2]插入到b中，b.size()由0变为3
b.insert(b.begin(), a.begin(), a.begin() + 3);
```

insert也可通过数组地址区间实现插入
```c++
int a[6] = {6, 6, 6, 6, 6, 6};
vector<int> b;
//将a的所有元素插入到b中
b.insert(b.begin(), a, a + 6);
```
此外，insert还可以插入m个值为n的元素
```c++
//在b开始位置处插入3个6
b.insert(b.begin(), 3, 6);
```

### 6. 通过copy函数赋值

```c++
vector<int> a(5, 1);
int a1[5] = {2, 2, 2, 2, 2};
vector<int> b(10);

/*将a中元素全部拷贝到b开始的位置中,注意拷贝的区间为a.begin() ~ a.end()的左闭右开的区间*/
copy(a.begin(), a.end(), b.begin());

//拷贝区间也可以是数组地址构成的区间
copy(a1, a1 + 5, b.begin() + 5);
```




## 二维向量

### 1. 向量 + 向量（常用方法）

`vector<vector<int> > myv(row, vector<int>(column,0));`

```c++
vector<vector<int> > myv(5, vector<int>(10,0)); // 两个维度都是向量
cout<<myv.size()<<endl; // 正确，因为向量有size()函数

vector<int> temp(2);
myv.push_back(temp);    // 正确，可以将向量插入到向量中
```
**或者：**
```c++
vector<vector<int> > myv(5);    // 两个维度都是向量，只是第二个维度的向量长度为0
cout<<myv.size()<<endl;         // 5
cout<<myv[0].size()<<endl;      // 0
```

### 2. 数组 + 向量（不建议）

```c++
vector<int> myv[n]; // 此时第一维是数组，第二维才是向量。
// cout<<myv.size()<<endl; // 错误，因为数组没有size()函数

vector<int> temp(2);
myv.push_back(temp);    // 错误，无法将向量插入到数组中
```

