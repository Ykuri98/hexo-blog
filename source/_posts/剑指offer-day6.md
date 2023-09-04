---
title: 剑指offer-day6
date: 2022-04-14 14:02:21
categories:
- 技术
tags:
- 算法
---

## 从上到下打印二叉树

经典题目，使用层序遍历来实现，具体实现方法就是用一个队列来记录节点，一个ArrayList记录答案。当前节点入队后，再将其左右节点入队，随后出队，ArrayList记录节点的值，直至队列为空。

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
    public int[] levelOrder(TreeNode root) {
        LinkedList<TreeNode> q = new LinkedList<>();
        ArrayList<Integer> res = new ArrayList<Integer>();
        q.add(root);// 将根节点入队
        TreeNode t;
        while((t = q.peek()) != null){// 队头为空跳出
            if(t.left != null){// 左节点入队
                q.add(t.left);
            }
            if(t.right != null){// 右节点入队
                q.add(t.right);
            }
            res.add(q.poll().val);// 取出队头获得其值
        }
        int[] arr = new int[res.size()];
        for(int i = 0; i<res.size(); i++){// ArrayList转数组
            arr[i] = res.get(i);
        }
        return arr;
    }
}
```

## 从上到下打印二叉树Ⅱ

上一题的变种，加入了对当前层数的判断，需要在之前的代码上做一些修改，主要是用一个int值记录当前层数中的节点数。

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
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        Queue<TreeNode> q = new LinkedList<>();
        if(root != null){
            q.add(root);
        }
        while(q.size() != 0){
            int i = q.size();// 记录当前层数的节点数
            List<Integer> tmp = new ArrayList<>();
            while(i > 0){
                TreeNode t = q.poll();
                tmp.add(t.val);
                if(t.left != null){
                    q.add(t.left);
                }
                if(t.right != null){
                    q.add(t.right);
                }
                i--;
            }
            res.add(tmp);
        }
        return res;
    }
}
```

## 从上到下打印二叉树Ⅲ

究极加强版，在上一题的基础上加上了蛇形遍历，其实就是需要对层数的奇偶进行判断，而且需要把临时队列tmp更改为双端队列，当层数为奇时从尾部插入，反之从头部插入

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
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        Queue<TreeNode> q = new LinkedList<>();
        if(root != null){
            q.add(root);
        }
        while(q.size() != 0){
            int i = q.size();
            LinkedList<Integer> tmp = new LinkedList<>();
            while(i > 0){
                TreeNode t = q.poll();
                if(res.size() % 2 == 0){// 层数判断
                    tmp.addLast(t.val);
                }
                else{
                    tmp.addFirst(t.val);
                }
                
                if(t.left != null){
                    q.add(t.left);
                }
                if(t.right != null){
                    q.add(t.right);
                }
                i--;
            }
            res.add(tmp);
        }
        return res;
    }
}
```

