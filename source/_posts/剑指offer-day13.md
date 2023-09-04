---
title: 剑指offer-day13
date: 2022-04-21 14:14:23
categories:
- 技术
tags:
- 算法
---

## 调整数组顺序

一开始是想的最简单的方法，用两个数组来记录奇数和偶数，遍历原数组一遍就行，但是明显不是最优解。最优解是用双指针从数组两边往中间遍历，头指针记录奇数，尾指针记录偶数，如果头指针遇到偶数就停止，转而遍历尾指针；尾指针遇到奇数就跟头指针的偶数交换，遍历直至两指针相遇。

```java
class Solution {
    public int[] exchange(int[] nums) {
        int i = 0;
        int j = nums.length - 1;
        while(i < j){// 遍历直至两指针相遇
            while(nums[i] % 2 != 0 && i < j){// 头指针遇到偶数停止
                i++;
            }
            while(nums[j] % 2 == 0 && i < j){// 尾指针遇到奇数停止
                j--;
            }
            int tmp = nums[i];// 交换
            nums[i] = nums[j];
            nums[j] = tmp;
        }
        return nums;// 返回原数组
    }
}
```

## 和为s的两个数字

还是双指针...用一个头指针和一个尾指针指向数组两端，求两个指针所指元素的和，大于目标值尾指针向前移，反之头指针向后移，直至等于目标值，返回两个指针指向的元素。

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int i = 0;// 维护一个头指针和一个尾指针
        int j = nums.length - 1;
        while(i < j){// 如果头指针超过尾指针，推出循环
            while(nums[i] + nums[j] < target && i < j){// 小于target，头指针后移
                i++;
            }
            while(nums[i] + nums[j] > target && i < j){// 大于target，尾指针前移
                j--;
            }
            if(nums[i] + nums[j] == target){// 等于target，退出循环
                break;
            }
        }
        int[] res = {nums[i],nums[j]};
        return res;
    }
}
```

## 翻转单词顺序

一开始是用split()方法来分割字符串的，虽然写出来了但是时空复杂度不好，看了答案也发现并不推荐这种做法，利用双指针会更好。

用两个指针都指向string的尾部，前指针i遍历至空格，与后指针j一起获取了第一个不含空格的子字符串，用一个StringBuffer接住该字符串，i则继续遍历，不过此时是遍历至第一个字符，再将j移到此处，重复至遍历完成。

```java
class Solution {
    public String reverseWords(String s) {
        s.trim();// 将s头尾的空格去掉
        int i = s.length() - 1;// 维护两个指针
        int j = i;
        StringBuffer sb = new StringBuffer();
        while(i >= 0){
            while(i >= 0 && s.charAt(i) != ' '){// 找到第一个空格
                i--;
            }
            sb.append(s.substring(i+1,j+1) + " ");// 获取并添加子字符串+" "
            while(i >= 0 && s.charAt(i) == ' '){// 找到第一个字符
                i--;
            }
            j = i;// 后指针指向第一个字符
        }
        return sb.toString().trim();// 完成后尾部会多出一个空格，除去空格
    }
}
```

