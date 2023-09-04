---
title: 剑指offer-day10
date: 2022-04-20 20:49:55
categories:
- 技术
tags:
- 算法
---

## 把数字翻译成字符串

有一点像青蛙跳台阶，但是递归条件有点不一样。

维护三个指针a=f(0)=1、b=f(1)=1、c = 1，遍历整个数字（为了方便遍历需要把数字转为字符串），从第二个数字开始，如果该数字和前一个数字组成的数>=10且<=25，说明这个数字的翻译方法有f(0)+f(1)种，否则只有f(1)种。c的值等于这个数的翻译方法，此时c指向该数字，a指向b，b指向c，循环至遍历结束。

```java
class Solution {
    public int translateNum(int num) {
        String s = String.valueOf(num);
        int a = 1;// 代表f(0)
        int b = 1;// 代表f(1)
        int c = 1;// 代表f(0)+f(1)，防止只有一个数的特殊情况，初始值为1
        for(int i = 1; i < s.length(); i++){
            int n = Integer.parseInt(s.substring(i-1,i+1));
            if(n >= 10 && n <= 25){// 如果遍历的数与前一个数组合符合范围，说明遍历至该数时的翻译方法有f(n-1)+f(n-2)种
                c = a + b;
            }
            else{// 不符合，明遍历至该数时的翻译方法有f(n-1)种
                c = b;
            }
            a = b;// a跳至f(n-2)
            b = c;// b跳至f(n-1)
        }
        return c;
    }
}
```

## 最长不含重复字符的子字符串

自己写了一个虽然过了，但是时空复杂度真的丢人...算是一个暴力解吧。

同样是动态规划思想，用一个哈希表来记录每个字符最后出现的位置，遍历整个字符串，获得一个字符过去出现的最后位置和现在出现的位置，相减获得重复前的子字符串长度。用一个数tmp来记录。最后返回每次记录后tmp的最大值。

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        Map<Character,Integer> map = new HashMap<>();
        int tmp = 0;
        int res = 0;
        for(int i = 0; i < s.length(); i++){
            int j = map.getOrDefault(s.charAt(i),-1);//无值则初始化map，初始值设为-1，有值则获取该字符前一次出现的位置
            map.put(s.charAt(i),i);// 修改位置至当前
            if(tmp < i - j){// 如果tmp<i-j，说明还未找到相同的字符，tmp长度+1
                tmp++;
            }
            else{// 如果tmp>=i-j，说明出现了相同的字符，tmp=i-j
                tmp = i - j;
            }
            res = Math.max(tmp,res);// 获取tmp的最大值
        }
        return res;
    }
}
```

