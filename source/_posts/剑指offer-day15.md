---
title: 剑指offer-day15
date: 2022-04-22 20:37:07
categories:
- 技术
tags:
- 算法
---

## 二叉树中和为某一值的路径

完全没思路，直接放答案。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    LinkedList<Integer> path = new LinkedList<>();// 定义一个path来记录每次递归结束后的结果
    LinkedList<List<Integer>> res = new LinkedList<>();
    public List<List<Integer>> pathSum(TreeNode root, int target) {
        firstOrder(root,target);// 先序遍历
        return res;
    }

    public void firstOrder(TreeNode root, int target){
        if(root == null){// 节点为空时返回
            return;
        }
        path.add(root.val);// path添加递归的值
        target -= root.val;// 目标值减去当前递归的值
        if(target == 0 && root.left == null && root.right == null){// 如果目标值为0且该节点为叶子节点，将已经递归完成的path放入答案列表中
            res.add(new LinkedList(path));
        }
        pathSum(root.left,target);// 递归遍历左节点
        pathSum(root.right,target);// 递归遍历右节点
        path.removeLast();// 执行至此表示寻找符合的叶子节点失败，将本次遍历的节点退出，回溯至上个节点
    }
}
```

## 二叉搜索树与双向链表

这道题想到了中序遍历，但是对双链表还是不太熟悉，没做出来，半抄了一个答案。

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public Node left;
    public Node right;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val,Node _left,Node _right) {
        val = _val;
        left = _left;
        right = _right;
    }
};
*/
class Solution {
    Node head;// 定义头节点和尾节点
    Node pre;
    public Node treeToDoublyList(Node root) {
        if(root == null){// 树为空返回null
            return null;
        }
        inorder(root);// 以尾节点为指针，进行中序遍历
        head.left = pre;// 遍历完成，将头尾节点连接
        pre.right = head;
        return head;
    }

    public void inorder(Node root){
        if(root == null){// 遍历至空节点返回
            return;
        }
        inorder(root.left);
        if(pre != null){// 此时尾节点若还没赋值表示头尾节点未初始化，需要初始化头节点；反之则让尾节点指向当前节点
            pre.right = root;
        }
        else{
            head = root;
        }
        root.left = pre;// 当前节点的尾指针指向尾节点
        pre = root;//尾节点跳至当前节点
        inorder(root.right);
    }
}
```

## 二叉搜索树的第k大节点

这道题倒是写出来了，但是还是老问题，时空复杂度非常感人，看了答案发现用了一个逆序的中序遍历，而且提前终止了遍历，所以能有非常优秀的时空复杂度。

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
    int res;
    int k;
    public int kthLargest(TreeNode root, int k) {
        this.k = k;
        inorder(root);// 逆序中序遍历
        return res;
    }

    public void inorder(TreeNode root){
        if(root == null || k == 0){// 当遍历至空节点时返回；当k已经为0时，提前终止遍历
            return;
        }

        inorder(root.right);
        if(--k == 0){// 当k-1 = 0时，表示当前遍历的节点就是答案，赋值给res
            res = root.val;
        }
        inorder(root.left);
    }
}
```

