---
title: 剑指offer-day1
date: 2022-04-07 19:29:40
categories:
- 技术
tags:
- 算法
---

## 用两个栈实现队列

简单且经典的题目，用两个栈来模拟一个队列。

设置两个栈s1和s2，s1用于正常模拟队列的进队。当队列需要出队时，要把是s1中的值依次放入s2，再在s2的栈顶删除s1的栈底元素（也就是队列的队头元素），然后将处理后的元素再依次放入s1中，就模拟了队列的出队过程。

此时需注意：删除元素应该有判空条件。

```java
class CQueue {
    Stack<Integer> s1;// 新建两个栈
    Stack<Integer> s2;
    public CQueue() {// 初始化
        s1 = new Stack<>();
        s2 = new Stack<>();
    }
    
    public void appendTail(int value) {// 模拟进队
        s1.push(value);
    }
    
    public int deleteHead() {// 模拟出队
        if(s1.empty()){
            return -1;
        }
        while(!s1.empty()){
            s2.push(s1.pop());
        }
        int res = s2.pop();// 需要出队的元素
        while(!s2.empty()){
            s1.push(s2.pop());
        
        }
        return res;
    }
}

```

看评论说java的stack类会造成速度变慢，可以用LinkedList做容器，但是会不符合题目要求，可以作为优化的一种选项。

## 包含min函数的栈

原本是简单题，但是因为自己想的太复杂了，迟迟没有做出来，参考了一下答案，才把思路理清（👎）

设置一个辅助栈supStack，用于记录主栈入栈操作时栈内的最小值。第一个入主栈的自然要记录，此后若有小于或等于辅助栈栈顶的数，则需要辅助栈将一个相同的数放入栈顶，反之则不用操作。出栈时则需要与辅助栈的栈顶比对，若相等则辅助栈也必须出栈。

```java
class MinStack {
    Stack<Integer> mainStack;// 主栈
    Stack<Integer> supStack;// 辅助栈
    /** initialize your data structure here. */
    public MinStack() {// 初始化
        mainStack = new Stack();
        supStack = new Stack();
    }
    
    public void push(int x) {
        mainStack.push(x);
        if(supStack.empty() || supStack.peek() >= x){// 辅助栈空或大于等于要放入的值时，需放入一个同样的值
            supStack.push(x);
        }
    }
    
    public void pop() {
        if(mainStack.pop().equals(supStack.peek())){// 当出栈值与辅助栈栈顶相等时，辅助栈也要出栈
            supStack.pop();
        }
    }
    
    public int top() {// 查看主栈的栈顶
        return mainStack.peek();
    }
    
    public int min() {// 查看辅助栈的栈顶
        return supStack.peek();
    }
}
```

需要注意的是，pop()中需要使用`equals`方法而不是`==`，因为在`Integer`中，[-128,127]会被缓存记住，只有在这个范围内`==`才是比较数值，否则比较的是对象，会造成错误。

## 记忆点

```java
// Java Stack类

boolean empty();// 判断栈是否为空
Object peek();// 返回栈顶
Object pop();// 返回并删除栈顶
Object push(Object element);// 在栈顶添加新元素
int search();// 查找s内的值，返回其在栈内的位置，以1为基数
```

