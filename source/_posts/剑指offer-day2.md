---
title: 剑指offer-day2
date: 2022-04-08 20:58:04
categories:
- 技术
tags:
- 算法
---

## 从尾到头打印链表

简单题，但是实现方法与答案有些不同。

我考虑的是用一个ArrayList来记录链表中的值，最后逆序赋值给一个int数组。答案用的是模拟栈的LinkedList。我尝试了两个方法，发现时空复杂度并没有什么变化，所以就放上自己的答案吧。

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
    public int[] reversePrint(ListNode head) {
        ArrayList<Integer> list = new ArrayList<>();
        while(head != null){
            list.add(head.val);
            head = head.next;
        }
        int[] res = new int[list.size()];
        for(int i = 0; i < list.size(); i++){
            res[i] = list.get(list.size() - i - 1);
        }
        return res;
    }
}
```

## 反转链表

经典题目了，但是发现自己死活做不出来，总是有空指针异常，一看答案发现语句顺序错了...但总体思路是对的。利用三个指针来指向前元素、现元素和后元素，然后进行反转操作，操作顺序为：

1. next指针指向当前元素的后一个元素
2. cur指向的当前元素的指针改为指向pre
3. pre改为指向cur指向的元素
4. cur改为指向next指向的元素

唯一要注意的点是next指针需要在循环内创建，否则在链表本身为空时会报空指针。

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
    public ListNode reverseList(ListNode head){
        ListNode pre = null;
        ListNode cur = head;

        while(cur != null){
            ListNode next = cur.next;// next指针指向后元素
            cur.next = pre;// cur的指针改为指向pre
            pre = cur;// pre改为指向cur指向的元素
            cur = next;// cur改为指向next指向的元素
            //next = cur.next;// 自己写的报空指针，不能写在后面，因为当cur在尾指针null时，无法获取next
        }
        return pre;
    }
}
```

之前有想过同上一题目一样，用一个辅助栈来完成反转，看了答案发现这样做并没有三指针快，就放弃这个思路了。

## 复杂链表的复制

完全不会做，选择睡大觉（恼）

好好看了看答案，大概知道思路是怎样的，复述一下。

有两种解法：辅助哈希表和拼接链表，因为后者在空间复杂度上优于前者，所以使用后者。

因为random节点的存在，导致复制时指针可能指向一个还没有创建的节点，所以可以将原链表两倍拉长，使原链表节点先指向复制节点，复制节点再指向原链表的下一个节点。完成复制后进行random节点的指向初始化，通过原节点的信息来让复制节点的指向正确的节点。最后将链表拆开，复制完成。

```java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/
class Solution {
    public Node copyRandomList(Node head) {
        if(head == null) return null;// 空链表返回空
        Node cur = head;
        // 1. 复制各节点，并构建拼接链表
        while(cur != null) {
            Node tmp = new Node(cur.val);// 新建复制节点，复制原节点的值
            tmp.next = cur.next;// 复制节点的指针指向原节点的下一个元素
            cur.next = tmp;// 原节点的指针指向复制节点
            cur = tmp.next;// cur改为指向复制节点的下一节点，即原链表的下一节点
        }
        // 2. 构建各新节点的 random 指向
        cur = head;
        while(cur != null) {
            if(cur.random != null)
                cur.next.random = cur.random.next;// cur.next是复制节点，说明将复制节点的random指针指向原节点的random指针指向的节点的下一个节点，即也是复制节点
            cur = cur.next.next;// 跳到原链表的下一个节点
        }
        // 3. 拆分两链表
        cur = head.next;
        Node pre = head, res = head.next;
        while(cur.next != null) {
            pre.next = pre.next.next;// 原节点的指针指向原链表的下一个节点
            cur.next = cur.next.next;// 复制节点的指针指向复制链表的下一个节点
            pre = pre.next;// 跳到原链表的下一个节点
            cur = cur.next;// 跳到复制链表的下一个节点
        }
        pre.next = null; // 单独处理原链表尾节点
        return res;      // 返回新链表头节点
    }
}

// 作者：jyd
// 链接：https://leetcode-cn.com/problems/fu-za-lian-biao-de-fu-zhi-lcof/solution/jian-zhi-offer-35-fu-za-lian-biao-de-fu-zhi-ha-xi-/
// 来源：力扣（LeetCode）
// 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

可以发现自己对链表还不够熟练，这道题要多加练习。
