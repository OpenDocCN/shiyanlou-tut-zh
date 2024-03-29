# 第 16 节 Linked List - 链表（五）

## LRU Cache

## Question

*   leetcode: [LRU Cache | LeetCode OJ](https://leetcode.com/problems/lru-cache/)
*   lintcode: [(134) LRU Cache](http://www.lintcode.com/en/problem/lru-cache/)

### Problem Statement

Design and implement a data structure for Least Recently Used (LRU) cache. It should support the following operations: `get` and `set`.

`get(key)` - Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.

`set(key, value)` - Set or insert the value if the key is not already present. When the cache reached its capacity, it should invalidate the least recently used item before inserting a new item.

## 题解

### Java

```
public class Solution {

    // @param capacity, an integer
    public Solution(int capacity) {
        // write your code here
    }

    // @return an integer
    public int get(int key) {
        // write your code here
    }

    // @param key, an integer
    // @param value, an integer
    // @return nothing
    public void set(int key, int value) {
        // write your code here
    }
} 
```

## Rotate List

## Question

*   leetcode: [Rotate List | LeetCode OJ](https://leetcode.com/problems/rotate-list/)
*   lintcode: [(170) Rotate List](http://www.lintcode.com/en/problem/rotate-list/)

### Problem Statement

Given a list, rotate the list to the right by *k* places, where *k* is non- negative.

#### Example

Given `1->2->3->4->5` and k = `2`, return `4->5->1->2->3`.

## 题解

旋转链表，链表类问题通常需要找到需要处理节点处的前一个节点。因此我们只需要找到旋转节点和最后一个节点即可。需要注意的细节是 k 有可能比链表长度还要大，此时需要取模，另一个 corner case 则是链表长度和 k 等长。

### Java

```
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
    /**
     * @param head: the List
     * @param k: rotate to the right k places
     * @return: the list after rotation
     */
    public ListNode rotateRight(ListNode head, int k) {
        if (head == null) return head;
        ListNode fast = head, slow = head;
        int len = 1;
        for (len = 1; fast.next != null && len <= k; len++) {
            fast = fast.next;
        }
        // k mod len if k > len
        if (len <= k) {
            k = k % len;
            fast = head;
            for (int i = 0; i < k; i++) {
                fast = fast.next;
            }
        }
        // forward slow and fast
        while (fast.next != null) {
            fast = fast.next;
            slow = slow.next;
        }
        // return new head
        fast.next = head;
        head = slow.next;
        slow.next = null;

        return head;
    }
} 
```

### 源码分析

由于需要处理的是节点的前一个节点，故最终的`while` 循环使用`fast.next != null`. k 与链表等长时包含在`len <= k`中。

### 复杂度分析

时间复杂度 $$O(n)$$, 空间复杂度 $$O(1)$$.

## Swap Nodes in Pairs

## Question

*   leetcode: [Swap Nodes in Pairs | LeetCode OJ](https://leetcode.com/problems/swap-nodes-in-pairs/)
*   lintcode: [(451) Swap Nodes in Pairs](http://www.lintcode.com/en/problem/swap-nodes-in-pairs/)

### Problem Statement

Given a linked list, swap every two adjacent nodes and return its head.

#### Example

Given `1->2->3->4`, you should return the list as `2->1->4->3`.

#### Challenge

Your algorithm should use only constant space. You may not modify the values in the list, only nodes itself can be changed.

## 题解 1 - Iteration

直觉上我们能想到的是使用 dummy 处理不定头节点，但是由于这里是交换奇偶位置的链表节点，我们不妨首先使用伪代码来表示。大致可以分为如下几个步骤：

1.  保存`2.next`
2.  将`2.next`赋值为`1`
3.  将`1.next`赋值为 1 中保存的`2.next`
4.  将前一个链表节点的 next 指向`1`
5.  更新前一个链表节点为`1`
6.  更新当前的链表节点为 1 中保存的`2.next`

链表类题目看似容易，但要做到 bug-free 其实不容易，建议结合图像辅助分析，onsite 时不要急，把过程先用伪代码写出来。然后将伪代码逐行转化。

### Java

```
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
public class Solution {
    /**
     * @param head a ListNode
     * @return a ListNode
     */
    public ListNode swapPairs(ListNode head) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode prev = dummy, curr = head;

        while (curr != null && curr.next != null) {
            ListNode after = curr.next;
            ListNode nextCurr = after.next;
            after.next = curr;
            curr.next = nextCurr;
            // link new node after prev
            prev.next = after;
            // update prev and curr
            prev = curr;
            curr = nextCurr;
        }

        return dummy.next;
    }
} 
```

### 源码分析

这里使用 dummy 处理不定头节点，首先将`prev`初始化为`dummy`, 然后按照题解中的几个步骤逐步转化，需要注意的是 while 循环中`curr`和`curr.next`都不能为`null`.

### 复杂度分析

遍历链表一遍，时间复杂度 $$O(1)$$. 使用了若干临时链表节点引用对象，空间复杂度 $$O(1)$$.

## 题解 2 - Recursion

在题解 1 的分析过程中我们发现比较难处理的是 `prev`和下一个头的连接，要是能直接得到链表后面新的头节点该有多好啊。首先我们可以肯定的是若`head == null || head.next == null`时应直接返回，如果不是则求得交换奇偶节点后的下一个头节点并链接到之前的奇数个节点。这种思想使用递归实现起来非常优雅！

### Java

```
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
public class Solution {
    /**
     * @param head a ListNode
     * @return a ListNode
     */
    public ListNode swapPairs(ListNode head) {
        if (head == null || head.next == null) return head;

        ListNode after = head.next;
        head.next = swapPairs(after.next);
        after.next = head;

        return after;
    }
} 
```

### 源码分析

这个递归实现非常优雅，需要注意的是递归步的退出条件==>`head == null || head.next == null)`.

### 复杂度分析

每个节点最多被遍历若干次，时间复杂度 $$O(n)$$, 空间复杂度 $$O(1)$$.

## Remove Linked List Elements

## Question

*   leetcode: [Remove Linked List Elements | LeetCode OJ](https://leetcode.com/problems/remove-linked-list-elements/)
*   lintcode: [(452) Remove Linked List Elements](http://www.lintcode.com/en/problem/remove-linked-list-elements/)

### Problem Statement

Remove all elements from a linked list of integers that have value `val`.

#### Example

Given `1->2->3->3->4->5->3`, val = 3, you should return the list as `1->2->4->5`

## 题解

删除链表中指定值，找到其前一个节点即可，将 next 指向下一个节点即可。

### Python

```
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def removeElements(self, head, val):
        """
        :type head: ListNode
        :type val: int
        :rtype: ListNode
        """
        dummy = ListNode(0)
        dummy.next = head
        curr = dummy
        while curr.next is not None:
            if curr.next.val == val:
                curr.next = curr.next.next
            else:
                curr = curr.next

        return dummy.next 
```

### Java

```
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
public class Solution {
    /**
     * @param head a ListNode
     * @param val an integer
     * @return a ListNode
     */
    public ListNode removeElements(ListNode head, int val) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode curr = dummy;
        while (curr.next != null) {
            if (curr.next.val == val) {
                curr.next = curr.next.next;
            } else {
                curr = curr.next;
            }
        }

        return dummy.next;
    }
} 
```

### 源码分析

while 循环中使用`curr.next`较为方便，if 语句中比较时也使用`curr.next.val`也比较简洁，如果使用`curr`会比较难处理。

### 复杂度分析

略