# 第 7 节 Binary Search - 二分搜索（二）

## Find Peak Element

## Question

*   leetcode: [Find Peak Element | LeetCode OJ](https://leetcode.com/problems/find-peak-element/)
*   lintcode: [(75) Find Peak Element](http://www.lintcode.com/en/problem/find-peak-element/)

### Problem Statement

A peak element is an element that is greater than its neighbors.

Given an input array where `num[i] ≠ num[i+1]`, find a peak element and return its index.

The array may contain multiple peaks, in that case return the index to any one of the peaks is fine.

You may imagine that `num[-1] = num[n] = -∞`.

For example, in array `[1, 2, 3, 1]`, 3 is a peak element and your function should return the index number 2.

#### Note:

Your solution should be in logarithmic complexity.

#### Credits:

Special thanks to [@ts](https://oj.leetcode.com/discuss/user/ts) for adding this problem and creating all test cases.

## 题解 1

由时间复杂度的暗示可知应使用二分搜索。首先分析若使用传统的二分搜索，若`A[mid] > A[mid - 1] && A[mid] < A[mid + 1]`，则找到一个 peak 为 A[mid]；若`A[mid - 1] > A[mid]`，则 A[mid]左侧必定存在一个 peak，可用反证法证明：若左侧不存在 peak，则 A[mid]左侧元素必满足`A[0] > A[1] > ... > A[mid -1] > A[mid]`，与已知`A[0] < A[1]`矛盾，证毕。同理可得若`A[mid + 1] > A[mid]`，则 A[mid]右侧必定存在一个 peak。如此迭代即可得解。 由于题中假设端点外侧的值均为负无穷大，即`num[-1] < num[0] && num[n-1] > num[n]`, 那么问题来了，这样一来就不能确定峰值一定存在了，因为给定数组为单调序列的话就咩有峰值了，但是实际情况是——题中有负无穷的假设，也就是说在单调序列的情况下，峰值为数组首部或者尾部元素，谁大就是谁了。

备注：如果本题是找 first/last peak，就不能用二分法了。

### Python

```
class Solution:
    #@param A: An integers list.
    #@return: return any of peek positions.
    def findPeak(self, A):
        if not A:
            return -1

        l, r = 0, len(A) - 1
        while l + 1 < r:
            mid = l + (r - l) / 2
            if A[mid] < A[mid - 1]:
                r = mid
            elif A[mid] < A[mid + 1]:
                l = mid
            else:
                return mid
        mid = l if A[l] > A[r] else r
        return mid 
```

### C++

```
class Solution {
public:
    /**
     * @param A: An integers array.
     * @return: return any of peek positions.
     */
    int findPeak(vector<int> A) {
        if (A.size() == 0) return -1;

        int l = 0, r = A.size() - 1;
        while (l + 1 < r) {
            int mid = l + (r - l) / 2;
            if (A[mid] < A[mid - 1]) {
                r = mid;
            } else if (A[mid] < A[mid + 1]) {
                l = mid;
            } else {
                return mid;
            }
        }

        int mid = A[l] > A[r] ? l : r;
        return mid;
    }
}; 
```

### Java

```
class Solution {
    /**
     * @param A: An integers array.
     * @return: return any of peek positions.
     */
    public int findPeak(int[] A) {
        if (A == null || A.length == 0) return -1;

        int lb = 0, ub = A.length - 1;
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (A[mid] < A[mid + 1]) {
                lb = mid;
            } else if (A[mid] < A[mid - 1]){
                ub = mid;
            } else {
                // find a peak
                return mid;
            }
        }

        // return a larger number
        return A[lb] > A[ub] ? lb : ub;
    }
} 
```

### 源码分析

典型的二分法模板应用，需要注意的是需要考虑单调序列的特殊情况。当然也可使用紧凑一点的实现如改写循环条件为`l < r`，这样就不用考虑单调序列了，见实现 2.

### 复杂度分析

二分法，时间复杂度 $$O(\log n)$$.

#### Java - compact implementation[^leetcode_discussion]

```
public class Solution {
    public int findPeakElement(int[] nums) {
        if (nums == null || nums.length == 0) {
            return -1;
        }

        int start = 0, end = nums.length - 1, mid = end / 2;
        while (start < end) {
            if (nums[mid] < nums[mid + 1]) {
                // 1 peak at least in the right side
                start = mid + 1;
            } else {
                // 1 peak at least in the left side
                end = mid;
            }
            mid = start + (end - start) / 2;
        }

        return start;
    }
} 
```

C++ 的代码可参考 Java 或者 @xuewei4d 的实现。

> **Warning** leetcode 和 lintcode 上给的方法名不一样，leetcode 上的为`findPeakElement`而 lintcode 上为`findPeak`，弄混的话会编译错误。

## Reference

*   [^leetcode_discussion]: [Java - Binary-Search Solution - Leetcode Discuss](https://leetcode.com/discuss/23840/java-binary-search-solution)

## Search in Rotated Sorted Array

## Question

*   leetcode: [Search in Rotated Sorted Array | LeetCode OJ](https://leetcode.com/problems/search-in-rotated-sorted-array/)
*   lintcode: [(62) Search in Rotated Sorted Array](http://www.lintcode.com/en/problem/search-in-rotated-sorted-array/)

### Problem Statement

Suppose a sorted array is rotated at some pivot unknown to you beforehand.

(i.e., `0 1 2 4 5 6 7` might become `4 5 6 7 0 1 2`).

You are given a target value to search. If found in the array return its index, otherwise return -1.

You may assume no duplicate exists in the array.

#### Example

For `[4, 5, 1, 2, 3]` and `target=1`, return `2`.

For `[4, 5, 1, 2, 3]` and `target=0`, return `-1`.

#### Challenge

O(logN) time

## 题解 - 找到有序数组

对于旋转数组的分析可使用画图的方法，如下图所示，升序数组经旋转后可能为如下两种形式。

![Rotated Array](img/579da4eaa569feaebf229fde9b600b88.jpg)

对于有序数组，使用二分搜索比较方便。分析题中的数组特点，旋转后初看是乱序数组，但仔细一看其实里面是存在两段有序数组的。刚开始做这道题时可能会去比较`target`和`A[mid]`, 但分析起来异常复杂。**该题较为巧妙的地方在于如何找出旋转数组中的局部有序数组，并使用二分搜索解之。**结合实际数组在纸上分析较为方便。

### C++

```
/**
 * 本代码 fork 自
 * http://www.jiuzhang.com/solutions/search-in-rotated-sorted-array/
 */
class Solution {
    /**
     * param A : an integer ratated sorted array
     * param target :  an integer to be searched
     * return : an integer
     */
public:
    int search(vector<int> &A, int target) {
        if (A.empty()) {
            return -1;
        }

        vector<int>::size_type start = 0;
        vector<int>::size_type end = A.size() - 1;
        vector<int>::size_type mid;

        while (start + 1 < end) {
            mid = start + (end - start) / 2;
            if (target == A[mid]) {
                return mid;
            }
            if (A[start] < A[mid]) {
                // situation 1, numbers between start and mid are sorted
                if (A[start] <= target && target < A[mid]) {
                    end = mid;
                } else {
                    start = mid;
                }
            } else {
                // situation 2, numbers between mid and end are sorted
                if (A[mid] < target && target <= A[end]) {
                    start = mid;
                } else {
                    end = mid;
                }
            }
        }

        if (A[start] == target) {
            return start;
        }
        if (A[end] == target) {
            return end;
        }
        return -1;
    }
}; 
```

### Java

```
public class Solution {
    /**
     *@param A : an integer rotated sorted array
     *@param target :  an integer to be searched
     *return : an integer
     */
    public int search(int[] A, int target) {
        if (A == null || A.length == 0) return -1;

        int lb = 0, ub = A.length - 1;
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (A[mid] == target) return mid;

            if (A[mid] > A[lb]) {
                // case1: numbers between lb and mid are sorted
                if (A[lb] <= target && target <= A[mid]) {
                    ub = mid;
                } else {
                    lb = mid;
                }
            } else {
                // case2: numbers between mid and ub are sorted
                if (A[mid] <= target && target <= A[ub]) {
                    lb = mid;
                } else {
                    ub = mid;
                }
            }
        }

        if (A[lb] == target) {
            return lb;
        } else if (A[ub] == target) {
            return ub;
        }
        return -1;
    }
} 
```

### 源码分析

1.  若`target == A[mid]`，索引找到，直接返回
2.  寻找局部有序数组，分析`A[mid]`和两段有序的数组特点，由于旋转后前面有序数组最小值都比后面有序数组最大值大。故若`A[start] < A[mid]`成立，则 start 与 mid 间的元素必有序（要么是前一段有序数组，要么是后一段有序数组，还有可能是未旋转数组）。
3.  接着在有序数组`A[start]~A[mid]`间进行二分搜索，但能在`A[start]~A[mid]`间搜索的前提是`A[start] <= target <= A[mid]`。
4.  接着在有序数组`A[mid]~A[end]`间进行二分搜索，注意前提条件。
5.  搜索完毕时索引若不是 mid 或者未满足 while 循环条件，则测试 A[start]或者 A[end]是否满足条件。
6.  最后若未找到满足条件的索引，则返回-1.

### 复杂度分析

分两段二分，时间复杂度仍近似为 $$O(\log n)$$.

## Search in Rotated Sorted Array II

## Question

*   leetcode: [Search in Rotated Sorted Array II | LeetCode OJ](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/)
*   lintcode: [(63) Search in Rotated Sorted Array II](http://www.lintcode.com/en/problem/search-in-rotated-sorted-array-ii/)

### Problem Statement

Follow up for "Search in Rotated Sorted Array": What if *duplicates* are allowed?

Would this affect the run-time complexity? How and why?

Write a function to determine if a given target is in the array.

## 题解

仔细分析此题和之前一题的不同之处，前一题我们利用`A[start] < A[mid]`这一关键信息，而在此题中由于有重复元素的存在，在`A[start] == A[mid]`时无法确定有序数组，此时只能依次递增 start/递减 end 以缩小搜索范围，时间复杂度最差变为 O(n)。

### C++

```
class Solution {
    /**
     * param A : an integer ratated sorted array and duplicates are allowed
     * param target :  an integer to be search
     * return : a boolean
     */
public:
    bool search(vector<int> &A, int target) {
        if (A.empty()) {
            return false;
        }

        vector<int>::size_type start = 0;
        vector<int>::size_type end = A.size() - 1;
        vector<int>::size_type mid;

        while (start + 1 < end) {
            mid = start + (end - start) / 2;
            if (target == A[mid]) {
                return true;
            }
            if (A[start] < A[mid]) {
                // situation 1, numbers between start and mid are sorted
                if (A[start] <= target && target < A[mid]) {
                    end = mid;
                } else {
                    start = mid;
                }
            } else if (A[start] > A[mid]) {
                // situation 2, numbers between mid and end are sorted
                if (A[mid] < target && target <= A[end]) {
                    start = mid;
                } else {
                    end = mid;
                }
            } else  {
                // increment start
                ++start;
            }
        }

        if (A[start] == target || A[end] == target) {
            return true;
        }
        return false;
    }
}; 
```

### Java

```
public class Solution {
    /**
     * param A : an integer ratated sorted array and duplicates are allowed
     * param target :  an integer to be search
     * return : a boolean
     */
    public boolean search(int[] A, int target) {
        if (A == null || A.length == 0) return false;

        int lb = 0, ub = A.length - 1;
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (A[mid] == target) return true;

            if (A[mid] > A[lb]) {
                // case1: numbers between lb and mid are sorted
                if (A[lb] <= target && target <= A[mid]) {
                    ub = mid;
                } else {
                    lb = mid;
                }
            } else if (A[mid] < A[lb]) {
                // case2: numbers between mid and ub are sorted
                if (A[mid] <= target && target <= A[ub]) {
                    lb = mid;
                } else {
                    ub = mid;
                }
            } else {
                // case3: A[mid] == target
                lb++;
            }
        }

        if (target == A[lb] || target == A[ub]) {
            return true;
        }
        return false;
    }
} 
```

### 源码分析

在`A[start] == A[mid]`时递增 start 序号即可。

### 复杂度分析

最差情况下 $$O(n)$$, 平均情况下 $$O(\log n)$$.

## Find Minimum in Rotated Sorted Array

## Question

*   leetcode: [Find Minimum in Rotated Sorted Array | LeetCode OJ](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)
*   lintcode: [(159) Find Minimum in Rotated Sorted Array](http://www.lintcode.com/en/problem/find-minimum-in-rotated-sorted-array/)

### Problem Statement

Suppose a sorted array is rotated at some pivot unknown to you beforehand.

(i.e., `0 1 2 4 5 6 7` might become `4 5 6 7 0 1 2`).

Find the minimum element.

#### Example

Given `[4, 5, 6, 7, 0, 1, 2]` return `0`

#### Note

You may assume no duplicate exists in the array.

## 题解

如前节所述，对于旋转数组的分析可使用画图的方法，如下图所示，升序数组经旋转后可能为如下两种形式。

![Rotated Array](img/240686abf93dbafa12fec4e46b066fdf.jpg)

最小值可能在上图中的两种位置出现，如果仍然使用数组首部元素作为 target 去比较，则需要考虑图中右侧情况。**使用逆向思维分析，如果使用数组尾部元素分析，则无需图中右侧的特殊情况。**不过考虑在内的话也算是一种优化。

### C++

```
class Solution {
public:
    /**
     * @param num: a rotated sorted array
     * @return: the minimum number in the array
     */
    int findMin(vector<int> &num) {
        if (num.empty()) {
            return -1;
        }

        vector<int>::size_type start = 0;
        vector<int>::size_type end = num.size() - 1;
        vector<int>::size_type mid;
        while (start + 1 < end) {
            mid = start + (end - start) / 2;
            if (num[mid] < num[end]) {
                end = mid;
            } else {
                start = mid;
            }
        }

        if (num[start] < num[end]) {
            return num[start];
        } else {
            return num[end];
        }
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param num: a rotated sorted array
     * @return: the minimum number in the array
     */
    public int findMin(int[] num) {
        if (num == null || num.length == 0) return Integer.MIN_VALUE;

        int lb = 0, ub = num.length - 1;
        // case1: num[0] < num[num.length - 1]
        // if (num[lb] < num[ub]) return num[lb];

        // case2: num[0] > num[num.length - 1] or num[0] < num[num.length - 1]
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (num[mid] < num[ub]) {
                ub = mid;
            } else {
                lb = mid;
            }
        }

        return Math.min(num[lb], num[ub]);
    }
} 
```

### 源码分析

仅需注意使用`num[end]`(使用 num[lb]不是那么直观)作为判断依据即可，由于题中已给无重复数组的条件，故无需处理`num[mid] == num[end]`特殊条件。

### 复杂度分析

由于无重复元素，平均情况下复杂度为 $$O(\log n)$$.

## Find Minimum in Rotated Sorted Array

## Question

*   leetcode: [Find Minimum in Rotated Sorted Array | LeetCode OJ](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)
*   lintcode: [(159) Find Minimum in Rotated Sorted Array](http://www.lintcode.com/en/problem/find-minimum-in-rotated-sorted-array/)

### Problem Statement

Suppose a sorted array is rotated at some pivot unknown to you beforehand.

(i.e., `0 1 2 4 5 6 7` might become `4 5 6 7 0 1 2`).

Find the minimum element.

#### Example

Given `[4, 5, 6, 7, 0, 1, 2]` return `0`

#### Note

You may assume no duplicate exists in the array.

## 题解

如前节所述，对于旋转数组的分析可使用画图的方法，如下图所示，升序数组经旋转后可能为如下两种形式。

![Rotated Array](img/dc9c05e0bd04e2939f94233d63d652de.jpg)

最小值可能在上图中的两种位置出现，如果仍然使用数组首部元素作为 target 去比较，则需要考虑图中右侧情况。**使用逆向思维分析，如果使用数组尾部元素分析，则无需图中右侧的特殊情况。**不过考虑在内的话也算是一种优化。

### C++

```
class Solution {
public:
    /**
     * @param num: a rotated sorted array
     * @return: the minimum number in the array
     */
    int findMin(vector<int> &num) {
        if (num.empty()) {
            return -1;
        }

        vector<int>::size_type start = 0;
        vector<int>::size_type end = num.size() - 1;
        vector<int>::size_type mid;
        while (start + 1 < end) {
            mid = start + (end - start) / 2;
            if (num[mid] < num[end]) {
                end = mid;
            } else {
                start = mid;
            }
        }

        if (num[start] < num[end]) {
            return num[start];
        } else {
            return num[end];
        }
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param num: a rotated sorted array
     * @return: the minimum number in the array
     */
    public int findMin(int[] num) {
        if (num == null || num.length == 0) return Integer.MIN_VALUE;

        int lb = 0, ub = num.length - 1;
        // case1: num[0] < num[num.length - 1]
        // if (num[lb] < num[ub]) return num[lb];

        // case2: num[0] > num[num.length - 1] or num[0] < num[num.length - 1]
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (num[mid] < num[ub]) {
                ub = mid;
            } else {
                lb = mid;
            }
        }

        return Math.min(num[lb], num[ub]);
    }
} 
```

### 源码分析

仅需注意使用`num[end]`(使用 num[lb]不是那么直观)作为判断依据即可，由于题中已给无重复数组的条件，故无需处理`num[mid] == num[end]`特殊条件。

### 复杂度分析

由于无重复元素，平均情况下复杂度为 $$O(\log n)$$.