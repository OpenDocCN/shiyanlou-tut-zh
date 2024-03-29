# 第 6 节 Binary Search - 二分搜索（一）

本章主要总结二分搜索相关的题。

*   能使用二分搜索的前提是数组已排序。
*   二分查找的使用场景：（1）可转换为 find the first/last position of...（2）时间复杂度至少为 O(lgn)。
*   递归和迭代的使用场景：能用迭代就用迭代，特别复杂时采用递归。

## Binary Search - 二分查找

## Question

*   lintcode: [lintcode - (14) Binary Search](http://www.lintcode.com/en/problem/binary-search/)

### Problem Statement

For a given sorted array (ascending order) and a `target` number, find the first index of this number in `O(log n)` time complexity.

If the target number does not exist in the array, return `-1`.

#### Example

If the array is `[1, 2, 3, 3, 4, 5, 10]`, for given target `3`, return `2`.

#### Challenge

If the count of numbers is bigger than $$2^{32}$$, can your code work properly?

## 题解

对于已排序升序(升序)数组，使用二分查找可满足复杂度要求，注意数组中可能有重复值，所以需要使用类似`lower_bound`中提到的方法。

### Java

```
class Solution {
    /**
     * @param nums: The integer array.
     * @param target: Target to find.
     * @return: The first position of target. Position starts from 0.
     */
    public int binarySearch(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return -1;
        }

        int start = -1, end = nums.length;
        int mid;
        while (start + 1 < end) {
            // avoid overflow when (end + start)
            mid = start + (end - start) / 2;
            if (nums[mid] < target) {
                start = mid;
            } else {
                end = mid;
            }
        }

        if (end == nums.length || nums[end] != target) {
            return -1;
        } else {
            return end;
        }
    }
} 
```

### 源码分析

1.  首先对输入做异常处理，数组为空或者长度为 0。
2.  初始化 `start, end, mid`三个变量，这里`start`初始化为`-1`主要是考虑到`end`为`1`。注意 mid 的求值方法，可以防止两个整型值相加时溢出。
3.  **使用迭代而不是递归**进行二分查找，因为工程中递归写法存在潜在溢出的可能。
4.  while 终止条件应为`start + 1 < end`而不是`start <= end`，`start == end`时可能出现死循环。**即循环终止条件是相邻或相交元素时退出。**由于这里初始化时`start < end`，所以一定是`start + 1 == end`时退出循环。
5.  迭代终止时有两种情况，一种是在原数组中找到了，这种情况下一定是`end`, 因为`start`的更新只在`nums[mid] < target`.
6.  最后判断`end`和`target`的关系，先排除`end`为数组长度这种会引起越界的情况，然后再判断和目标值是否相等。

### 复杂度分析

时间复杂度 $$O(\log n)$$, 空间复杂度 $$(1)$$. 对于题中的 follow up, Java 中数组不允许使用 long 型，如果使用 long 型，那么数组大小可大 17GB 之巨！！几乎没法用。

## Reference

*   《挑战程序设计竞赛》3.1 节

## Search Insert Position

## Question

*   lintcode: [(60) Search Insert Position](http://www.lintcode.com/en/problem/search-insert-position/)

### Problem Statement

Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You may assume **NO** duplicates in the array.

#### Example

`[1,3,5,6]`, 5 → 2

`[1,3,5,6]`, 2 → 1

`[1,3,5,6]`, 7 → 4

`[1,3,5,6]`, 0 → 0

#### Challenge

O(log(n)) time

## 题解

### Python

问题可以转化为， 寻找`first position that value is >= target`。如果没找到， 那么就插入在 list 的尾部。

```
class Solution:
    """
    @param A : a list of integers
    @param target : an integer to be inserted
    @return : an integer
    """
    def searchInsert(self, A, target):
        if not A:
            return 0
        st, ed = 0, len(A) - 1
        while st + 1 < ed:
            mid = (st + ed) / 2
            if A[mid] == target:
                ed = mid
            elif A[mid] < target:
                st = mid
            else:
                ed = mid
        if A[st] >= target:
            return st
        elif A[ed] >= target:
            return ed
        else:
            return len(A) 
```

### C++

```
class Solution {
    /** 
     * param A : an integer sorted array
     * param target :  an integer to be inserted
     * return : an integer
     */
public:
    int searchInsert(vector<int> &A, int target) {
        // write your code here
        if (A.empty()) return 0;

        int n = A.size();
        int lb = -1, ub = n;
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (A[mid] < target) {
                lb = mid;
            } else {
                ub = mid;
            }
        }
        return ub;
    }
}; 
```

### Java

仍然是 [Binary Search](http://algorithm.yuanbin.me/zh-hans/basics_algorithm/binary_search.html) 中`lower_bound`的变形，两大关键点：`start` 和`end` 的初始化；最终插入位置和`start` 以及`end` 之间的关系，由于`start`对应的索引一定是小于目标值的，那么`start + 1` 就是要求的值了，再检查下两端的边界，DONE

```
public class Solution {
    /**
     * param A : an integer sorted array
     * param target :  an integer to be inserted
     * return : an integer
     */
    public int searchInsert(int[] A, int target) {
        if (A == null || A.length == 0) {
            return -1;
        }

        int start = -1, end = A.length;
        while (start + 1 < end) {
            int mid = start + (end - start) / 2;
            if (A[mid] == target) {
                return mid; // no duplicates
            } else if (A[mid] < target) {
                start = mid;
            } else {
                end = mid;
            }
        }

    return start + 1;
    }
} 
```

### 源码分析

分析三种典型情况：

1.  目标值在数组范围之内，最后返回值一定是`start + 1`
2.  目标值比数组最小值还小，此时`start` 一直为`-1`, 故最后返回`start + 1` 也没错，也可以将`-1` 理解为数组前一个更小的值
3.  目标值大于等于数组最后一个值，由于循环退出条件为`start + 1 == end`, 那么循环退出时一定有`start = A.length - 1`, 应该返回`start + 1`

综上所述，返回`start + 1`是非常优雅的实现。其实以上三种情况都可以统一为一种方式来理解，即索引`-1` 对应于在数组前方插入一个非常小的数，索引`end` 即对应数组后方插入一个非常大的数，那么要插入的数就一定在`start` 和`end` 之间了。

有时复杂的边界条件处理可以通过『补项』这种优雅的方式巧妙处理。

### 复杂度分析

时间复杂度 $$O(\log n)$$, 空间复杂度 $$O(1)$$.

## Search for a Range

## Question

*   leetcode: [Search for a Range | LeetCode OJ](https://leetcode.com/problems/search-for-a-range/)
*   lintcode: [(61) Search for a Range](http://www.lintcode.com/en/problem/search-for-a-range/)

### Problem Statement

Given a sorted array of *n* integers, find the starting and ending position of a given target value.

If the target is not found in the array, return `[-1, -1]`.

#### Example

Given `[5, 7, 7, 8, 8, 10]` and target value `8`, return `[3, 4]`.

#### Challenge

O(log *n*) time.

## 题解

### Python

first/last position 结合。

```
class Solution:
    """
    @param A : a list of integers
    @param target : an integer to be searched
    @return : a list of length 2, [index1, index2]
    """
    def searchRange(self, A, target):
        ret = [-1, -1]
        if not A:
            return ret

        # find the first position of target
        st, ed = 0, len(A) - 1
        while st + 1 < ed:
            mid = (st + ed) / 2
            if A[mid] == target:
                ed = mid
            elif A[mid] < target:
                st = mid
            else:
                ed = mid
        if A[st] == target:
            ret[0] = st
        elif A[ed] == target:
            ret[0] = ed
        # find the last position of target
        st, ed = 0, len(A) - 1
        while st + 1 < ed:
            mid = (st + ed) / 2
            if A[mid] == target:
                st = mid
            elif A[mid] < target:
                st = mid
            else:
                ed = mid
        if A[ed] == target:
            ret[1] = ed
        elif A[st] == target:
            ret[1] = st

        return ret 
```

### C++

```
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> result = {-1, -1};
        if (nums.empty()) return result;

        int lb = -1, ub = nums.size();
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (nums[mid] < target) lb = mid;
            else ub = mid;
        }

        if ((ub < nums.size()) && (nums[ub] == target)) result[0] = ub;
        else return result;

        ub = nums.size();
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (nums[mid] > target) ub = mid;
            else lb = mid;
        }
        result[1] = ub - 1;
        return result;
    }
}; 
```

### Java

lower/upper bound 的结合，做两次搜索即可。

```
public class Solution {
    /**
     *@param A : an integer sorted array
     *@param target :  an integer to be inserted
     *return : a list of length 2, [index1, index2]
     */
    public int[] searchRange(int[] A, int target) {
        int[] result = new int[]{-1, -1};
        if (A == null || A.length == 0) return result;

        int lb = -1, ub = A.length;
        // lower bound
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (A[mid] < target) {
                lb = mid;
            } else {
                ub = mid;
            }
        }
        // whether A[lb + 1] == target, check lb + 1 first
        if ((lb + 1 < A.length) && (A[lb + 1] == target)) {
            result[0] = lb + 1;
        } else {
            result[0] = -1;
            result[1] = -1;
            // target is not in the array
            return result;
        }

        // upper bound, since ub >= lb, we do not reset lb
        ub = A.length;
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (A[mid] > target) {
                ub = mid;
            } else {
                lb = mid;
            }
        }
        // target must exist in the array
        result[1] = ub - 1;

        return result;
    }
} 
```

### 源码分析

1.  首先对输入做异常处理，数组为空或者长度为 0
2.  分 lower/upper bound 两次搜索，注意如果在 lower bound 阶段未找到目标值时，upper bound 也一定找不到。
3.  取`A[lb + 1]` 时一定要注意判断索引是否越界！

### 复杂度分析

两次二分搜索，时间复杂度仍为 $$O(\log n)$$.

## First Bad Version

## Question

*   lintcode: [(74) First Bad Version](http://www.lintcode.com/en/problem/first-bad-version/)

### Problem Statement

The code base version is an integer start from 1 to n. One day, someone committed a bad version in the code case, so it caused this version and the following versions are all failed in the unit tests. Find the first bad version.

You can call `isBadVersion` to help you determine which version is the first bad one. The details interface can be found in the code's annotation part.

#### Example

Given n = `5`:

```
isBadVersion(3) -> false
isBadVersion(5) -> true
isBadVersion(4) -> true 
```

Here we are 100% sure that the 4th version is the first bad version.

#### Note

Please read the annotation in code area to get the correct way to call isBadVersion in different language. For example, Java is `VersionControl.isBadVersion(v)`

#### Challenge

You should call *isBadVersion* as few as possible.

## 题解

基础算法中 [Binary Search](http://algorithm.yuanbin.me/zh-hans/basics_algorithm/binary_search.html) 的 lower bound. 找出满足条件的下界即可。

### Python

```
#class VersionControl:
#    @classmethod
#    def isBadVersion(cls, id)
#        # Run unit tests to check whether verison `id` is a bad version
#        # return true if unit tests passed else false.
# You can use VersionControl.isBadVersion(10) to check whether version 10 is a
# bad version.
class Solution:
    """
    @param n: An integers.
    @return: An integer which is the first bad version.
    """
    def findFirstBadVersion(self, n):
        lb, ub = 0, n + 1
        while lb + 1 < ub:
            mid = lb + (ub - lb) / 2
            if VersionControl.isBadVersion(mid):
                ub = mid
            else:
                lb = mid

        return lb + 1 
```

### C++

```
/**
 * class VersionControl {
 *     public:
 *     static bool isBadVersion(int k);
 * }
 * you can use VersionControl::isBadVersion(k) to judge whether
 * the kth code version is bad or not.
*/
class Solution {
public:
    /**
     * @param n: An integers.
     * @return: An integer which is the first bad version.
     */
    int findFirstBadVersion(int n) {
        int lb = 0, ub = n + 1;
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (VersionControl::isBadVersion(mid)) {
                ub = mid;
            } else {
                lb = mid;
            }
        }

        return lb + 1;
    }
}; 
```

### Java

```
/**
 * public class VersionControl {
 *     public static boolean isBadVersion(int k);
 * }
 * you can use VersionControl.isBadVersion(k) to judge whether
 * the kth code version is bad or not.
*/
class Solution {
    /**
     * @param n: An integers.
     * @return: An integer which is the first bad version.
     */
    public int findFirstBadVersion(int n) {
        int lb = 0, ub = n + 1;
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (VersionControl.isBadVersion(mid)) {
                ub = mid;
            } else {
                lb = mid;
            }
        }

        return lb + 1;
    }
} 
```

### 源码分析

lower bound 的实现，这里稍微注意下 lb 初始化为 0，因为 n 从 1 开始。ub 和 lb 分别都在什么条件下更新就好了。另外这里并未考虑 `n <= 0` 的情况。

### 复杂度分析

二分搜索，$$O(\log n)$$.

## Search a 2D Matrix

## Question

*   leetcode: [Search a 2D Matrix | LeetCode OJ](https://leetcode.com/problems/search-a-2d-matrix/)
*   lintcode: [(28) Search a 2D Matrix](http://www.lintcode.com/en/problem/search-a-2d-matrix/)

### Problem Statement

Write an efficient algorithm that searches for a value in an *m* x *n* matrix.

This matrix has the following properties:

*   Integers in each row are sorted from left to right.
*   The first integer of each row is greater than the last integer of the previous row.

#### Example

Consider the following matrix:

```
[
    [1, 3, 5, 7],
    [10, 11, 16, 20],
    [23, 30, 34, 50]
] 
```

Given `target = 3`, return `true`.

#### Challenge

O(log(n) + log(m)) time

## 题解 - 一次二分搜索 V.S. 两次二分搜索

*   **一次二分搜索** - 由于矩阵按升序排列，因此可将二维矩阵转换为一维问题。对原始的二分搜索进行适当改变即可(求行和列)。时间复杂度为 $$O(log(mn))=O(log(m)+log(n))$$
*   **两次二分搜索** - 先按行再按列进行搜索，即两次二分搜索。时间复杂度相同。

## 一次二分搜索

### Python

```
class Solution:
    def search_matrix(self, matrix, target):
        # Find the first position of target
        if not matrix or not matrix[0]:
            return False
        m, n = len(matrix), len(matrix[0])
        st, ed = 0, m * n - 1

        while st + 1 < ed:
            mid = (st + ed) / 2
            if matrix[mid / n][mid % n] == target:
                return True
            elif matrix[mid / n][mid % n] < target:
                st = mid
            else:
                ed = mid
        return matrix[st / n][st % n] == target or \
                matrix[ed / n][ed % n] == target 
```

### C++

```
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        if (matrix.empty() || matrix[0].empty()) return false;

        int ROW = matrix.size(), COL = matrix[0].size();
        int lb = -1, ub = ROW * COL;
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (matrix[mid / COL][mid % COL] < target) {
                lb = mid;
            } else {
                if (matrix[mid / COL][mid % COL] == target) return true;
                ub = mid;
            }
        }
        return false;
    }
}; 
```

### Java

lower bound 二分模板。

```
public class Solution {
    /**
     * @param matrix, a list of lists of integers
     * @param target, an integer
     * @return a boolean, indicate whether matrix contains target
     */
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0] == null) {
            return false;
        }

        int ROW = matrix.length, COL = matrix[0].length;
        int lb = -1, ub = ROW * COL;
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (matrix[mid / COL][mid % COL] < target) {
                lb = mid;
            } else {
                if (matrix[mid / COL][mid % COL] == target) {
                    return true;
                }
                ub = mid;
            }
        }

        return false;
    }
} 
```

### 源码分析

仍然可以使用经典的二分搜索模板(lower bound)，注意下标的赋值即可。

1.  首先对输入做异常处理，不仅要考虑到 matrix 为 null，还要考虑到 matrix[0]的长度也为 0。
2.  由于 lb 的变化处一定小于 target, 故在 else 中判断。

### 复杂度分析

二分搜索，$$O(\log mn)$$.

## 两次二分法

### Python

```
class Solution:
    def search_matrix(self, matrix, target):
        if not matrix or not matrix[0]:
            return False

        # first pos >= target
        st, ed = 0, len(matrix) - 1
        while st + 1 < ed:
            mid = (st + ed) / 2
            if matrix[mid][-1] == target:
                st = mid
            elif matrix[mid][-1] < target:
                st = mid
            else:
                ed = mid
        if matrix[st][-1] >= target:
            row = matrix[st]
        elif matrix[ed][-1] >= target:
            row = matrix[ed]
        else:
            return False

        # binary search in row
        st, ed = 0, len(row) - 1
        while st + 1 < ed:
            mid = (st + ed) / 2
            if row[mid] == target:
                return True
            elif row[mid] < target:
                st = mid
            else:
                ed = mid
        return row[st] == target or row[ed] == target 
```

### 源码分析

1.  先找到`first position`的行， 这一行的最后一个元素大于等于 target
2.  再在这一行中找 target

#### 复杂度分析

二分搜索， $$O(\log m + \log n)$$

## Search a 2D Matrix

## Question

*   leetcode: [Search a 2D Matrix | LeetCode OJ](https://leetcode.com/problems/search-a-2d-matrix/)
*   lintcode: [(28) Search a 2D Matrix](http://www.lintcode.com/en/problem/search-a-2d-matrix/)

### Problem Statement

Write an efficient algorithm that searches for a value in an *m* x *n* matrix.

This matrix has the following properties:

*   Integers in each row are sorted from left to right.
*   The first integer of each row is greater than the last integer of the previous row.

#### Example

Consider the following matrix:

```
[
    [1, 3, 5, 7],
    [10, 11, 16, 20],
    [23, 30, 34, 50]
] 
```

Given `target = 3`, return `true`.

#### Challenge

O(log(n) + log(m)) time

## 题解 - 一次二分搜索 V.S. 两次二分搜索

*   **一次二分搜索** - 由于矩阵按升序排列，因此可将二维矩阵转换为一维问题。对原始的二分搜索进行适当改变即可(求行和列)。时间复杂度为 $$O(log(mn))=O(log(m)+log(n))$$
*   **两次二分搜索** - 先按行再按列进行搜索，即两次二分搜索。时间复杂度相同。

## 一次二分搜索

### Python

```
class Solution:
    def search_matrix(self, matrix, target):
        # Find the first position of target
        if not matrix or not matrix[0]:
            return False
        m, n = len(matrix), len(matrix[0])
        st, ed = 0, m * n - 1

        while st + 1 < ed:
            mid = (st + ed) / 2
            if matrix[mid / n][mid % n] == target:
                return True
            elif matrix[mid / n][mid % n] < target:
                st = mid
            else:
                ed = mid
        return matrix[st / n][st % n] == target or \
                matrix[ed / n][ed % n] == target 
```

### C++

```
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        if (matrix.empty() || matrix[0].empty()) return false;

        int ROW = matrix.size(), COL = matrix[0].size();
        int lb = -1, ub = ROW * COL;
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (matrix[mid / COL][mid % COL] < target) {
                lb = mid;
            } else {
                if (matrix[mid / COL][mid % COL] == target) return true;
                ub = mid;
            }
        }
        return false;
    }
}; 
```

### Java

lower bound 二分模板。

```
public class Solution {
    /**
     * @param matrix, a list of lists of integers
     * @param target, an integer
     * @return a boolean, indicate whether matrix contains target
     */
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0] == null) {
            return false;
        }

        int ROW = matrix.length, COL = matrix[0].length;
        int lb = -1, ub = ROW * COL;
        while (lb + 1 < ub) {
            int mid = lb + (ub - lb) / 2;
            if (matrix[mid / COL][mid % COL] < target) {
                lb = mid;
            } else {
                if (matrix[mid / COL][mid % COL] == target) {
                    return true;
                }
                ub = mid;
            }
        }

        return false;
    }
} 
```

### 源码分析

仍然可以使用经典的二分搜索模板(lower bound)，注意下标的赋值即可。

1.  首先对输入做异常处理，不仅要考虑到 matrix 为 null，还要考虑到 matrix[0]的长度也为 0。
2.  由于 lb 的变化处一定小于 target, 故在 else 中判断。

### 复杂度分析

二分搜索，$$O(\log mn)$$.

## 两次二分法

### Python

```
class Solution:
    def search_matrix(self, matrix, target):
        if not matrix or not matrix[0]:
            return False

        # first pos >= target
        st, ed = 0, len(matrix) - 1
        while st + 1 < ed:
            mid = (st + ed) / 2
            if matrix[mid][-1] == target:
                st = mid
            elif matrix[mid][-1] < target:
                st = mid
            else:
                ed = mid
        if matrix[st][-1] >= target:
            row = matrix[st]
        elif matrix[ed][-1] >= target:
            row = matrix[ed]
        else:
            return False

        # binary search in row
        st, ed = 0, len(row) - 1
        while st + 1 < ed:
            mid = (st + ed) / 2
            if row[mid] == target:
                return True
            elif row[mid] < target:
                st = mid
            else:
                ed = mid
        return row[st] == target or row[ed] == target 
```

### 源码分析

1.  先找到`first position`的行， 这一行的最后一个元素大于等于 target
2.  再在这一行中找 target

#### 复杂度分析

二分搜索， $$O(\log m + \log n)$$