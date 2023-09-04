---
title: 剑指offer-day8
date: 2022-04-18 20:33:28
categories:
- 技术
tags:
- 算法
---

## 斐波那契数列

递归经典题目，但是使用传统递归会超时，需要使用优化的记忆递归。

```java
class Solution {
    public int fib(int n) {
        // 超出时间限制
        // if(n == 0 || n == 1){
        //     return n;
        // }
        // return fib(n-1) + fib(n-2);
        int a = 0;// a表示f(0)
        int b = 1;// b表示f(1)
        int sum = 0;
        for(int i = 0; i < n; i++){
            sum = (a + b) % 1000000007;// sum表示f(2) = f(0) + f(1)
            a = b;// a向前移动至f(1)
            b = sum;// b向前移动至f(2)
        }
        return a;// 循环结束，a=f(n)
    }
}
```

## 青蛙跳台阶

其实就是斐波那契数列，只要记住上一题的解法，这道题就是一模一样，只是初始条件改变了。

```java
class Solution {
    public int numWays(int n) {
        int a = 1;// f(0) = 1
        int b = 1;// f(1) = 1，f(2) = f(0) + f(1) = 2
        int sum = 0;
        for(int i = 0; i < n; i++){
            sum = (a+b) % 1000000007;
            a = b;
            b = sum;
        }
        return a;
    }
}
```



## 股票的最大利润

这道题用动态规划没有想出来，看了答案才发现原来这么淳朴...大体思路是遍历整个数组，记录一个最低价，如果后面有更低的价格就更新最低价；没有的话就计算差值，差值最大即为答案。

```java
class Solution {
    public int maxProfit(int[] prices) {
        int cost = Integer.MAX_VALUE;
        int profit = 0;
        for(int price : prices){
            cost = Math.min(price,cost);// 更新最低价
            profit = Math.max(profit,price - cost);// 更新最大差值
        }
        return profit;
    }
}
```

