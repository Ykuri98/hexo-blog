---
title: 剑指offer-day17
date: 2022-04-23 17:05:12
categories:
- 技术
tags:
- 算法
---

## 最小的k个数

非常简单的题目，如果利用Arrays.sort()基本等于白给，可是这就是明着考你排序算法的。我自己手动写了一个快排，虽然还是不太熟悉，多写写就好了。

最优解是利用快排的特点，在基准值左边的数都小于基准值，当基准值的下标为k时，左边则是答案了。

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if (k >= arr.length){// 特殊值返回：如果k大于数组的长度，直接返回数组
        	return arr;  
        } 
        return quickSort(arr, k, 0, arr.length - 1);
    }
    
    private int[] quickSort(int[] arr, int k, int l, int r) {
        int i = l, j = r;// 定义两个指针指向数组的头和尾
        while (i < j) {
            while (i < j && arr[j] >= arr[l]) j--;// 找到基准值右边小于基准值的值
            while (i < j && arr[i] <= arr[l]) i++;// 找到基准值左边大于基准值的值
            swap(arr, i, j);// 交换值
        }
        swap(arr, i, l);// 交换头/尾指针和基准值指针，使得基准值左边都小于基准值，右边都大于基准值
        if (i > k) return quickSort(arr, k, l, i - 1);// 如果基准值在k的右边，则对基准值左边的数组进行快排
        if (i < k) return quickSort(arr, k, i + 1, r);// 如果基准值在k的左边，则对基准值右边的数组进行快排
        return Arrays.copyOf(arr, k);// 返回0-(k-1)的下标的数组
    }
    private void swap(int[] arr, int i, int j) {// 交换方法
        int tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }
}
```

## 数据流中的中位数

看完题目我还嗤之以鼻，这也叫hard题？刷刷写完一提交，过是过了，跑了1600ms...

看了最优解，用到了大根堆和小根堆的知识，代码是不一定看得懂了，只能说尽力而为吧。

```java
class MedianFinder {
    Queue<Integer> A, B;
    public MedianFinder() {
        A = new PriorityQueue<>(); // 小顶堆，保存较大的一半
        B = new PriorityQueue<>((x, y) -> (y - x)); // 大顶堆，保存较小的一半
    }
    public void addNum(int num) {
        if(A.size() != B.size()) {// 插入数量为奇数，
            A.add(num);
            B.add(A.poll());
        } else {
            B.add(num);
            A.add(B.poll());
        }
    }
    public double findMedian() {
        return A.size() != B.size() ? A.peek() : (A.peek() + B.peek()) / 2.0;// 如果两堆容量相等，说明此时为偶数，各取堆顶值/2；反之说明为奇数，只取小顶堆中的值。
    }
}
```

