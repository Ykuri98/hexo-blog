---
title: 剑指offer-day11
date: 2022-04-21 09:12:58
categories:
- 技术
tags:
- 算法
---

## 删除链表的节点

思路很简单，维护一个双指针，一个指向现节点，一个指向前节点，找到要删除的节点，让前节点指向现节点的下一个节点，再让现节点指向空指针即可。主要是删除头节点的问题，我选择再维护一个头指针preHead，而答案是将其作为特殊情况处理，直接返回头节点指向的链表。

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
    public ListNode deleteNode(ListNode head, int val) {
        ListNode pre = new ListNode(-1);// pre指向头指针
        ListNode preHead = pre;// preHead指向头指针
        ListNode cur = head;// cur指向链表中的头指针
        pre.next = cur;// 将自建的头指针和链表中的头指针连接
        while(cur != null){
            if(cur.val == val){// 删除节点
                pre.next = cur.next;
                cur.next = null;
            }
            pre = cur;
            cur = cur.next;
        }
        return preHead.next;
    }
}
```

## 链表中倒数第k个节点

跟上面一样，维护一个双指针，都先指向头节点，根据k值让p节点向前移动，当两个节点相距k-1时q节点和p节点一起前移，到p节点指向链表尾节点时结束，返回q节点所指向的链表即可。

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
    public ListNode getKthFromEnd(ListNode head, int k) {
        ListNode p = head;// 维护两个节点p、q
        ListNode q = head;
        for(int i = 0; i < k-1; i++){// 迁移p节点
            p = p.next;
        }
        while(p.next != null){// p节点和q节点一起前移
            q = q.next;
            p = p.next;
        }
        return q;// 返回q节点指向的链表
    }
}
```

