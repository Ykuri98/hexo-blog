---
title: 剑指offer-day18
date: 2022-04-25 21:22:42
categories:
- 技术
tags:
- 算法
---

## 二叉树的深度

第一道自己写出来的树题！可以说非常具有纪念意义了，时空复杂度也不输最优解，自豪地放上自己的解。

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
    int depth;
    int max;
    public int maxDepth(TreeNode root) {
        if(root == null){
            return 0;
        }
        depth = 0;
        max = 0;
        preorder(root);
        return max;
    }

    public void preorder(TreeNode root){// 前序遍历
        depth++;// 遍历该节点，深度+1
        if(root == null){// 若节点为空，说明到达叶节点，深度-1并记录此时的最大深度
            depth--;
            max = Math.max(max,depth);
            return;
        }
        preorder(root.left);// 递归遍历左节点和右节点
        preorder(root.right);
        depth--;// 遍历完成，深度-1
    }
}
```

## 平衡二叉树

半写半抄完成的，用的是从上到下的递归，然而并不是最优解，最优解是从下到上的递归+剪枝。使用后序遍历从叶子节点往上遍历，每次遍历时看以该节点为根的子树是否是平衡二叉树，如果是则返回该子树的高度，否则返回-1，表示该树不是平衡二叉树。

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        return recur(root) != -1;
    }

    private int recur(TreeNode root) {// 返回平衡二叉树的高度，不是平衡二叉树则返回-1
        if (root == null) return 0;// 遍历至叶子节点，返回高度为0
        
        // 遍历左右子树，如果左右子树不是平衡二叉树，返回-1
        int left = recur(root.left);
        if(left == -1) return -1;
        int right = recur(root.right);
        if(right == -1) return -1;
        
        // 如果左右子树的高度差<2,表示是平衡二叉树，返回该根节点的高度，否则返回-1
        return Math.abs(left - right) < 2 ? Math.max(left, right) + 1 : -1;
    }
}
```

