---
title: 剑指offer-day7
date: 2022-04-16 16:26:11
categories:
- 技术
tags:
- 算法
---

## 树的子结构

大致思路是有的：通过先序遍历来遍历主树，如果有等于子树根节点的节点，再先序遍历比较。

但是实现得磕磕绊绊，没法了，进行一个答案的抄。

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
    public boolean isSubStructure(TreeNode A, TreeNode B) {// 遍历A树
        if(A == null || B == null){// A或B为空，返回false
            return false;
        }
        return (compare(A,B) || isSubStructure(A.left,B) || isSubStructure(A.right,B));
    }

    public boolean compare(TreeNode A,TreeNode B){// 比较B树
        if(B == null){// 如果先序遍历完成，返回true
            return true;
        }
        if(A == null || (A.val != B.val)){// 如果A树为空或者两数节点不相等，返回false
            return false;
        }
        return (compare(A.left,B.left) && compare(A.right,B.right));// 两数节点相等，再先序遍历比较其左节点和右节点
    }
}
```

感觉递归的结束条件确实很难想。

## 二叉树的镜像

直接摆烂看答案

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
    public TreeNode mirrorTree(TreeNode root) {
        if(root == null){// 遍历至子节点返回null
            return null;
        }
        TreeNode tmp = root.left;
        root.left = mirrorTree(root.right);
        root.right = mirrorTree(tmp);
        return root;
    }
}
```

可以发现这道题目其实跟交换数字的思路很像，可以靠这个来记忆。

## 对称二叉树

跟第一题很像，都是需要遍历比较。

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
    public boolean isSymmetric(TreeNode root) {
        if(root == null){// root为空返回true
            return true;
        }
        return compare(root.left,root.right);
    }

    public boolean compare(TreeNode left, TreeNode right){// 遍历比较两个节点
        if(left == null && right == null){// 两个节点同时遍历完毕，说明对称，返回true
            return true;
        }
        if(left == null || right == null || left.val != right.val){// 其中一个节点先遍历完毕或两节点不相等，返回false
            return false;
        }
        return(compare(left.left,right.right) && compare(left.right,right.left));
    }
    
}
```



