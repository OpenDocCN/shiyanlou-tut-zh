# 第 8 节 Binary Search - 二分搜索（三）

## Median of two Sorted Arrays

## Question

*   leetcode: [Median of Two Sorted Arrays | LeetCode OJ](https://leetcode.com/problems/median-of-two-sorted-arrays/)
*   lintcode: [(65) Median of two Sorted Arrays](http://www.lintcode.com/en/problem/median-of-two-sorted-arrays/)

### Problem Statement

There are two sorted arrays *A* and *B* of size *m* and *n* respectively. Find the **median** of the two sorted arrays.

#### Example

Given `A=[1,2,3,4,5,6]` and `B=[2,3,4,5]`, the median is `3.5`.

Given `A=[1,2,3]` and `B=[4,5]`, the median is `3`.

#### Challenge

The overall run time complexity should be O(log (m+n)).

## 题解 1 - 归并排序

何谓"Median"? 由题目意思可得即为两个数组中一半数据比它大，另一半数据比它小的那个数。详见 [中位数 - 维基百科，自由的百科全书](http://zh.wikipedia.org/wiki/%E4%B8%AD%E4%BD%8D%E6%95%B8)。简单粗暴的方法就是使用归并排序的思想，挨个比较两个数组的值，取小的，最后分奇偶长度返回平均值或者中位值。

### Java1 - merge sort with equal length

```
class Solution {
    /**
     * @param A: An integer array.
     * @param B: An integer array.
     * @return: a double whose format is *.5 or *.0
     */
    public double findMedianSortedArrays(int[] A, int[] B) {
        if ((A == null || A.length == 0) && (B == null || B.length == 0)) {
            return -1.0;
        }
        int lenA = (A == null) ? 0 : A.length;
        int lenB = (B == null) ? 0 : B.length;
        int len = lenA + lenB;

        /* merge sort */
        int indexA = 0, indexB = 0, indexC = 0;
        int[] C = new int[len];
        // case1: both A and B have elements
        while (indexA < lenA && indexB < lenB) {
            if (A[indexA] < B[indexB]) {
                C[indexC++] = A[indexA++];
            } else {
                C[indexC++] = B[indexB++];
            }
        }
        // case2: only A has elements
        while (indexA < lenA) {
            C[indexC++] = A[indexA++];
        }
        // case3: only B has elements
        while (indexB < lenB) {
            C[indexC++] = B[indexB++];
        }

        // return median for even and odd cases
        int indexM1 = (len - 1) / 2, indexM2 = len / 2;
        if (len % 2 == 0) {
            return (C[indexM1] + C[indexM2]) / 2.0;
        } else {
            return C[indexM2];
        }
    }
} 
```

### Java2 - space optimization

```
class Solution {
    /**
     * @param A: An integer array.
     * @param B: An integer array.
     * @return: a double whose format is *.5 or *.0
     */
    public double findMedianSortedArrays(int[] A, int[] B) {
        if ((A == null || A.length == 0) && (B == null || B.length == 0)) {
            return -1.0;
        }
        int lenA = (A == null) ? 0 : A.length;
        int lenB = (B == null) ? 0 : B.length;
        int len = lenA + lenB;
        int indexM1 = (len - 1) / 2, indexM2 = len / 2;
        int m1 = 0, m2 = 0;

        /* merge sort */
        int indexA = 0, indexB = 0, indexC = 0;
        // case1: both A and B have elements
        while (indexA < lenA && indexB < lenB) {
            if (indexC > indexM2) {
                break;
            }
            if (indexC == indexM1) {
                m1 = Math.min(A[indexA], B[indexB]);
            }
            if (indexC == indexM2) {
                m2 = Math.min(A[indexA], B[indexB]);
            }
            if (A[indexA] < B[indexB]) {
                indexA++;
            } else {
                indexB++;
            }
            indexC++;
        }
        // case2: only A has elements
        while (indexA < lenA) {
            if (indexC > indexM2) {
                break;
            }
            if (indexC == indexM1) {
                m1 = A[indexA];
            }
            if (indexC == indexM2) {
                m2 = A[indexA];
            }
            indexA++;
            indexC++;
        }
        // case3: only B has elements
        while (indexB < lenB) {
            if (indexC > indexM2) {
                break;
            }
            if (indexC == indexM1) {
                m1 = B[indexB];
            }
            if (indexC == indexM2) {
                m2 = B[indexB];
            }
            indexB++;
            indexC++;
        }

        // return median for even and odd cases
        if (len % 2 == 0) {
            return (m1 + m2) / 2.0;
        } else {
            return m2;
        }
    }
} 
```

### 源码分析

使用归并排序的思想做这道题不难，但是边界条件的处理比较闹心，使用归并排序带辅助空间的做法实现起来比较简单，代码也短。如果不使用额外空间并做一定优化的话需要多个 if 语句进行判断，需要注意的是多个 if 之间不能使用 else ，因为`indexM1`和`indexM2`有可能相等。

### 复杂度分析

时间复杂度 $$O(m + n)$$, 空间复杂度为 $$(m + n)$$(使用额外数组), 或者 $$O(1)$$(不使用额外数组).

## 题解 2 - 二分搜索

题中已有信息两个数组均为有序，找中位数的关键在于找到第一半大的数，显然可以使用二分搜索优化。本题是找中位数，其实可以泛化为一般的找第 k 大数，这个辅助方法的实现非常有意义！在两个数组中找第 k 大数->找中位数即为找第 k 大数的一个特殊情况——第(A.length + B.length) / 2 大数。因此首先需要解决找第 k 大数的问题。这个联想确实有点牵强...

由于是找第 k 大数(从 1 开始)，使用二分法则需要比较 A[k/2 - 1]和 B[k/2 - 1]，并思考这两个元素和第 k 大元素的关系。

1.  A[k/2 - 1] <= B[k/2 - 1] => A 和 B 合并后的第 k 大数中必包含 A[0]~A[k/2 -1]，可使用归并的思想去理解。
2.  若 k/2 - 1 超出 A 的长度，则必取 B[0]~B[k/2 - 1]

### C++

```
class Solution {
public:
    /**
     * @param A: An integer array.
     * @param B: An integer array.
     * @return: a double whose format is *.5 or *.0
     */
    double findMedianSortedArrays(vector<int> A, vector<int> B) {
        if (A.empty() && B.empty()) {
            return 0;
        }

        vector<int> NonEmpty;
        if (A.empty()) {
            NonEmpty = B;
        }
        if (B.empty()) {
            NonEmpty = A;
        }
        if (!NonEmpty.empty()) {
            vector<int>::size_type len_vec = NonEmpty.size();
            return len_vec % 2 == 0 ?
                    (NonEmpty[len_vec / 2 - 1] + NonEmpty[len_vec / 2]) / 2.0 :
                    NonEmpty[len_vec / 2];
        }

        vector<int>::size_type len = A.size() + B.size();
        if (len % 2 == 0) {
            return ((findKth(A, 0, B, 0, len / 2) + findKth(A, 0, B, 0, len / 2 + 1)) / 2.0);
        } else {
            return findKth(A, 0, B, 0, len / 2 + 1);
        }
        // write your code here
    }

private:
    int findKth(vector<int> &A, vector<int>::size_type A_start, vector<int> &B, vector<int>::size_type B_start, int k) {
        if (A_start > A.size() - 1) {
            // all of the element of A are smaller than the kTh number
            return B[B_start + k - 1];
        }
        if (B_start > B.size() - 1) {
            // all of the element of B are smaller than the kTh number
            return A[A_start + k - 1];
        }

        if (k == 1) {
            return A[A_start] < B[B_start] ? A[A_start] : B[B_start];
        }

        int A_key = A_start + k / 2 - 1 < A.size() ?
                    A[A_start + k / 2 - 1] : INT_MAX;
        int B_key = B_start + k / 2 - 1 < B.size() ?
                    B[B_start + k / 2 - 1] : INT_MAX;

        if (A_key > B_key) {
            return findKth(A, A_start, B, B_start + k / 2, k - k / 2);
        } else {
            return findKth(A, A_start + k / 2, B, B_start, k - k / 2);
        }
    }
}; 
```

### Java

```
class Solution {
    /**
     * @param A: An integer array.
     * @param B: An integer array.
     * @return: a double whose format is *.5 or *.0
     */
    public double findMedianSortedArrays(int[] A, int[] B) {
        if ((A == null || A.length == 0) && (B == null || B.length == 0)) {
            return -1.0;
        }
        int lenA = (A == null) ? 0 : A.length;
        int lenB = (B == null) ? 0 : B.length;
        int len = lenA + lenB;

        // return median for even and odd cases
        if (len % 2 == 0) {
            return (findKth(A, 0, B, 0, len/2) + findKth(A, 0, B, 0, len/2 + 1)) / 2.0;
        } else {
            return findKth(A, 0, B, 0, len/2 + 1);
        }
    }

    private int findKth(int[] A, int indexA, int[] B, int indexB, int k) {

        int lenA = (A == null) ? 0 : A.length;
        if (indexA > lenA - 1) {
            return B[indexB + k - 1];
        }
        int lenB = (B == null) ? 0 : B.length;
        if (indexB > lenB - 1) {
            return A[indexA + k - 1];
        }

        // avoid infilite loop if k == 1
        if (k == 1) return Math.min(A[indexA], B[indexB]);

        int keyA = Integer.MAX_VALUE, keyB = Integer.MAX_VALUE;
        if (indexA + k/2 - 1 < lenA) keyA = A[indexA + k/2 - 1];
        if (indexB + k/2 - 1 < lenB) keyB = B[indexB + k/2 - 1];

        if (keyA > keyB) {
            return findKth(A, indexA, B, indexB + k/2, k - k/2);
        } else {
            return findKth(A, indexA + k/2, B, indexB, k - k/2);
        }
    }
} 
```

### 源码分析

本题用非递归的方法非常麻烦，递归的方法减少了很多边界的判断。此题的边界条件较多，不容易直接从代码看清思路。首先分析找 k 大的辅助程序。以 Java 的代码为例。

1.  首先在主程序中排除 A, B 均为空的情况。
2.  排除 A 或者 B 中有一个为空或者长度为 0 的情况。如果`A_start > A.size() - 1`，意味着 A 中无数提供，故仅能从 B 中取，所以只能是 B 从`B_start`开始的第 k 个数。下面的 B...分析方法类似。
3.  k 为 1 时，无需再递归调用，直接返回较小值。如果 k 为 1 不返回将导致后面的无限循环。
4.  以 A 为例，取出自`A_start`开始的第`k / 2`个数，若下标`A_start + k / 2 - 1 < A.size()`，则可取此下标对应的元素，否则置为 int 的最大值，便于后面进行比较，免去了诸多边界条件的判断。
5.  比较`A_key > B_key`，取小的折半递归调用 findKth。

接下来分析`findMedianSortedArrays`：

1.  首先考虑异常情况，A, B 都为空。
2.  A+B 的长度为偶数时返回 len / 2 和 len / 2 + 1 的均值，为奇数时则返回 len / 2 + 1

### 复杂度分析

找中位数，K 为数组长度和的一半，故总的时间复杂度为 $$O(\log (m+n))$$.

## Reference

*   [九章算法 | Median of Two Sorted Arrays](http://www.jiuzhang.com/solutions/median-of-two-sorted-arrays/)
*   [LeetCode: Median of Two Sorted Arrays 解题报告 - Yu's Garden - 博客园](http://www.cnblogs.com/yuzhangcmu/p/4138184.html)

## Sqrt x

## Question

*   leetcode: [Sqrt(x) | LeetCode OJ](https://leetcode.com/problems/sqrtx/)
*   lintcode: [(141) Sqrt(x)](http://www.lintcode.com/en/problem/sqrtx/)

## 题解 - 二分搜索

由于只需要求整数部分，故对于任意正整数 $$x$$, 设其整数部分为 $$k$$, 显然有 $$1 \leq k \leq x$$, 求解 $$k$$ 的值也就转化为了在有序数组中查找满足某种约束条件的元素，显然二分搜索是解决此类问题的良方。

### Python

```
class Solution:
    # @param {integer} x
    # @return {integer}
    def mySqrt(self, x):
        if x < 0:
            return -1
        elif x == 0:
            return 0

        start, end = 1, x
        while start + 1 < end:
            mid = start + (end - start) / 2
            if mid**2 == x:
                return mid
            elif mid**2 > x:
                end = mid
            else:
                start = mid

        return start 
```

### C++

```
int sqrt(int x) {
    // write your code here
    if (x <= 0) return 0;

    int lb = 0, ub = x;
    while (lb + 1 < ub) {
        long long mid = lb + (ub - lb) / 2; 
        if (mid * mid == x) return mid; 
        if (mid * mid < x) lb = mid;
        else ub = mid;
    }
    return lb;
} 
```

### 源码分析

1.  异常检测，先处理小于等于 0 的值。
2.  使用二分搜索的经典模板，注意不能使用`start < end`, 否则在给定值 1 时产生死循环。
3.  最后返回平方根的整数部分`start`.
4.  C++代码 mid 需要定义为 long long，否则计算平方时会溢出

二分搜索过程很好理解，关键是最后的返回结果还需不需要判断？比如是取 start, end, 还是 mid? 我们首先来分析下二分搜索的循环条件，由`while`循环条件`start + 1 < end`可知，`start`和`end`只可能有两种关系，一个是`end == 1 || end ==2`这一特殊情况，返回值均为 1，另一个就是循环终止时`start`恰好在`end`前一个元素。设值 x 的整数部分为 k, 那么在执行二分搜索的过程中 $$ start \leq k \leq end$$ 关系一直存在，也就是说在没有找到 $$mid² == x$$ 时，循环退出时有 $$start < k < end$$, 取整的话显然就是`start`了。

### 复杂度分析

经典的二分搜索，时间复杂度为 $$O(\log n)$$, 使用了`start`, `end`, `mid`变量，空间复杂度为 $$O(1)$$.

除了使用二分法求平方根近似解之外，还可使用牛顿迭代法进一步提高运算效率，欲知后事如何，请猛戳 [求平方根 sqrt()函数的底层算法效率问题 -- 简明现代魔法](http://www.nowamagic.net/algorithm/algorithm_EfficacyOfFunctionSqrt.php)，不得不感叹算法的魔力！

## Wood Cut

## Question

*   lintcode: [(183) Wood Cut](http://www.lintcode.com/en/problem/wood-cut/)

### Problem Statement

Given n pieces of wood with length `L[i]` (integer array). Cut them into small pieces to guarantee you could have equal or more than k pieces with the same length. What is the longest length you can get from the n pieces of wood? Given L & k, return the maximum length of the small pieces.

#### Example

For `L=[232, 124, 456]`, `k=7`, return `114`.

#### Note

You couldn't cut wood into float length.

#### Challenge

O(n log Len), where Len is the longest length of the wood.

## 题解 - 二分搜索

这道题要直接想到二分搜素其实不容易，但是看到题中 Challenge 的提示后你大概就能想到往二分搜索上靠了。首先来分析下题意，题目意思是说给出 n 段木材`L[i]`, 将这 n 段木材切分为至少 k 段，这 k 段等长，求能从 n 段原材料中获得的最长单段木材长度。以 k=7 为例，要将 L 中的原材料分为 7 段，能得到的最大单段长度为 114, 232/114 = 2, 124/114 = 1, 456/114 = 4, 2 + 1 + 4 = 7.

理清题意后我们就来想想如何用算法的形式表示出来，显然在计算如`2`, `1`, `4`等分片数时我们进行了取整运算，在计算机中则可以使用下式表示： $$\sum _{i = 1} ^{n} \frac {L[i]}{l} \geq k$$

其中 $$l$$ 为单段最大长度，显然有 $$1 \leq l \leq max(L[i])$$. 单段长度最小为 1，最大不可能超过给定原材料中的最大木材长度。

> **Warning** 注意求和与取整的顺序，是先求 `L[i]/l`的单个值，而不是先对`L[i]`求和。

分析到这里就和题 [Sqrt x](http://algorithm.yuanbin.me/zh-hans/binary_search/sqrt_x.html) 差不多一样了，要求的是 $$l$$ 的最大可能取值，同时 $$l$$ 可以看做是从有序序列`[1, max(L[i])]`的一个元素，典型的二分搜素！

P.S. 关于二分搜索总结在 [Binary Search](http://algorithm.yuanbin.me/zh-hans/basics_algorithm/binary_search.html) 一小节，直接套用『模板二——最优化』即可。

### Python

```
class Solution:
    """
    @param L: Given n pieces of wood with length L[i]
    @param k: An integer
    return: The maximum length of the small pieces.
    """
    def woodCut(self, L, k):
        if sum(L) < k:
            return 0

        start, end = 1, max(L)
        while start + 1 < end:
            mid = (start + end) / 2
            pieces_sum = sum(len_i / mid for len_i in L)
            if pieces_sum < k:
                end = mid
            else:
                start = mid

        if sum(len_i / end for len_i in L) >= k:
            return end
        return start 
```

### C++

```
class Solution {
public:
    /** 
     *@param L: Given n pieces of wood with length L[i]
     *@param k: An integer
     *return: The maximum length of the small pieces.
     */
    int woodCut(vector<int> L, int k) {
        // write your code here
        int lb = 0, ub = 0;
        for (auto l : L) if (l + 1 > ub) ub = l + 1;

        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (C(L, k, mid)) lb = mid;
            else ub = mid;
        }
        return lb;
    }

    int C(vector<int> L, int k, int mid) {
        int sum = 0;
        for (auto l : L) {
            sum += l / mid;
        }
        return sum >= k;
    }
}; 
```

### Java

```
public class Solution {
    /**
     *@param L: Given n pieces of wood with length L[i]
     *@param k: An integer
     *return: The maximum length of the small pieces.
     */
    public int woodCut(int[] L, int k) {
        if (L == null || L.length == 0) return 0;

        int lb = 0, ub = Integer.MIN_VALUE;
        // get the upper bound of L
        for (int l : L) if (l > ub) ub = l + 1;

        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (C(L, k, mid)) {
                lb = mid;
            } else {
                ub = mid;
            }
        }

        return lb;
    }

    // whether it cut with length x and get more than k pieces
    private boolean C(int[] L, int k, int x) {
        int sum = 0;
        for (int l : L) {
            sum += l / x;
        }
        return sum >= k;
    }
} 
```

### 源码分析

定义私有方法`C`为切分为 x 长度时能否大于等于 k 段。若满足条件则更新`lb`, 由于 lb 和 ub 的初始化技巧使得我们无需单独对最后的 lb 和 ub 单独求和判断。九章算法网站上的方法初始化为 1 和某最大值，还需要单独判断，虽然不会出 bug, 但稍显复杂。这个时候 lb, ub 初始化为两端不满足条件的值的优雅之处就体现出来了。

### 复杂度分析

遍历求和时间复杂度为 $$O(n)$$, 二分搜索时间复杂度为 $$O(\log max(L))$$. 故总的时间复杂度为 $$O(n \log max(L))$$. 空间复杂度 $$O(1)$$.

## Reference

*   [Binary Search](http://algorithm.yuanbin.me/zh-hans/basics_algorithm/binary_search.html)