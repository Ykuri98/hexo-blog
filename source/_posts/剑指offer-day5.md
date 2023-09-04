---
title: 剑指offer-day5
date: 2022-04-13 16:06:44
categories:
- 技术
tags:
- 算法
---

## 二维数组中的查找

一开始就想到了线性查找，但是是从左上方开始的，发现判定条件不好写后看了看答案，才知道从右上方开始会比较方便（即大于查找值向左移，小于查找值向下移）

提交几次都发现会有边界溢出的情况，无奈只能跟答案一一对照，发现答案事先用了一个int值接住了要判断的值，这样就不会发生边界溢出了。

```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        if(matrix.length == 0){
            return false;
        }
        int m = 0;// 行数
        int n = matrix[0].length - 1;// 列数
        while(n >=0 && m < matrix.length){
            int num = matrix[m][n];// 用一个int表示当前值，防止边界溢出
            if(num == target){
                return true;
            }
            if(num > target){
                n--;
            }
            if(num < target){
                m++;
            }
        }
        return false;
    }
}
```

## 旋转数组的最小数字

不想多说了，就是二分法，重点是怎么设定跳出条件，不然就是寄（是的，我又没做出来）

```java
// 将数组划分成旋转的和未旋转的
// 左指针指向未旋转数组的最后一个元素，右指针指向旋转数组的第一个元素
class Solution {
    public int minArray(int[] numbers) {
        int i = 0, j = numbers.length - 1;
        while (i < j) {
            int m = (i + j) / 2;
            if (numbers[m] > numbers[j]) i = m + 1;// 说明m还在未旋转数组中，左指针指向m的下一个元素
            else if (numbers[m] < numbers[j]) j = m;// 说明m在旋转数组中，右指针指向m
            else j--;// m正好指向与旋转数组第一个元素相等的元素，右指针左移至与左指针重合
        }
        return numbers[i];
    }
}
```

## 第一个只出现一次的字符

一开始就把题目想错了，以为类似为**`"aadadaad"`**的字符应该返回为空，但是实际上是返回a（a、d两两配对，第一个只剩一个的字母是a）

这道题应该是用哈希表解决的，放出代码。

```java
class Solution {
    public char firstUniqChar(String s) {
        HashMap<Character, Boolean> dic = new HashMap<>();
        char[] sc = s.toCharArray();
        for(char c : sc)
            dic.put(c, !dic.containsKey(c));// 没有则修改为true，有则改为false
        for(char c : sc)
            if(dic.get(c)) return c;
        return ' ';
    }
}
```

