---
title: 剑指offer-day4
date: 2022-04-11 22:46:37
categories:
- 技术
tags:
- 算法
---

## 数组中重复的数字

最简单的思路当然是使用哈希表来记录重复数，老老实实的写了。

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        Map<Integer,Integer> map = new HashMap<>();
        for(int i : nums){
            if(map.containsKey(i)){
                map.put(i,map.get(i)+1);
            }
            else{
                map.put(i,1);
            }
        }
        int res = 0;
        for(int i : nums){
            if(map.get(i) > 1){
                res = i;
                break;
            }
        }
        return res;
    }
}
```

结果发现这时空复杂度也太高了...愤而看官方解，发现它也是这么写的...只不过优化了一下过程。

发现自己两年前居然做过这道题，而且时空复杂度都比我现在的优秀，看了一眼：

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        Arrays.sort(nums);
        for(int i = 1; i < nums.length;i++){
            if(nums[i] == nums[i-1])
                return nums[i];
        }
        return 0;
    }
}
```

...也不知道是聪明还是偷懒，利用了题目中

> 长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内

的条件，自作聪明地用排序来解决问题，不过这应该也算是另辟蹊径吧...

但这道题的最优解还真是利用了这个条件，因为数组内的数不会超过下标值，所以可以利用值对应下标的方法来检测是否有重复，即数字0交换至对应下标0，以此类推，直到交换值和被交换的值相等，说明这个值是重复值。

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        int i = 0;
        while(i < nums.length) {
            if(nums[i] == i) {// 如果值一开始就对应，则跳过该次循环
                i++;
                continue;
            }
            if(nums[nums[i]] == nums[i]) return nums[i];// 交换值和被交换的值相等，说明这个值是重复值
            int tmp = nums[i];// 交换两个值
            nums[i] = nums[tmp];
            nums[tmp] = tmp;
        }
        return -1;
    }
}
```

## 在排序数组中查找数字 Ⅰ

遍历的方法最简单也是最low的，放一个二分法的答案吧。

```java
class Solution {
    public int search(int[] nums, int target) {
        // 搜索右边界 right
        int i = 0, j = nums.length - 1;
        while(i <= j) {
            int m = (i + j) / 2;
            if(nums[m] <= target) i = m + 1;
            else j = m - 1;
        }
        int right = i;
        // 若数组中无 target ，则提前返回
        if(j >= 0 && nums[j] != target) return 0;
        // 搜索左边界 right
        i = 0; j = nums.length - 1;
        while(i <= j) {
            int m = (i + j) / 2;
            if(nums[m] < target) i = m + 1;
            else j = m - 1;
        }
        int left = j;
        return right - left - 1;
    }
}
```

## 0~n-1中缺失的数字

想了很久的二分，但是跳出条件总是写不出，怒而写出暴力解，直接遍历查询。

跟上题一样，还是放出二分的答案吧。

```java
// 将数组分为两部分：nums[i] == i，nums[i] != i
// 用i记录前一数组的最后一个元素，j记录后一数组的第一个元素
// 以i <= j来作为跳出条件
class Solution {
    public int missingNumber(int[] nums) {
        int i = 0, j = nums.length - 1;
        while(i <= j) {
            int m = (i + j) / 2;
            if(nums[m] == m) i = m + 1;
            else j = m - 1;
        }
        return i;
    }
}
```

感觉静不下心来想二分，都是怎么简单怎么来了，看来得好好练一下二分。
