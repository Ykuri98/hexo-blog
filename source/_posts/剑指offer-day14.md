---
title: 剑指offer-day14
date: 2022-04-21 22:14:31
categories:
- 技术
tags:
- 算法
---

## 矩阵中的路径

涉及到图的范围了，只能说是力所不能及，直接放答案吧，主要是DFS的思想怎样用代码表现出来。

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        char[] words = word.toCharArray();
        for(int i = 0; i < board.length; i++){
            for(int j = 0; j < board[0].length; j++){
                if(dfs(board,words,i,j,0)){// 遍历找到第一个等于字符串头的字母，开始DFS
                    return true;
                }
            }
        }
        // 没有找到或者没有路径，返回false
        return false;
    }

    public boolean dfs(char[][] board, char[] word, int i, int j, int k){
        // 终止条件有：遍历时超出边界值；遍历的值不等于字符串中的值
        if(i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] != word[k]){
            return false;
        }
        // k为字符串的下标，如果此时k等于字符串长度，说明寻找到路径，直接返回true
        if(k == word.length - 1){
            return true;
        }
        board[i][j] = '\0';// 遍历过的地方设为空，防止遍历回来
        boolean res = (dfs(board,word,i+1,j,k+1) || dfs(board,word,i-1,j,k+1) 
                    || dfs(board,word,i,j+1,k+1) || dfs(board,word,i,j-1,k+1));// 分别遍历上下左右，只要有一个符合条件即可
        board[i][j] = word[k];// 将遍历过的地方重新赋值，防止影响下一次遍历
        return res;// 返回结果
    }
}
```

## 机器人的运动范围

跟上题有些相似，就是返回值从boolean改成了int，参考着答案跌跌撞撞地写下来了。

```java
class Solution {
    boolean[][] visited;// 定义一个boolean数组，来表示该元素是否被访问过
    public int movingCount(int m, int n, int k) {
        visited = new boolean[m][n];
        return dfs(0,0,m,n,k);// 从左上角开始遍历
    }

    public int bitSum(int m, int n){// 计算位数和，因为题目只取两位数，就方便着写
        int m1 = m % 10;
        int m2 = m / 10;
        int n1 = n % 10;
        int n2 = n / 10;
        return m1+m2+n1+n2;
    }

    public int dfs(int i,int j, int m, int n,int k){
        // 遍历失败条件：遍历超出范围；遍历的位置的位数和大于要求数；该位置早被遍历过
        if(i < 0 || i >= m || j < 0 || j >= n || bitSum(i,j) > k || visited[i][j]){
            return 0;// 返回0表示此处不是机器人能到达的地方，不能计入总数
        }
        visited[i][j] = true;// 遍历成功，将这个位置设为已访问过
        return 1+dfs(i+1,j,m,n,k)+dfs(i-1,j,m,n,k)+dfs(i,j+1,m,n,k)+dfs(i,j-1,m,n,k);// 因为成功，所以总数+1
    }
}
```

