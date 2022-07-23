---
title: 线段树（JAVA实现）
categories: []
tags: [segment tree]
date: 2022-07-05T14:13:29+800
last_modified_at: 
pin: false
---

线段树


```java
class NumArray {
    int[] segmentTree;
    int n;


    public NumArray(int[] nums) {
        n = nums.length;
        segmentTree = new int [2 * n];
        System.arraycopy(nums, 0, segmentTree, n, n);
        for(int i = n - 1; i > 0; i--){
            segmentTree[i] = segmentTree[2 * i] + segmentTree[2 * i + 1];
        }
    }
    
    public void update(int index, int val) {
        index += n;
        segmentTree[index] = val;
        index /= 2;
        while(index != 0){
            segmentTree[index] = segmentTree[2 * index] + segmentTree[2 * index + 1];
            index /= 2;
        }
    }
    
    public int sumRange(int left, int right) {
        left += n;
        right += n;
        int sum = 0;
        while(left <= right){
            if(left % 2 == 1){
                sum += segmentTree[left];
                left ++;
            }
            if(right % 2 == 0){
                sum += segmentTree[right];
                right--;
            }
            left /= 2;
            right /= 2;
        }
        return sum;
    }
}
```