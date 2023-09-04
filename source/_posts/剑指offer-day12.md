---
title: 剑指offer-day12
date: 2022-04-21 14:14:16
categories:
- 技术
tags:
- 算法
---

## 合并两个排序的链表

还是双指针的题目，一开始是想直接在两个链表上处理的，后来发现会出现漏元素的问题，就跟着答案老老实实地用一条新链表来接。

思路很简单，用两个指针p和q遍历两个链表，p<=q时将p指向的元素放入新建的链表，反之则放入q指向的元素，遍历至一个指针遍历完成，将新链表指向未遍历完的那个指针就完成了。

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if(l1 == null){
            return l2;
        }
        if(l2 == null){
            return l1;
        }
        ListNode p = l1;// 创建两个指针
        ListNode q = l2;
        ListNode head = new ListNode(-1);// 创建新链表头和一个头指针
        ListNode pre = head;
        while(p != null && q != null){// 遍历两个链表
            if(p.val <= q.val){
                ListNode cp = p;
                head.next = cp;
                head = head.next;
                p = p.next;
            }
            else{
                ListNode cq = q;
                head.next = cq;
                head = head.next;
                q = q.next;
            }
        }
        if(p != null){// 其中一个链表遍历完成，遍历另一个链表
            head.next = p;
        }
        if(q != null){
            head.next = q;
        }
        return pre.next;
    }
}
```

## 两个链表的第一个公共节点

这个并没有想出来怎么做，还是看答案吧。

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if(headA == null || headB == null){// 其中一个节点为空，返回null
            return null;
        }
        ListNode p = headA;// 建立两个指针
        ListNode q = headB;
        while(p != q){
            if(p == null){// 当headA的指针遍历完成后，改为从headB再遍历一次
                p = headB;
            }
            else{
                p = p.next;
            }
            if(q == null){// 当headB的指针遍历完成后，改为从headA再遍历一次
                q = headA;
            }
            else{
                q = q.next;
            }
        }
        // p、q相等时，跳出循环并输出p，这就是公共节点
        return p;
    }
}
```

