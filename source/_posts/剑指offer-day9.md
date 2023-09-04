---
title: 剑指offer-day9
date: 2022-04-20 20:17:27
categories:
- 技术
tags:
- 算法
---

## 连续子数组的最大和

思路跟之前的股票的最大利润是一样的，遍历一次数组，如果之前遍历的值的和sum加上这次遍历的值num都小于该值，那么就将起点改为num，并用一个值res来记录每次遍历后的最大值。

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int res = Integer.MIN_VALUE;
        int sum = 0;
        for(int num : nums){
            sum += num;// 记录遍历至该值的和
            if(sum <= num){
                sum = num;// 如果总和都没有该值大，将记录的起点改为该值
            }
            res = Math.max(res,sum);// 记录每次遍历后总和间的最大值
        }
        return res;
    }
}
```

## 礼物的最大价值

相当于上题的加强版，使用一个二维数组，并且增加了一个向下还是向右的判断，但是核心思想是不变的。

遍历数组来记录每次行进的最大总和，放在这次行进的节点中。从左上角开始遍历，因为从第一行遍历和从第一列遍历都是累加的，可以做一个判断：当要遍历的值位于这两个特殊位置时，只需要获取它的左边（或上边）的和，累加至该值并替换。其他时候则需要对左边的值和上边的值进行比较，取较大的累加至该值并替换。最后输出右下角的总和。

```java
class Solution {
    public int maxValue(int[][] grid) {
        for(int m = 0; m < grid.length; m++){
            for(int n = 0; n < grid[0].length; n++){
                if(m == 0 && n == 0){// 位于起点，跳过遍历
                    continue;
                }
                if(m == 0){// 位于第一行，只累加左边的值
                    grid[m][n] += grid[m][n-1];
                }
                else if(n == 0){// 位于第一列，只累加上边的值
                    grid[m][n] += grid[m-1][n];
                }
                else{// 其他情况需要判断左边和上边的值，取较大值累加
                    grid[m][n] += Math.max(grid[m][n-1],grid[m-1][n]);
                }
            }
        }
        return grid[grid.length - 1][grid[0].length - 1];// 返回右下角值
    }
}
```

