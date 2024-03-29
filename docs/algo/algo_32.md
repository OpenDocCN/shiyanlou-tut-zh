# 第 32 节 Problem Misc（一）

本章主要总结暂时不方便归到其他章节的题目。

## Nuts and Bolts Problem

## Question

*   lintcode: [(399) Nuts & Bolts Problem](http://www.lintcode.com/en/problem/nuts-bolts-problem/)

```
Given a set of n nuts of different sizes and n bolts of different sizes.
There is a one-one mapping between nuts and bolts.
Comparison of a nut to another nut or a bolt to another bolt is not allowed.
It means nut can only be compared with bolt and bolt can only
be compared with nut to see which one is bigger/smaller.

We will give you a compare function to compare nut with bolt.

Example
Given nuts = ['ab','bc','dd','gg'], bolts = ['AB','GG', 'DD', 'BC'].

Your code should find the matching bolts and nuts.

one of the possible return:

nuts = ['ab','bc','dd','gg'], bolts = ['AB','BC','DD','GG'].

we will tell you the match compare function.
If we give you another compare function.

the possible return is the following:

nuts = ['ab','bc','dd','gg'], bolts = ['BC','AA','DD','GG'].

So you must use the compare function that we give to do the sorting.

The order of the nuts or bolts does not matter.
You just need to find the matching bolt for each nut. 
```

## 题解

首先结合例子读懂题意，本题为 nuts 和 bolts 的配对问题，但是需要根据题目所提供的比较函数，且 nuts 与 nuts 之间的元素无法直接比较，compare 仅能在 nuts 与 bolts 之间进行。首先我们考虑若没有比较函数的限制，那么我们可以分别对 nuts 和 bolts 进行排序，由于是一一配对，故排完序后即完成配对。那么在只能通过比较对方元素得知相对大小时怎么完成排序呢？

我们容易通过以一组元素作为参考进行遍历获得两两相等的元素，这样一来在最坏情况下时间复杂度为 $$O(n²)$$, 相当于冒泡排序。根据排序算法理论可知基于比较的排序算法最好的时间复杂度为 $$O(n \log n)$$, 也就是说这道题应该是可以进一步优化。回忆一些基于比较的排序算法，能达到 $$O(n \log n)$$ 时间复杂度的有堆排、归并排序和快速排序，由于这里只能通过比较得到相对大小的关系，故可以联想到快速排序。

快速排序的核心即为定基准，划分区间。由于这里只能以对方的元素作为基准，故一趟划分区间后仅能得到某一方基准元素排序后的位置，那通过引入 $$O(n)$$ 的额外空间来对已处理的基准元素进行标记如何呢？这种方法实现起来较为困难，因为只能对一方的元素划分区间，而对方的元素无法划分区间进而导致递归无法正常进行。

山穷水尽疑无路，柳暗花明又一村。由于只能通过对方进行比较，故需要相互配合进行 partition 操作(这个点确实难以想到)。核心在于：**首先使用 nuts 中的某一个元素作为基准对 bolts 进行 partition 操作，随后将 bolts 中得到的基准元素作为基准对 nuts 进行 partition 操作。**

### Python

```
# class Comparator:
#     def cmp(self, a, b)
# You can use Compare.cmp(a, b) to compare nuts "a" and bolts "b",
# if "a" is bigger than "b", it will return 1, else if they are equal,
# it will return 0, else if "a" is smaller than "b", it will return -1.
# When "a" is not a nut or "b" is not a bolt, it will return 2, which is not valid.
class Solution:
    # @param nuts: a list of integers
    # @param bolts: a list of integers
    # @param compare: a instance of Comparator
    # @return: nothing
    def sortNutsAndBolts(self, nuts, bolts, compare):
        if nuts is None or bolts is None:
            return
        if len(nuts) != len(bolts):
            return
        self.qsort(nuts, bolts, 0, len(nuts) - 1, compare)

    def qsort(self, nuts, bolts, l, u, compare):
        if l >= u:
            return
        # find the partition index for nuts with bolts[l]
        part_inx = self.partition(nuts, bolts[l], l, u, compare)
        # partition bolts with nuts[part_inx]
        self.partition(bolts, nuts[part_inx], l, u, compare)
        # qsort recursively
        self.qsort(nuts, bolts, l, part_inx - 1, compare)
        self.qsort(nuts, bolts, part_inx + 1, u, compare)

    def partition(self, alist, pivot, l, u, compare):
        m = l
        i = l + 1
        while i <= u:
            if compare.cmp(alist[i], pivot) == -1 or \
               compare.cmp(pivot, alist[i]) == 1:
                m += 1
                alist[i], alist[m] = alist[m], alist[i]
                i += 1
            elif compare.cmp(alist[i], pivot) == 0 or \
                 compare.cmp(pivot, alist[i]) == 0:
                # swap nuts[l]/bolts[l] with pivot
                alist[i], alist[l] = alist[l], alist[i]
            else:
                i += 1
        # move pivot to proper index
        alist[l], alist[m] = alist[m], alist[l]

        return m 
```

### C++

```
/**
 * class Comparator {
 *     public:
 *      int cmp(string a, string b);
 * };
 * You can use compare.cmp(a, b) to compare nuts "a" and bolts "b",
 * if "a" is bigger than "b", it will return 1, else if they are equal,
 * it will return 0, else if "a" is smaller than "b", it will return -1.
 * When "a" is not a nut or "b" is not a bolt, it will return 2, which is not valid.
*/
class Solution {
public:
    /**
     * @param nuts: a vector of integers
     * @param bolts: a vector of integers
     * @param compare: a instance of Comparator
     * @return: nothing
     */
    void sortNutsAndBolts(vector<string> &nuts, vector<string> &bolts, Comparator compare) {
        if (nuts.empty() || bolts.empty()) return;
        if (nuts.size() != bolts.size()) return;

        qsort(nuts, bolts, compare, 0, nuts.size() - 1);
    }

private:
    void qsort(vector<string>& nuts, vector<string>& bolts, Comparator compare,
               int l, int u) {

        if (l >= u) return;
        // find the partition index for nuts with bolts[l]
        int part_inx = partition(nuts, bolts[l], compare, l, u);
        // partition bolts with nuts[part_inx]
        partition(bolts, nuts[part_inx], compare, l, u);
        // qsort recursively
        qsort(nuts, bolts, compare, l, part_inx - 1);
        qsort(nuts, bolts, compare, part_inx + 1, u);
    }

    int partition(vector<string>& str, string& pivot, Comparator compare,
                  int l, int u) {

        int m = l;
        for (int i = l + 1; i <= u; ++i) {
            if (compare.cmp(str[i], pivot) == -1 || 
                compare.cmp(pivot, str[i]) == 1) {

                ++m;
                std::swap(str[m], str[i]);
            } else if (compare.cmp(str[i], pivot) == 0 || 
                       compare.cmp(pivot, str[i]) == 0) {
                // swap nuts[l]/bolts[l] with pivot
                std::swap(str[i], str[l]);
                --i;
            }
        }
        // move pivot to proper index
        std::swap(str[m], str[l]);

        return m;
    }
}; 
```

### Java

```
/**
 * public class NBCompare {
 *     public int cmp(String a, String b);
 * }
 * You can use compare.cmp(a, b) to compare nuts "a" and bolts "b",
 * if "a" is bigger than "b", it will return 1, else if they are equal,
 * it will return 0, else if "a" is smaller than "b", it will return -1.
 * When "a" is not a nut or "b" is not a bolt, it will return 2, which is not valid.
*/
public class Solution {
    /**
     * @param nuts: an array of integers
     * @param bolts: an array of integers
     * @param compare: a instance of Comparator
     * @return: nothing
     */
    public void sortNutsAndBolts(String[] nuts, String[] bolts, NBComparator compare) {
        if (nuts == null || bolts == null) return;
        if (nuts.length != bolts.length) return;

        qsort(nuts, bolts, compare, 0, nuts.length - 1);
    }

    private void qsort(String[] nuts, String[] bolts, NBComparator compare, 
                       int l, int u) {
        if (l >= u) return;
        // find the partition index for nuts with bolts[l]
        int part_inx = partition(nuts, bolts[l], compare, l, u);
        // partition bolts with nuts[part_inx]
        partition(bolts, nuts[part_inx], compare, l, u);
        // qsort recursively
        qsort(nuts, bolts, compare, l, part_inx - 1);
        qsort(nuts, bolts, compare, part_inx + 1, u);
    }

    private int partition(String[] str, String pivot, NBComparator compare, 
                          int l, int u) {
        //
        int m = l;
        for (int i = l + 1; i <= u; i++) {
            if (compare.cmp(str[i], pivot) == -1 || 
                compare.cmp(pivot, str[i]) == 1) {
                //
                m++;
                swap(str, i, m);
            } else if (compare.cmp(str[i], pivot) == 0 || 
                       compare.cmp(pivot, str[i]) == 0) {
                // swap nuts[l]/bolts[l] with pivot
                swap(str, i, l);
                i--;
            }
        }
        // move pivot to proper index
        swap(str, m, l);

        return m;
    }

    private void swap(String[] str, int l, int r) {
        String temp = str[l];
        str[l] = str[r];
        str[r] = temp;
    }
} 
```

### 源码分析

难以理解的可能在`partition`部分，不仅需要使用`compare.cmp(alist[i], pivot)`, 同时也需要使用`compare.cmp(pivot, alist[i])`, 否则答案有误。第二个在于`alist[i] == pivot`时，需要首先将其和`alist[l]`交换，因为`i`是从`l+1`开始处理的，将`alist[l]`换过来后可继续和 pivot 进行比较。在 while 循环退出后在将当前遍历到的小于 pivot 的元素 alist[m] 和 alist[l] 交换，此时基准元素正确归位。对这一块不是很清楚的举个例子就明白了。

### 复杂度分析

快排的思路，时间复杂度为 $$O(2n \log n)$$, 使用了一些临时变量，空间复杂度 $$O(1)$$.

## Reference

*   [LintCode/Nuts & Bolts Problem.py at master · algorhythms/LintCode](https://github.com/algorhythms/LintCode/blob/master/Nuts%20%26%20Bolts%20Problem.py)

## String to Integer

## Question

*   leetcode: [String to Integer (atoi) | LeetCode OJ](https://leetcode.com/problems/string-to-integer-atoi/)
*   lintcode: [(54) String to Integer(atoi)](http://www.lintcode.com/en/problem/string-to-integeratoi/)

```
Implement function atoi to convert a string to an integer.

If no valid conversion could be performed, a zero value is returned.

If the correct value is out of the range of representable values,
INT_MAX (2147483647) or INT_MIN (-2147483648) is returned.

Example
"10" => 10

"-1" => -1

"123123123123123" => 2147483647

"1.0" => 1 
```

## 题解

经典的字符串转整数题，边界条件比较多，比如是否需要考虑小数点，空白及非法字符的处理，正负号的处理，科学计数法等。最先处理的是空白字符，然后是正负号，接下来只要出现非法字符(包含正负号，小数点等，无需对这两类单独处理)即退出，否则按照正负号的整数进位加法处理。

### Java

```
public class Solution {
    /**
     * @param str: A string
     * @return An integer
     */
    public int atoi(String str) {
        if (str == null || str.length() == 0) return 0;

        // trim left and right spaces
        String strTrim = str.trim();
        int len = strTrim.length();
        // sign symbol for positive and negative
        int sign = 1;
        // index for iteration
        int i = 0;
        if (strTrim.charAt(i) == '+') {
            i++;
        } else if (strTrim.charAt(i) == '-') {
            sign = -1;
            i++;
        }

        // store the result as long to avoid overflow
        long result = 0;
        while (i < len) {
            if (strTrim.charAt(i) < '0' || strTrim.charAt(i) > '9') {
                break;
            }
            result = 10 * result + sign * (strTrim.charAt(i) - '0');
            // overflow
            if (result > Integer.MAX_VALUE) {
                return Integer.MAX_VALUE;
            } else if (result < Integer.MIN_VALUE) {
                return Integer.MIN_VALUE;
            }
            i++;
        }

        return (int)result;
    }
} 
```

### 源码分析

符号位使用整型表示，便于后期相乘相加。在 while 循环中需要注意判断是否已经溢出，如果放在 while 循环外面则有可能超过 long 型范围。

### 复杂度分析

略

## Reference

*   [String to Integer (atoi) 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/string-to-integer-atoi/)

## Insert Interval

## Question

*   leetcode: [Insert Interval | LeetCode OJ](https://leetcode.com/problems/insert-interval/)
*   lintcode: [(30) Insert Interval](http://www.lintcode.com/en/problem/insert-interval/)

```
Given a non-overlapping interval list which is sorted by start point.
Insert a new interval into it,
make sure the list is still in order and non-overlapping
(merge intervals if necessary).

Example
Insert [2, 5] into [[1,2], [5,9]], we get [[1,9]].

Insert [3, 4] into [[1,2], [5,9]], we get [[1,2], [3,4], [5,9]]. 
```

## 题解

这道题看似简单，但其实实现起来不那么容易，因为若按照常规思路，需要分很多种情况考虑，如半边相等的情况。以返回新数组为例，首先，遍历原数组肯定是必须的，以`[N]`代表`newInterval`, `[I]`代表当前遍历到的`interval`, 那么有以下几种情况：

1.  `[N], [I]` <==> `newInterval.end < interval.start`, 由于 intervals 中的间隔数组已经为升序排列，那么遍历到的下一个间隔的左边元素必然也大于新间隔的右边元素。
2.  `[NI]` <==> `newInterval.end == interval.start`，这种情况下需要进行合并操作。
3.  `[IN]` <==> `newInterval.start == interval.end`, 这种情况下也需要进行合并。
4.  `[I], [N]` <==> `newInterval.start > interval.end`, 这意味着`newInterval`有可能在此处插入，也有可能在其后面的间隔插入。故遍历时需要在这种情况下做一些标记以确定最终插入位置。

由于间隔都是互不重叠的，故其关系只可能为以上四种中的某几个。1 和 4 两种情况很好处理，关键在于 2 和 3 的处理。由于 2 和 3 这种情况都将生成新的间隔，且这种情况一旦发生，**原来的`newInterval`即被新的合并间隔取代，这是一个非常关键的突破口。**

### Java

```
/**
 * Definition of Interval:
 * public classs Interval {
 *     int start, end;
 *     Interval(int start, int end) {
 *         this.start = start;
 *         this.end = end;
 *     }
 */

class Solution {
    /**
     * Insert newInterval into intervals.
     * @param intervals: Sorted interval list.
     * @param newInterval: A new interval.
     * @return: A new sorted interval list.
     */
    public ArrayList<Interval> insert(ArrayList<Interval> intervals, Interval newInterval) {
        ArrayList<Interval> result = new ArrayList<Interval>();
        if (intervals == null || intervals.isEmpty()) {
            if (newInterval != null) {
                result.add(newInterval);
            }
            return result;
        }

        int insertPos = 0;
        for (Interval interval : intervals) {
            if (newInterval.end < interval.start) {
                // case 1: [new], [old]
                result.add(interval);
            } else if (interval.end < newInterval.start) {
                // case 2: [old], [new]
                result.add(interval);
                insertPos++;
            } else {
                // case 3, 4: [old, new] or [new, old]
                newInterval.start = Math.min(newInterval.start, interval.start);
                newInterval.end = Math.max(newInterval.end, interval.end);
            }
        }

        result.add(insertPos, newInterval);

        return result;
    }
} 
```

### 源码分析

源码的精华在 case 3 和 case 4 的处理，case 2 用于确定最终新间隔的插入位置。

之所以不在 case 1 立即返回，有两点考虑：一是代码的复杂性(需要用到 addAll 添加数组部分元素)；二是 case2, case3, case 4 有可能正好遍历到数组的最后一个元素，如果在 case 1 就返回的话还需要单独做一判断。

### 复杂度分析

遍历一次，时间复杂度 $$O(n)$$. 不考虑作为结果返回占用的空间 result, 空间复杂度 $$O(1)$$.

## Reference

*   [Insert Interval 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/insert-interval/)

## Merge Intervals

## Question

*   leetcode: [Merge Intervals | LeetCode OJ](https://leetcode.com/problems/merge-intervals/)
*   lintcode: [(156) Merge Intervals](http://www.lintcode.com/en/problem/merge-intervals/)

### Problem Statement

Given a collection of intervals, merge all overlapping intervals.

#### Example

Given intervals => merged intervals:

```
[                     [
  [1, 3],               [1, 6],
  [2, 6],      =>       [8, 10],
  [8, 10],              [15, 18]
  [15, 18]            ]
] 
```

#### Challenge

O(n log n) time and O(1) extra space.

## 题解 1 - 排序后

初次接触这道题可能会先对 interval 排序，随后考虑相邻两个 interval 的 end 和 start 是否交叉，若交叉则合并之。

### Java

```
/**
 * Definition of Interval:
 * public class Interval {
 *     int start, end;
 *     Interval(int start, int end) {
 *         this.start = start;
 *         this.end = end;
 *     }
 */

class Solution {
    /**
     * @param intervals: Sorted interval list.
     * @return: A new sorted interval list.
     */
    public List<Interval> merge(List<Interval> intervals) {
        if (intervals == null || intervals.isEmpty()) return intervals;

        List<Interval> result = new ArrayList<Interval>();
        // sort with Comparator
        Collections.sort(intervals, new IntervalComparator());
        Interval prev = intervals.get(0);
        for (Interval interval : intervals) {
            if (prev.end < interval.start) {
                result.add(prev);
                prev = interval;
            } else {
                prev.start = Math.min(prev.start, interval.start);
                prev.end = Math.max(prev.end, interval.end);
            }
        }
        result.add(prev);

        return result;
    }

    private class IntervalComparator implements Comparator<Interval> {
        public int compare(Interval a, Interval b) {
            return a.start - b.start;
        }
    }

} 
```

### 源码分析

这里因为需要比较 interval 的 start, 所以需要自己实现 Comparator 接口并覆盖 compare 方法。这里取 prev 为前一个 interval。最后不要忘记加上 prev.

### 复杂度分析

排序 $$O(n \log n)$$, 遍历 $$O(n)$$, 所以总的时间复杂度为 $$O(n \log n)$$. 空间复杂度 $$O(1)$$.

## 题解 2 - 插入排序

除了首先对 intervals 排序外，还可以使用类似插入排序的方法，插入的方法在题 [Insert Interval](http://algorithm.yuanbin.me/zh-hans/problem_misc/insert_interval.html) 中已详述。这里将 result 作为 intervals 传进去即可，新插入的 interval 为 intervals 遍历得到的结果。

### Java

```
/**
 * Definition of Interval:
 * public class Interval {
 *     int start, end;
 *     Interval(int start, int end) {
 *         this.start = start;
 *         this.end = end;
 *     }
 */

class Solution {
    /**
     * @param intervals: Sorted interval list.
     * @return: A new sorted interval list.
     */
    public List<Interval> merge(List<Interval> intervals) {
        if (intervals == null || intervals.isEmpty()) return intervals;

        List<Interval> result = new ArrayList<Interval>();
        for (Interval interval : intervals) {
            result = insert(result, interval);
        }

        return result;
    }

    private List<Interval> insert(List<Interval> intervals, Interval newInterval) {
        List<Interval> result = new ArrayList<Interval>();
        int insertPos = 0;
        for (Interval interval : intervals) {
            if (newInterval.end < interval.start) {
                result.add(interval);
            } else if (newInterval.start > interval.end) {
                result.add(interval);
                insertPos++;
            } else {
                newInterval.start = Math.min(newInterval.start, interval.start);
                newInterval.end = Math.max(newInterval.end, interval.end);
            }
        }
        result.add(insertPos, newInterval);

        return result;
    }
} 
```

### 源码分析

关键在 insert 的理解，`result = insert(result, interval);`作为迭代生成新的 result.

### 复杂度分析

每次添加新的 interval 都是线性时间复杂度，故总的时间复杂度为 $$O(1 + 2 + ... + n) = O(n²)$$. 空间复杂度为 $$O(n)$$.

## Reference

*   [Merge Intervals 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/merge-intervals/)
*   Soulmachine 的 leetcode 题解

## Minimum Subarray

## Question

*   lintcode: [(44) Minimum Subarray](http://www.lintcode.com/en/problem/minimum-subarray/)

```
Given an array of integers, find the subarray with smallest sum.

Return the sum of the subarray.

Example
For [1, -1, -2, 1], return -3

Note
The subarray should contain at least one integer. 
```

## 题解

题 [Maximum Subarray](http://algorithm.yuanbin.me/zh-hans/dynamic_programming/maximum_subarray.html) 的变形，使用区间和容易理解和实现。

### Java

```
public class Solution {
    /**
     * @param nums: a list of integers
     * @return: A integer indicate the sum of minimum subarray
     */
    public int minSubArray(ArrayList<Integer> nums) {
        if (nums == null || nums.isEmpty()) return -1;

        int sum = 0, maxSum = 0, minSub = Integer.MAX_VALUE;
        for (int num : nums) {
            maxSum = Math.max(maxSum, sum);
            sum += num;
            minSub = Math.min(minSub, sum - maxSum);
        }

        return minSub;
    }
} 
```

### 源码分析

略

### 复杂度分析

略

## Matrix Zigzag Traversal

## Question

*   lintcode: [(185) Matrix Zigzag Traversal](http://www.lintcode.com/en/problem/matrix-zigzag-traversal/)

```
Given a matrix of m x n elements (m rows, n columns),
return all elements of the matrix in ZigZag-order.

Example
Given a matrix:

[
  [1, 2,  3,  4],
  [5, 6,  7,  8],
  [9,10, 11, 12]
]
return [1, 2, 5, 9, 6, 3, 4, 7, 10, 11, 8, 12] 
```

## 题解

按之字形遍历矩阵，纯粹找下标规律。以题中所给范例为例，设`(x, y)`为矩阵坐标，按之字形遍历有如下规律：

```
(0, 0)
(0, 1), (1, 0)
(2, 0), (1, 1), (0, 2)
(0, 3), (1, 2), (2, 1)
(2, 2), (1, 3)
(2, 3) 
```

可以发现其中每一行的坐标之和为常数，坐标和为奇数时 x 递增，为偶数时 x 递减。

### Java - valid matrix index second

```
public class Solution {
    /**
     * @param matrix: a matrix of integers
     * @return: an array of integers
     */
    public int[] printZMatrix(int[][] matrix) {
        if (matrix == null || matrix.length == 0) return null;

        int m = matrix.length - 1, n = matrix[0].length - 1;
        int[] result = new int[(m + 1) * (n + 1)];
        int index = 0;
        for (int i = 0; i <= m + n; i++) {
            if (i % 2 == 0) {
                for (int x = i; x >= 0; x--) {
                    // valid matrix index
                    if ((x <= m) && (i - x <= n)) {
                        result[index] = matrix[x][i - x];
                        index++;
                    }
                }
            } else {
                for (int x = 0; x <= i; x++) {
                    if ((x <= m) && (i - x <= n)) {
                        result[index] = matrix[x][i - x];
                        index++;
                    }
                }
            }
        }

        return result;
    }
} 
```

### Java - valid matrix index first

```
public class Solution {
    /**
     * @param matrix: a matrix of integers
     * @return: an array of integers
     */
    public int[] printZMatrix(int[][] matrix) {
        if (matrix == null || matrix.length == 0) return null;

        int m = matrix.length - 1, n = matrix[0].length - 1;
        int[] result = new int[(m + 1) * (n + 1)];
        int index = 0;
        for (int i = 0; i <= m + n; i++) {
            int upperBoundx = Math.min(i, m); // x <= m
            int lowerBoundx = Math.max(0, i - n); // lower bound i - x(y) <= n
            int upperBoundy = Math.min(i, n); // y <= n
            int lowerBoundy = Math.max(0, i - m); // i - y(x) <= m
            if (i % 2 == 0) {
                // column increment
                for (int y = lowerBoundy; y <= upperBoundy; y++) {
                    result[index] = matrix[i - y][y];
                    index++;
                }
            } else {
                // row increment
                for (int x = lowerBoundx; x <= upperBoundx; x++) {
                    result[index] = matrix[x][i - x];
                    index++;
                }
            }
        }

        return result;
    }
} 
```

### 源码分析

矩阵行列和分奇偶讨论，奇数时行递增，偶数时列递增，一种是先循环再判断索引是否合法，另一种是先取的索引边界。

### 复杂度分析

后判断索引是否合法的实现遍历次数为 $$1 + 2 + ... + (m + n) = O((m+n)²)$$, 首先确定上下界的每个元素遍历一次，时间复杂度 $$O(m \cdot n)$$. 空间复杂度都是 $$O(1)$$.

## Reference

*   [LintCode/matrix-zigzag-traversal.cpp at master · kamyu104/LintCode](https://github.com/kamyu104/LintCode/blob/master/C++/matrix-zigzag-traversal.cpp)

## Valid Sudoku

## Question

*   leetcode: [Valid Sudoku | LeetCode OJ](https://leetcode.com/problems/valid-sudoku/)
*   lintcode: [(389) Valid Sudoku](http://www.lintcode.com/en/problem/valid-sudoku/)

```
Determine whether a Sudoku is valid.

The Sudoku board could be partially filled,
where empty cells are filled with the character ..

Example
The following partially filed sudoku is valid. 
```

![valid-sudoku.png](img/e3106e8ab47b2d2bf8cefa4c1d246ef8.jpg)

```
Valid Sudoku

Note
A valid Sudoku board (partially filled) is not necessarily solvable.
Only the filled cells need to be validated.
Clarification
What is Sudoku?

http://sudoku.com.au/TheRules.aspx
https://zh.wikipedia.org/wiki/%E6%95%B8%E7%8D%A8
https://en.wikipedia.org/wiki/Sudoku
http://baike.baidu.com/subview/961/10842669.htm 
```

## 题解

看懂数独的含义就好了，分为三点考虑，一是每行无重复数字；二是每列无重复数字；三是小的九宫格中无重复数字。

### Java

```
class Solution {
    /**
      * @param board: the board
        @return: wether the Sudoku is valid
      */
    public boolean isValidSudoku(char[][] board) {
        if (board == null || board.length == 0) return false;

        // check row
        for (int i = 0; i < 9; i++) {
            boolean[] numUsed = new boolean[9];
            for (int j = 0; j < 9; j++) {
                if (isDuplicate(board[i][j], numUsed)) {
                    return false;
                }
            }
        }

        // check column
        for (int i = 0; i < 9; i++) {
            boolean[] numUsed = new boolean[9];
            for (int j = 0; j < 9; j++) {
                if (isDuplicate(board[j][i], numUsed)) {
                    return false;
                }
            }
        }

        // check sub box
        for (int i = 0; i < 9; i = i + 3) {
            for (int j = 0; j < 9; j = j + 3) {
                if (!isValidBox(board, i, j)) {
                    return false;
                }
            }
        }

        return true;
    }

    private boolean isValidBox(char[][] box, int x, int y) {
        boolean[] numUsed = new boolean[9];
        for (int i = x; i < x + 3; i++) {
            for (int j = y; j < y + 3; j++) {
                if (isDuplicate(box[i][j], numUsed)) {
                    return false;
                }
            }
        }
        return true;
    }

    private boolean isDuplicate(char c, boolean[] numUsed) {
        if (c == '.') {
            return false;
        } else if (numUsed[c - '1']) {
            return true;
        } else {
            numUsed[c - '1'] = true;
            return false;
        }
    }
} 
```

### 源码分析

首先实现两个小的子功能模块判断是否有重复和小的九宫格是否重复。

### 复杂度分析

略

## Reference

*   Soulmachine 的 leetcode 题解