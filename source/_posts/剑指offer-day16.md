---
title: 剑指offer-day16
date: 2022-04-23 17:05:05
categories:
- 技术
tags:
- 算法
---

## 把数组排成最小的数

看到题目就想到自然排序了，但是直接使用是不可以的，正确的排序方式应该是看两字符串x和y，是否满足x+y<y+x（比如3和30，如果按默认的字典序排列应该是3在前，但是两者的组合330明显大于303，所以应该是看是否满足330<303,不满足则30排在前面）

```java
class Solution {
    public String minNumber(int[] nums) {
        StringBuffer res = new StringBuffer();
        String[] s = new String[nums.length];
        for(int i = 0; i < nums.length; i++){// 将int转为String
            s[i] = String.valueOf(nums[i]);
        }
        Arrays.sort(s, (s1,s2) ->  (s1+s2).compareTo(s2+s1));// 实现comparable接口的自然排序
        for(String str : s){
            res.append(str);
        }
        return res.toString();
    }
}
```

## 扑克牌的顺子

一开始没有很好的想法，就放弃看答案了。

```java
class Solution {
    public boolean isStraight(int[] nums) {
        int zeroCount = 0;
        Arrays.sort(nums);// 对数组排序
        for(int i = 0; i < nums.length - 1; i++){
            if(nums[i] == 0){
                zeroCount++;// 记录大小王的个数，在0-2之间
            }
            else if(nums[i] == nums[i+1]){// 出现相等的数表示无法成为顺子
                return false;
            }
        }
        return nums[nums.length-1] - nums[zeroCount] < 5;// 用最大的数减去最小的非0数，如果差值小于5说明可以成为顺子
    }
}
```

