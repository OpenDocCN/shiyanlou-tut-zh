# 第 31 节 Data Structure - 数据结构

本章主要总结数据结构如 Queue, Stack 等相关的题。

## Implement Queue by Two Stacks

## Question

*   lintcode: [(40) Implement Queue by Two Stacks](http://www.lintcode.com/en/problem/implement-queue-by-two-stacks/)

```
As the title described, you should only use two stacks to implement a queue's actions.

The queue should support push(element), 
pop() and top() where pop is pop the first(a.k.a front) element in the queue.

Both pop and top methods should return the value of first element.

Example
For push(1), pop(), push(2), push(3), top(), pop(), you should return 1, 2 and 2

Challenge
implement it by two stacks, do not use any other data structure and push, 
pop and top should be O(1) by AVERAGE. 
```

## 题解

两个栈模拟队列，栈是 LIFO, 队列是 FIFO, 故用两个栈模拟队列时可结合栈 1 和栈 2, LIFO + LIFO ==> FIFO, 即先将一个栈元素全部 push 到另一个栈，效果即等价于 Queue.

### Java

```
public class Solution {
    private Stack<Integer> stack1;
    private Stack<Integer> stack2;

    public Solution() {
        // source stack
        stack1 = new Stack<Integer>();
        // target stack
        stack2 = new Stack<Integer>();
    }

    public void push(int element) {
        stack1.push(element);
    }

    public int pop() {
        if (stack2.empty()) {
            stack1ToStack2(stack1, stack2);
        }
        return stack2.pop();
    }

    public int top() {
        if (stack2.empty()) {
            stack1ToStack2(stack1, stack2);
        }
        return stack2.peek();
    }

    private void stack1ToStack2(Stack<Integer> stack1, Stack<Integer> stack2) {
        while (!stack1.empty()) {
            stack2.push(stack1.pop());
        }
    }
} 
```

### 源码分析

将栈 1 作为原始栈，将栈 1 元素压入栈 2 是公共方法，故写成一个私有方法。

### 复杂度分析

视连续 push 的元素而定，时间复杂度近似为 $$O(1)$$.

## Reference

*   [Implement Queue by Two Stacks 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/implement-queue-by-two-stacks/)

## Min Stack

## Question

*   lintcode: [(12) Min Stack](http://www.lintcode.com/en/problem/min-stack/)

```
Implement a stack with min() function,
which will return the smallest number in the stack.

It should support push, pop and min operation all in O(1) cost.

Example
Operations: push(1), pop(), push(2), push(3), min(), push(1), min() Return: 1, 2, 1

Note
min operation will never be called if there is no number in the stack 
```

## 题解

『最小』栈，要求在栈的基础上实现可以在 $$O(1)$$ 的时间内找出最小值，一般这种 $$O(1)$$的实现往往就是哈希表或者哈希表的变体，这里简单起见可以另外克隆一个栈用以跟踪当前栈的最小值。

### Java

```
public class Solution {
    public Solution() {
        stack1 = new Stack<Integer>();
        stack2 = new Stack<Integer>();
    }

    public void push(int number) {
        stack1.push(number);
        if (stack2.empty()) {
            stack2.push(number);
        } else {
            stack2.push(Math.min(number, stack2.peek()));
        }
    }

    public int pop() {
        stack2.pop();
        return stack1.pop();
    }

    public int min() {
        return stack2.peek();
    }

    private Stack<Integer> stack1; // original stack
    private Stack<Integer> stack2; // min stack
} 
```

### 源码分析

取最小栈的栈顶值时需要先判断是否为空栈(而不仅是 null)。

### 复杂度分析

均为 $$O(1)$$.

## Sliding Window Maximum

## Question

*   leetcode: [Sliding Window Maximum | LeetCode OJ](https://leetcode.com/problems/sliding-window-maximum/)
*   lintcode: [(362) Sliding Window Maximum](http://www.lintcode.com/en/problem/sliding-window-maximum/)

```
Given an array of n integer with duplicate number, and a moving window(size k),
move the window at each iteration from the start of the array,
find the maximum number inside the window at each moving.

Example
For array [1, 2, 7, 7, 8], moving window size k = 3\. return [7, 7, 8]

At first the window is at the start of the array like this

[|1, 2, 7| ,7, 8] , return the maximum 7;

then the window move one step forward.

[1, |2, 7 ,7|, 8], return the maximum 7;

then the window move one step forward again.

[1, 2, |7, 7, 8|], return the maximum 8;

Challenge
o(n) time and O(k) memory 
```

## 题解

$$O(nk)$$ 的时间复杂度的方法很容易想到，不停地从当前窗口中取最大就好了。但其实可以发现下一个窗口的最大值与当前窗口的最大值其实是有一定关系的，但这个关系不是简单的将前一个窗口的最大值传递给下一个窗口，**因为数组中每一个元素都是有其作用范围的，超过窗口长度后就失效了！**所以现在思路就稍微清晰一些了，将前一个窗口的最大值传递给下一个窗口时需要判断当前遍历的元素下标和前一个窗口的最大元素下标之差是否已经超过一个窗口长度。

问题来了，思路基本定型，现在就是选用合适的数据结构了。根据上面的思路，这种数据结构应该能在 $$O(1)$$ 的时间内返回最大值，且存储的元素最大可以不超过窗口长度。常规一点的可以采用队列，但是此题中使用普通队列似乎还是很难实现，因为要在 $$O(1)$$ 的时间内返回最大值。符合这个要求的数据结构必须能支持从两端对队列元素进行维护，其中一种实现方法为队首维护最大值，队尾用于插入新元素。双端队列无疑了，有关双端队列的科普见 [双端队列](https://zh.wikipedia.org/wiki/%E5%8F%8C%E7%AB%AF%E9%98%9F%E5%88%97)。可以自己试着以一个实际例子来帮助理解。

### Java

```
public class Solution {
    /**
     * @param nums: A list of integers.
     * @return: The maximum number inside the window at each moving.
     */
    public ArrayList<Integer> maxSlidingWindow(int[] nums, int k) {
        ArrayList<Integer> winMax = new ArrayList<Integer>();
        if (nums == null || nums.length == 0 || k <= 0) return winMax;

        int len = nums.length;
        Deque<Integer> deque = new ArrayDeque<Integer>();
        for (int i = 0; i < len; i++) {
            // remove the smaller in the rear of queue
            while ((!deque.isEmpty()) && (nums[i] > deque.peekLast())) {
                deque.pollLast();
            }
            // push element in the rear of queue
            deque.offer(nums[i]);
            // remove invalid max
            if (i + 1 > k && deque.peekFirst() == nums[i - k]) {
                deque.pollFirst();
            }
            // add max in current window
            if (i + 1 >= k) {
                winMax.add(deque.peekFirst());
            }
        }

        return winMax;
    }
} 
```

### 源码分析

1.  移除队尾元素时首先判断是否为空，因为在移除过程中可能会将队列元素清空。
2.  在移除队尾元素时`nums[i] > deque.peekLast()`不可取等于号，因为这样会将相等的元素全部移除，这样会在窗口中部分元素相等时错误地移除本该添加到最终结果的元素。
3.  移除失效元素和添加元素到最终结果时需要注意下标`i`和`k`的关系，建议举例确定。

### 复杂度分析

时间复杂度 $$O(n)$$, 空间复杂度 $$O(k)$$. 空间复杂度可能不是那么直观，可以这么理解，双端队列中的元素最多只能存活 k 次，因为只有最大元素的存活时间最久，而最大元素在超过窗口长度时即被移除，故空间复杂度为 $$O(k)$$.

## Reference

*   《剑指 Offer》
*   [sliding-window-maximum 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/sliding-window-maximum/)
*   [Maximum of all subarrays of size k (Added a O(n) method) - GeeksforGeeks](http://www.geeksforgeeks.org/maximum-of-all-subarrays-of-size-k/)

## Longest Words

## Question

*   lintcode: [(133) Longest Words](http://www.lintcode.com/en/problem/longest-words/)

```
Given a dictionary, find all of the longest words in the dictionary.

Example
Given

{
  "dog",
  "google",
  "facebook",
  "internationalization",
  "blabla"
}
the longest words are(is) ["internationalization"].

Given

{
  "like",
  "love",
  "hate",
  "yes"
}
the longest words are ["like", "love", "hate"].

Challenge
It's easy to solve it in two passes, can you do it in one pass? 
```

## 题解

简单题，容易想到的是首先遍历以便，找到最长的字符串，第二次遍历时取最长的放到最终结果中。但是如果只能进行一次遍历呢？一次遍历意味着需要维护当前遍历的最长字符串，这必然有比较与更新删除操作，这种情况下使用双端队列最为合适，这道题稍微特殊一点，不必从尾端插入，只需在遍历时若发现比数组中最长的元素还长时删除整个列表。

### Java

```
class Solution {
    /**
     * @param dictionary: an array of strings
     * @return: an arraylist of strings
     */
    ArrayList<String> longestWords(String[] dictionary) {
        ArrayList<String> result = new ArrayList<String>();
        if (dictionary == null || dictionary.length == 0) return result;

        for (String str : dictionary) {
            // combine empty and shorter length
            if (result.isEmpty() || str.length() > result.get(0).length()) {
                result.clear();
                result.add(str);
            } else if (str.length() == result.get(0).length()) {
                result.add(str);
            }
        }

        return result;
    }
} 
```

### 源码分析

熟悉变长数组的常用操作。

### 复杂度分析

时间复杂度 $$O(n)$$, 最坏情况下需要保存 n - 1 个字符串，空间复杂度 $$O(n)$$.

## Reference

*   [Lintcode: Longest Words | codesolutiony](https://codesolutiony.wordpress.com/2015/06/07/lintcode-longest-words/)

## Heapify

## Question

*   lintcode: [(130) Heapify](http://www.lintcode.com/en/problem/heapify/)

```
Given an integer array, heapify it into a min-heap array.

For a heap array A, A[0] is the root of heap, and for each A[i],
A[i * 2 + 1] is the left child of A[i] and A[i * 2 + 2] is the right child of A[i].

Example
Given [3,2,1,4,5], return [1,2,3,4,5] or any legal heap array.

Challenge
O(n) time complexity

Clarification
What is heap?

Heap is a data structure, which usually have three methods: push, pop and top.
where "push" add a new element the heap,
"pop" delete the minimum/maximum element in the heap,
"top" return the minimum/maximum element.

What is heapify?
Convert an unordered integer array into a heap array.
If it is min-heap, for each element A[i],
we will get A[i * 2 + 1] >= A[i] and A[i * 2 + 2] >= A[i].

What if there is a lot of solutions?
Return any of them. 
```

## 题解

参考前文提到的 [Heap Sort](http://algorithm.yuanbin.me/zh-hans/basics_sorting/heap_sort.html) 可知此题要实现的只是小根堆的堆化过程，并不要求堆排。

### C++

```
class Solution {
public:
    /**
     * @param A: Given an integer array
     * @return: void
     */
    void heapify(vector<int> &A) {
        // build min heap
        for (int i = A.size() / 2; i >= 0; --i) {
            min_heap(A, i);
        }
    }

private:
    void min_heap(vector<int> &nums, int k) {
        int len = nums.size();
        while (k < len) {
            int min_index = k;
            // left leaf node search
            if (k * 2 + 1 < len && nums[k * 2 + 1] < nums[min_index]) {
                min_index = k * 2 + 1;
            }
            // right leaf node search
            if (k * 2 + 2 < len && nums[k * 2 + 2] < nums[min_index]) {
                min_index = k * 2 + 2;
            }
            if (k == min_index) {
                break;
            }
            // swap with the minimal
            int temp = nums[k];
            nums[k] = nums[min_index];
            nums[min_index] = temp;
            // not only current index
            k = min_index;
        }
    }
}; 
```

### 源码分析

堆排的简化版，最后一步`k = min_index`不能忘，因为增删节点时需要重新建堆，这样才能保证到第一个节点时数组已经是二叉堆。

### 复杂度分析

由于采用的是自底向上的建堆方式，时间复杂度为 $$(N)$$, 证明待补充...

## Reference

*   [Heap Sort](http://algorithm.yuanbin.me/zh-hans/basics_sorting/heap_sort.html)
*   [Heapify 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/heapify/)