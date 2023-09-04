---
title: 剑指offer-day3
date: 2022-04-09 15:57:22
categories:
- 技术
tags:
- 算法
---

## 替换空格

一看到这道题我就优雅地敲下代码

```java
class Solution {
    public String replaceSpace(String s) {
		return s.replace(" ","%20");
    }
}
```

...咳咳，大佬说过，不要成为API Coder，不然35岁就去开滴滴了。

但其实思路还是很简单，因为java的特性，字符串不能被直接修改，只能创建一个新对象，所以在空间复杂度上是怎么都干不过C++的。那么就新建一个可变字符串对象StringBuffer来接收修改后的String对象，可以最大程度优化空间。（其实可以替换为StringBulider，但是在本题中没有明显的优化）

```java
class Solution {
    public String replaceSpace(String s) {
        if(s == null || s == ""){
            return null;
        }
        StringBuilder res = new StringBuilder();
        for(int i = 0; i < s.length(); i++){
            if(s.charAt(i) == ' '){
                res.append("%20");
            }
            else{
                res.append(s.charAt(i));
            }
        }
        return res.toString();
        // return s.replace(" ","%20");
    }
}
```

最后吐槽一下，自己写的方法时空复杂度甚至还没有包装好的replace()低...这道题可能只是单纯用来折磨C++程序员的吧...

## 左旋转字符串

一看到这我又优雅地敲下代码

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        return s.substring(n, s.length()) + s.substring(0, n);
    }
}
```

没想到这么写居然是对的，而且还是最优解...

当然，我一开始并不是这么写的，而是仿造[796.旋转字符串](https://leetcode-cn.com/problems/rotate-string/)的解来写的，用一个字符串来接收两个拼接起来的目标字符串，再返回其中与目标字符串等长的结果字符串。

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        String s1 = s + s;
        return s1.substring(n,s.length() + n);
    }
}
```

空间复杂度当然没有API好，毕竟自创了一个字符串。

（今天题目挺水的，不过周末了，就放过我吧）
