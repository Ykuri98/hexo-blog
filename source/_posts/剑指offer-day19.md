---
title: 剑指offer-day19
date: 2022-04-25 21:22:47
categories:
- 技术
tags:
- 算法
---

## 求1+2+...+n

感觉本质上是一道脑筋急转弯题目，因为迭代和递归的方式都被锁死了，只能另辟蹊径完成，答案用的是逻辑运算符的短路，也见到有人用异常的，我没有想出来，就以答案为标准吧。

```java
class Solution {
    int res = 0;
    public int sumNums(int n) {
        // 运用逻辑运算符短路，递归至n=1时发生短路，后面的递归计算终止
        boolean x = n > 1 && sumNums(n-1) > 0;
        res += n;
        return res;
    }
}
```

## 二叉搜索树的最近公共祖先

自己想出来了递归，答案给的最优解是迭代，但是实际实验过发现两者并没有差别，那就放自己的答案吧。

因为是二叉搜索树，可以利用搜索树的特性，判断两个节点是否在一侧，在哪一侧则往哪边遍历，否则就返回当前的根节点。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        // 在左子树往左遍历，在右子树往右遍历
        if(root.val > p.val && root.val > q.val){
            return lowestCommonAncestor(root.left,p,q);
        }
        else if(root.val < p.val && root.val < q.val){
            return lowestCommonAncestor(root.right,p,q);
        }
        // 两边各一个返回当前根节点
        return root;
    }
}
```

## 二叉树的最近祖先

相比上题少了一个搜索树的条件，显然变难了，自己写的是递归+遍历，有很高的时间复杂度，最优解只使用了递归。

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        // 递归终止条件：root为空，或root为p、q中的一个
        if(root == null || root == p || root == q) return root;
        // 递归遍历根节点的左右子树，返回null、p或q
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        // left为空表示p、q都在右子树，返回右子树的根节点，反之同理
        if(left == null) return right;
        if(right == null) return left;
        // 两者都不为空表示p、q在异侧，直接返回根节点
        return root;
    }
}
```

