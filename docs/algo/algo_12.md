# 第 12 节 Linked List - 链表（一）

本节包含链表的一些常用操作，如删除、插入和合并等。

常见错误有 遍历链表不向前递推节点，遍历链表前未保存头节点，返回链表节点指针错误。

下图是把本章中所有出现的题目归类总结了一下，便于记忆

![链表](https://raw.githubusercontent.com/billryan/algorithm-exercise/master/images/linked_list_summary.png)

## Remove Duplicates from Sorted List

## Question

*   leetcode: [Remove Duplicates from Sorted List | LeetCode OJ](https://leetcode.com/problems/remove-duplicates-from-sorted-list/)
*   lintcode: [(112) Remove Duplicates from Sorted List](http://www.lintcode.com/en/problem/remove-duplicates-from-sorted-list/)

### Problem Statement

Given a sorted linked list, delete all duplicates such that each element appear only *once*.

#### Example

Given `1->1->2`, return `1->2`.
Given `1->1->2->3->3`, return `1->2->3`.

## 题解

遍历之，遇到当前节点和下一节点的值相同时，删除下一节点，并将当前节点`next`值指向下一个节点的`next`, 当前节点首先保持不变，直到相邻节点的值不等时才移动到下一节点。

### Python

```
"""
Definition of ListNode
class ListNode(object):
    def __init__(self, val, next=None):
        self.val = val
        self.next = next
"""
class Solution:
    """
    @param head: A ListNode
    @return: A ListNode
    """
    def deleteDuplicates(self, head):
        curt = head
        while curt:
            while curt.next and curt.next.val == curt.val:
                curt.next = curt.next.next
            curt = curt.next
        return head 
```

### C++

```
/**
 * Definition of ListNode
 * class ListNode {
 * public:
 *     int val;
 *     ListNode *next;
 *     ListNode(int val) {
 *         this->val = val;
 *         this->next = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     * @param head: The first node of linked list.
     * @return: head node
     */
    ListNode *deleteDuplicates(ListNode *head) {
        ListNode *curr = head;
        while (curr != NULL) {
            while (curr->next != NULL && curr->val == curr->next->val) {
                ListNode *temp = curr->next;
                curr->next = curr->next->next;
                delete(temp);
                temp = NULL;
            }
            curr = curr->next;
        }

        return head;
    }
}; 
```

### Java

```
/**
 * Definition for ListNode
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
     * @param ListNode head is the head of the linked list
     * @return: ListNode head of linked list
     */
    public static ListNode deleteDuplicates(ListNode head) {
        ListNode curr = head;
        while (curr != null) {
            while (curr.next != null && curr.val == curr.next.val) {
                curr.next = curr.next.next;
            }
            curr = curr.next;
        }

        return head;
    }
} 
```

### 源码分析

1.  首先进行异常处理，判断 head 是否为 NULL
2.  遍历链表，`curr->val == curr->next->val`时，保存`curr->next`，便于后面释放内存(非 C/C++无需手动管理内存)
3.  不相等时移动当前节点至下一节点，注意这个步骤必须包含在`else`中，否则逻辑较为复杂

~~`while` 循环处也可使用`curr != null && curr.next != null`, 这样就不用单独判断`head` 是否为空了，但是这样会降低遍历的效率，因为需要判断两处。~~使用双重`while`循环可只在内循环处判断，避免了冗余的判断，谢谢 @xuewei4d 提供的思路。

### 复杂度分析

遍历链表一次，时间复杂度为 $$O(n)$$, 使用了一个中间变量进行遍历，空间复杂度为 $$O(1)$$.

## Reference

*   [Remove Duplicates from Sorted List 参考程序 | 九章](http://www.jiuzhang.com/solutions/remove-duplicates-from-sorted-list/)

## Remove Duplicates from Sorted List II

## Question

*   leetcode: [Remove Duplicates from Sorted List II | LeetCode OJ](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/)
*   lintcode: [(113) Remove Duplicates from Sorted List II](http://www.lintcode.com/en/problem/remove-duplicates-from-sorted-list-ii/)

### Problem Statement

Given a sorted linked list, delete all nodes that have duplicate numbers, leaving only *distinct* numbers from the original list.

#### Example

Given `1->2->3->3->4->4->5`, return `1->2->5`.
Given `1->1->1->2->3`, return `2->3`.

## 题解

上题为保留重复值节点的一个，这题删除全部重复节点，看似区别不大，但是考虑到链表头不确定(可能被删除，也可能保留)，因此若用传统方式需要较多的 if 条件语句。这里介绍一个**处理链表头节点不确定的方法——引入 dummy node.**

```
ListNode *dummy = new ListNode(0);
dummy->next = head;
ListNode *node = dummy; 
```

引入新的指针变量`dummy`，并将其 next 变量赋值为 head，考虑到原来的链表头节点可能被删除，故应该从 dummy 处开始处理，这里复用了 head 变量。考虑链表`A->B->C`，删除 B 时，需要处理和考虑的是 A 和 C，将 A 的 next 指向 C。如果从空间使用效率考虑，可以使用 head 代替以上的 node，含义一样，node 比较好理解点。

与上题不同的是，由于此题引入了新的节点`dummy`，不可再使用`node->val == node->next->val`，原因有二：

1.  此题需要将值相等的节点全部删掉，而删除链表的操作与节点前后两个节点都有关系，故需要涉及三个链表节点。且删除单向链表节点时不能删除当前节点，只能改变当前节点的`next`指向的节点。
2.  在判断 val 是否相等时需先确定`node->next`和`node->next->next`均不为空，否则不可对其进行取值。

说多了都是泪，先看看我的错误实现：

### C++ - Wrong

```
/**
 * Definition of ListNode
 * class ListNode {
 * public:
 *     int val;
 *     ListNode *next;
 *     ListNode(int val) {
 *         this->val = val;
 *         this->next = NULL;
 *     }
 * }
 */
class Solution{
public:
    /**
     * @param head: The first node of linked list.
     * @return: head node
     */
    ListNode * deleteDuplicates(ListNode *head) {
        if (head == NULL || head->next == NULL) {
            return NULL;
        }

        ListNode *dummy;
        dummy->next = head;
        ListNode *node = dummy;

        while (node->next != NULL && node->next->next != NULL) {
            if (node->next->val == node->next->next->val) {
                int val = node->next->val;
                while (node->next != NULL && val == node->next->val) {
                    ListNode *temp = node->next;
                    node->next = node->next->next;
                    delete temp;
                }
            } else {
                node->next = node->next->next;
            }
        }

        return dummy->next;
    }
}; 
```

### 错因分析

错在什么地方？

1.  节点 dummy 的初始化有问题，对类的初始化应该使用`new`
2.  在 else 语句中`node->next = node->next->next;`改写了`dummy-next`中的内容，返回的`dummy-next`不再是队首元素，而是队尾元素。原因很微妙，应该使用`node = node->next;`，node 代表节点指针变量，而 node->next 代表当前节点所指向的下一节点地址。具体分析可自行在纸上画图分析，可对指针和链表的理解又加深不少。

![remove_duplicates_from_sorted_listd 内存分析](img/f25a65310ec07f0a961fa328cd6ba732.jpg)

图中上半部分为 ListNode 的内存示意图，每个框底下为其内存地址。`dummy`指针变量本身的地址为 ox7fff5d0d2500，其保存着指针变量值为 0x7fbe7bc04c50\. `head`指针变量本身的地址为 ox7fff5d0d2508，其保存着指针变量值为 0x7fbe7bc04c00.

好了，接下来看看正确实现及解析。

### Python

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    # @param {ListNode} head
    # @return {ListNode}
    def deleteDuplicates(self, head):
        if head is None:
            return None

        dummy = ListNode(0)
        dummy.next = head
        node = dummy
        while node.next is not None and node.next.next is not None:
            if node.next.val == node.next.next.val:
                val_prev = node.next.val
                while node.next is not None and node.next.val == val_prev:
                    node.next = node.next.next
            else:
                node = node.next

        return dummy.next 
```

### C++

```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (head == NULL) return NULL;

        ListNode dummy(0);
        dummy.next = head;
        ListNode *node = &dummy;
        while (node->next != NULL && node->next->next != NULL) {
            if (node->next->val == node->next->next->val) {
                int val_prev = node->next->val;
                // remove ListNode node->next
                while (node->next != NULL && val_prev == node->next->val) {
                    ListNode *temp = node->next;
                    node->next = node->next->next;
                    delete temp;
                }
            } else {
                node = node->next;
            }
        }

        return dummy.next;
    }
}; 
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
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) return null;

        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode node = dummy;
        while(node.next != null && node.next.next != null) {
            if (node.next.val == node.next.next.val) {
                int val_prev = node.next.val;
                while (node.next != null && node.next.val == val_prev) {
                    node.next = node.next.next;
                }
            } else {
                node = node.next;
            }
        }

        return dummy.next;
    }
} 
```

### 源码分析

1.  首先考虑异常情况，head 为 NULL 时返回 NULL
2.  new 一个 dummy 变量，`dummy->next`指向原链表头。(C++中最好不要使用 new 的方式生成 dummy, 否则会有内存泄露)
3.  使用新变量 node 并设置其为 dummy 头节点，遍历用。
4.  当前节点和下一节点 val 相同时先保存当前值，便于 while 循环终止条件判断和删除节点。注意这一段代码也比较精炼。
5.  最后返回`dummy->next`，即题目所要求的头节点。

Python 中也可不使用`is not None`判断，但是效率会低一点。

### 复杂度分析

两根指针(node.next 和 node.next.next)遍历，时间复杂度为 $$O(2n)$$. 使用了一个 dummy 和中间缓存变量，空间复杂度近似为 $$O(1)$$.

## Reference

*   [Remove Duplicates from Sorted List II | 九章](http://www.jiuzhang.com/solutions/remove-duplicates-from-sorted-list-ii/)

## Partition List

## Question

*   leetcode: [Partition List | LeetCode OJ](https://leetcode.com/problems/partition-list/)
*   lintcode: [(96) Partition List](http://www.lintcode.com/en/problem/partition-list/)

### Problem Statement

Given a linked list and a value *x*, partition it such that all nodes less than *x* come before nodes greater than or equal to *x*.

You should preserve the original relative order of the nodes in each of the two partitions.

For example,
Given `1->4->3->2->5->2` and *x* = 3,
return `1->2->2->4->3->5`.

## 题解

此题出自 *CTCI* 题 2.4，依据题意，是要根据值 x 对链表进行分割操作，具体是指将所有小于 x 的节点放到不小于 x 的节点之前，咋一看和快速排序的分割有些类似，但是这个题的不同之处在于只要求将小于 x 的节点放到前面，而并不要求对元素进行排序。

这种分割的题使用两路指针即可轻松解决。左边指针指向小于 x 的节点，右边指针指向不小于 x 的节点。由于左右头节点不确定，我们可以使用两个 dummy 节点。

### Python

```
"""
Definition of ListNode
class ListNode(object):

    def __init__(self, val, next=None):
        self.val = val
        self.next = next
"""
class Solution:
    """
    @param head: The first node of linked list.
    @param x: an integer
    @return: a ListNode
    """
    def partition(self, head, x):
        if head is None:
            return None

        leftDummy = ListNode(0)
        left = leftDummy
        rightDummy = ListNode(0)
        right = rightDummy
        node = head
        while node is not None:
            if node.val < x:
                left.next = node
                left = left.next
            else:
                right.next = node
                right = right.next
            node = node.next
        # post-processing
        right.next = None
        left.next = rightDummy.next

        return leftDummy.next 
```

### C++

```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* partition(ListNode* head, int x) {
        if (head == NULL) return NULL;

        ListNode *leftDummy = new ListNode(0);
        ListNode *left = leftDummy;
        ListNode *rightDummy = new ListNode(0);
        ListNode *right = rightDummy;
        ListNode *node = head;
        while (node != NULL) {
            if (node->val < x) {
                left->next = node;
                left = left->next;
            } else {
                right->next = node;
                right = right->next;
            }
            node = node->next;
        }
        // post-processing
        right->next = NULL;
        left->next = rightDummy->next;

        return leftDummy->next;
    }
}; 
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
    public ListNode partition(ListNode head, int x) {
        ListNode leftDummy = new ListNode(0);
        ListNode leftCurr = leftDummy;
        ListNode rightDummy = new ListNode(0);
        ListNode rightCurr = rightDummy;

        ListNode runner = head;
        while (runner != null) {
            if (runner.val < x) {
                leftCurr.next = runner;
                leftCurr = leftCurr.next;
            } else {
                rightCurr.next = runner;
                rightCurr = rightCurr.next;
            }
            runner = runner.next;
        }

        // cut off ListNode after rightCurr to avoid cylic
        rightCurr.next = null;
        leftCurr.next = rightDummy.next;

        return leftDummy.next;
    }
} 
```

### 源码分析

1.  异常处理
2.  引入左右两个 dummy 节点及 left 和 right 左右尾指针
3.  遍历原链表
4.  处理右链表，置`right->next`为空(否则如果不为尾节点则会报错，处理链表时 以 null 为判断)，将右链表的头部链接到左链表尾指针的 next，返回左链表的头部

### 复杂度分析

遍历链表一次，时间复杂度近似为 $$O(n)$$, 使用了两个 dummy 节点及中间变量，空间复杂度近似为 $$O(1)$$.

## Add Two Numbers

## Question

*   leetcode: [Add Two Numbers | LeetCode OJ](https://leetcode.com/problems/add-two-numbers/)
*   lintcode: [Add Two Numbers](http://www.lintcode.com/en/problem/add-two-numbers/)

### Problem Statement

You have two numbers represented by a linked list, where each node contains a single digit. The digits are stored in `reverse` order, such that the 1's digit is at the head of the list. Write a function that adds the two numbers and returns the sum as a linked list.

#### Example

Given `7->1->6 + 5->9->2`. That is, `617 + 295`.

Return `2->1->9`. That is `912`.

Given `3->1->5` and `5->9->2`, return `8->0->8`.

## 题解

一道看似简单的进位加法题，实则杀机重重，不信你不看答案自己先做做看。

首先由十进制加法可知应该注意进位的处理，但是这道题仅注意到这点就够了吗？还不够！因为两个链表长度有可能不等长！因此这道题的亮点在于边界和异常条件的处理，感谢 @wen 引入的 dummy 节点，处理起来更为优雅！

### Python

```
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def add_two_numbers(self, l1, l2):
        '''
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        '''
        carry = 0
        dummy = prev = ListNode(-1)
        while l1 or l2 or carry:
            v1 = l1.val if l1 else 0
            v2 = l2.val if l2 else 0
            val = (v1 + v2 + carry) % 10
            carry = (v1 + v2 + carry) / 10

            prev.next = ListNode(val)
            prev = prev.next

            if l1:
                l1 = l1.next
            if l2:
                l2 = l2.next
        return dummy.next 
```

### C++

```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode dummy(0);
        ListNode *curr = &dummy;
        int carry = 0;

        while ((l1 != NULL) || (l2 != NULL) || (carry != 0)) {
            int l1_val = (l1 != NULL) ? l1->val : 0;
            int l2_val = (l2 != NULL) ? l2->val : 0;
            int sum = carry + l1_val + l2_val;
            carry = sum / 10;
            curr->next = new ListNode(sum % 10);

            curr = curr->next;
            if (l1 != NULL) l1 = l1->next;
            if (l2 != NULL) l2 = l2->next;
        }

        return dummy.next;
    }
}; 
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
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;
        int carry = 0;

        while ((l1 != null) || (l2 != null) || (carry != 0)) {
            int l1_val = (l1 != null) ? l1.val : 0;
            int l2_val = (l2 != null) ? l2.val : 0;
            int sum = carry + l1_val + l2_val;
        // update carry
            carry = sum / 10;
            curr.next = new ListNode(sum % 10);

            curr = curr.next;
            if (l1 != null) l1 = l1.next;
            if (l2 != null) l2 = l2.next;
        }

        return dummy.next;
    }
} 
```

### 源码分析

1.  迭代能正常进行的条件为`(NULL != l1) || (NULL != l2) || (0 != carry)`, 缺一不可。
2.  对于空指针节点的处理可以用相对优雅的方式处理 - `int l1_val = (NULL == l1) ? 0 : l1->val;`
3.  ~~生成新节点时需要先判断迭代终止条件 - `(NULL == l1) && (NULL == l2) && (0 == carry)`, 避免多生成一位数 0。~~ 使用 dummy 节点可避免这一情况。

### 复杂度分析

没啥好分析的，时间和空间复杂度均为 $$O(max(L1, L2))$$.

## Reference

*   *CC150 Chapter 9.2* 题 2.5，中文版 p123
*   [Add two numbers represented by linked lists | Set 1 - GeeksforGeeks](http://www.geeksforgeeks.org/add-two-numbers-represented-by-linked-lists/)

## Two Lists Sum Advanced <i class="fa fa-star"></i><i class="fa fa-star"></i><i class="fa fa-star"></i>

## Question

*   CC150 - [Add two numbers represented by linked lists | Set 2 - GeeksforGeeks](http://www.geeksforgeeks.org/sum-of-two-linked-lists/)

```
Given two numbers represented by two linked lists, write a function that returns sum list.
The sum list is linked list representation of addition of two input numbers.

Example

Input:
  First  List: 5->6->3  // represents number 563
  Second List: 8->4->2  // represents number 842
Output
  Resultant list: 1->4->0->5  // represents number 1405

Challenge

Not allowed to modify the lists.
Not allowed to use explicit extra space. 
```

## 题解 1 - 反转链表

在题 [Two Lists Sum | Data Structure and Algorithm](http://algorithm.yuanbin.me/zh-hans/linked_list/two_lists_sum.html) 的基础上改了下数位的表示方式，前者低位在前，高位在后，这个题的高位在前，低位在后。很自然地可以联想到先将链表反转，而后再使用 Two Lists Sum 的解法。

## Reference

*   [Add two numbers represented by linked lists | Set 2 - GeeksforGeeks](http://www.geeksforgeeks.org/sum-of-two-linked-lists/)

## Remove Nth Node From End of List

## Question

*   lintcode: [(174) Remove Nth Node From End of List](http://www.lintcode.com/en/problem/remove-nth-node-from-end-of-list/)

```
Given a linked list, remove the nth node from the end of list and return its head.

Note
The minimum number of nodes in list is n.

Example
Given linked list: 1->2->3->4->5->null, and n = 2.

After removing the second node from the end, the linked list becomes 1->2->3->5->null.

Challenge
O(n) time 
```

## 题解

简单题， 使用快慢指针解决此题，需要注意最后删除的是否为头节点。让快指针先走`n`步，直至快指针走到终点，找到需要删除节点之前的一个节点，改变`node->next`域即可。

### C++

```
/**
 * Definition of ListNode
 * class ListNode {
 * public:
 *     int val;
 *     ListNode *next;
 *     ListNode(int val) {
 *         this->val = val;
 *         this->next = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     * @param head: The first node of linked list.
     * @param n: An integer.
     * @return: The head of linked list.
     */
    ListNode *removeNthFromEnd(ListNode *head, int n) {
        if (NULL == head || n < 0) {
            return NULL;
        }

        ListNode *preN = head;
        ListNode *tail = head;
        // slow fast pointer
        int index = 0;
        while (index < n) {
            if (NULL == tail) {
                return NULL;
            }
            tail = tail->next;
            ++index;
        }

        if (NULL == tail) {
            return head->next;
        }

        while (tail->next) {
            tail = tail->next;
            preN = preN->next;
        }
        preN->next = preN->next->next;

        return head;
    }
}; 
```

以上代码单独判断了是否需要删除头节点的情况，在遇到头节点不确定的情况下，引入`dummy`节点将会使代码更加优雅，改进的代码如下。

### C++ dummy node

```
/**
 * Definition of ListNode
 * class ListNode {
 * public:
 *     int val;
 *     ListNode *next;
 *     ListNode(int val) {
 *         this->val = val;
 *         this->next = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     * @param head: The first node of linked list.
     * @param n: An integer.
     * @return: The head of linked list.
     */
    ListNode *removeNthFromEnd(ListNode *head, int n) {
        if (NULL == head || n < 1) {
            return head;
        }

        ListNode dummy(0);
        dummy.next = head;
        ListNode *preDel = dummy;

        for (int i = 0; i != n; ++i) {
            if (NULL == head) {
                return NULL;
            }
            head = head->next;
        }

        while (head) {
            head = head->next;
            preDel = preDel->next;
        }
        preDel->next = preDel->next->next;

        return dummy.next;
    }
}; 
```

### 源码分析

引入`dummy`节点后画个图分析下就能确定`head`和`preDel`的转移关系了。