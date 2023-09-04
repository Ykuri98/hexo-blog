---
title: 剑指offer-day20
date: 2022-04-26 21:38:20
categories:
- 技术
tags:
- 算法
---

## 重建二叉树

又是一道知道思路但是不知道怎么实现的题目，直接放答案吧。

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
    int[] preorder;
    Map<Integer, Integer> inorderMap = new HashMap<>();
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        this.preorder = preorder;
        for(int i = 0; i < inorder.length; i++){
            inorderMap.put(inorder[i], i);
        }
        return recur(0, 0, inorder.length - 1);
    }

    public TreeNode recur(int root, int left, int right){
        if(left > right){// 发生越界，返回null
            return null;
        }
        int i = inorderMap.get(preorder[root]);// 获得根节点在中序序列的下标
        TreeNode node = new TreeNode(preorder[root]);// 新建根节点
        node.left = recur(root + 1, left, i-1);// 根节点的左节点连接递归生成的左子树
        node.right = recur(root + 1 + i - left, i+1, right);// 右节点连接递归生成的右子树
        return node;
    }
}
```

## 数值的整次方

如果用简单的迭代会超时，就是摆明了不准用迭代，正确的方式是用变化的二分法，每次将幂数折半，将要累乘的数换成该数的二次方，如果幂数是奇数的话就把多出来的一个数乘进返回值里。

```java
class Solution {
    public double myPow(double x, int n) {
        double res = 1;
        long b = n;// 用long来接int值，防止负数转正数时发生溢出
        if(b < 0){// 幂数小于0，底数要变为分数
            b = -b;
            x = 1 / x;
        }
        while(b > 0){
            if(b % 2 == 1){// 幂数为奇数，将多出的数放入答案
                res *= x;
            }
            x *= x;// 底数变成平方
            b /= 2;// 幂数变为一半，向下取整
        }
        return res;
    }
}
```

## 二叉搜索树的后序遍历序列

完全不会，直接放答案。

```java
class Solution {
    public boolean verifyPostorder(int[] postorder) {
        // 用栈存储倒序的后序遍历，即是“根，右，左”的逆序先序遍历
        Stack<Integer> stack = new Stack<>();
        int root = Integer.MAX_VALUE;
        for(int i = postorder.length - 1; i >= 0; i--) {
            // 因为按照遍历逻辑当前节点一定是父节点的左孩子，如果出现左孩子的值大于父节点，说明不是后序遍历序列，返回false
            if(postorder[i] > root) return false;
            // 如果栈顶大于当前节点，说明栈内有当前节点的父节点，出栈至栈空，栈底为父节点
            while(!stack.isEmpty() && stack.peek() > postorder[i])
            	root = stack.pop();
            // 当前节点压栈
            stack.add(postorder[i]);
        }
        return true;
    }
}
```

