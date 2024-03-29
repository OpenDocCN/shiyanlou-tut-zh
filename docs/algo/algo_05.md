# 第 5 节 Integer Array - 整型数组（三）

## Remove Duplicates from Sorted Array

## Question

*   leetcode: [Remove Duplicates from Sorted Array | LeetCode OJ](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)
*   lintcode: [(100) Remove Duplicates from Sorted Array](http://www.lintcode.com/en/problem/remove-duplicates-from-sorted-array/)

```
Given a sorted array, remove the duplicates in place
such that each element appear only once and return the new length.

Do not allocate extra space for another array,
you must do this in place with constant memory.

For example,
Given input array A = [1,1,2],

Your function should return length = 2, and A is now [1,2].

Example 
```

## 题解

使用两根指针(下标)，一个指针(下标)遍历数组，另一个指针(下标)只取不重复的数置于原数组中。

### C++

```
class Solution {
public:
    /**
     * @param A: a list of integers
     * @return : return an integer
     */
    int removeDuplicates(vector<int> &nums) {
        if (nums.size() <= 1) return nums.size();

        int len = nums.size();
        int newIndex = 0;
        for (int i = 1; i< len; ++i) {
            if (nums[i] != nums[newIndex]) {
                newIndex++;
                nums[newIndex] = nums[i];
            }
        }

        return newIndex + 1;
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param A: a array of integers
     * @return : return an integer
     */
    public int removeDuplicates(int[] nums) {
        if (nums == null) return -1;
        if (nums.length <= 1) return nums.length;

        int newIndex = 0;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] != nums[newIndex]) {
                newIndex++;
                nums[newIndex] = nums[i];
            }
        }

        return newIndex + 1;
    }
} 
```

### 源码分析

注意最后需要返回的是索引值加 1。

### 复杂度分析

遍历一次数组，时间复杂度 $$O(n)$$, 空间复杂度 $$O(1)$$.

## Remove Duplicates from Sorted Array II

## Question

*   leetcode: [Remove Duplicates from Sorted Array II | LeetCode OJ](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/)
*   lintcode: [(101) Remove Duplicates from Sorted Array II](http://www.lintcode.com/en/problem/remove-duplicates-from-sorted-array-ii/)

```
Follow up for "Remove Duplicates":
What if duplicates are allowed at most twice?

For example,
Given sorted array A = [1,1,1,2,2,3],

Your function should return length = 5, and A is now [1,1,2,2,3].
Example 
```

## 题解

在上题基础上加了限制条件元素最多可重复出现两次。~~因此可以在原题的基础上添加一变量跟踪元素重复出现的次数，小于指定值时执行赋值操作。但是需要注意的是重复出现次数`occurence`的初始值(从 1 开始，而不是 0)和 reset 的时机。~~这种方法比较复杂，谢谢 @meishenme 提供的简洁方法，核心思想仍然是两根指针，只不过此时新索引自增的条件是当前遍历的数组值和『新索引』或者『新索引-1』两者之一不同。

### C++

```
class Solution {
public:
    /**
     * @param A: a list of integers
     * @return : return an integer
     */
    int removeDuplicates(vector<int> &nums) {
        if (nums.size() <= 2) return nums.size();

        int len = nums.size();
        int newIndex = 1;
        for (int i = 2; i < len; ++i) {
            if (nums[i] != nums[newIndex] || nums[i] != nums[newIndex - 1]) {
                ++newIndex;
                nums[newIndex] = nums[i];
            }
        }

        return newIndex + 1;
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param A: a array of integers
     * @return : return an integer
     */
    public int removeDuplicates(int[] nums) {
        if (nums == null) return -1;
        if (nums.length <= 2) return nums.length;

        int newIndex = 1;
        for (int i = 2; i < nums.length; i++) {
            if (nums[i] != nums[newIndex] || nums[i] != nums[newIndex - 1]) {
                newIndex++;
                nums[newIndex] = nums[i];
            }
        }

        return newIndex + 1;
    }
} 
```

### 源码分析

遍历数组时 i 从 2 开始，newIndex 初始化为 1 便于分析。

### 复杂度分析

时间复杂度 $$O(n)$$, 空间复杂度 $$O(1)$$.

## Merge Sorted Array

## Question

*   leetcode: [Merge Sorted Array | LeetCode OJ](https://leetcode.com/problems/merge-sorted-array/)
*   lintcode: [(6) Merge Sorted Array](http://www.lintcode.com/en/problem/merge-sorted-array/)

```
Given two sorted integer arrays A and B, merge B into A as one sorted array.

Example
A = [1, 2, 3, empty, empty], B = [4, 5]

After merge, A will be filled as [1, 2, 3, 4, 5]

Note
You may assume that A has enough space (size that is greater or equal to m + n)
to hold additional elements from B.
The number of elements initialized in A and B are m and n respectively. 
```

## 题解

因为本题有 in-place 的限制，故必须从数组末尾的两个元素开始比较；否则就会产生挪动，一旦挪动就会是 $$O(n²)$$ 的。 自尾部向首部逐个比较两个数组内的元素，取较大的置于数组 A 中。由于 A 的容量较 B 大，故最后 `m == 0` 或者 `n == 0` 时仅需处理 B 中的元素，因为 A 中的元素已经在 A 中，无需处理。

### Python

```
class Solution:
    """
    @param A: sorted integer array A which has m elements,
              but size of A is m+n
    @param B: sorted integer array B which has n elements
    @return: void
    """
    def mergeSortedArray(self, A, m, B, n):
        if B is None:
            return A

        index = m + n - 1
        while m > 0 and n > 0:
            if A[m - 1] > B[n - 1]:
                A[index] = A[m - 1]
                m -= 1
            else:
                A[index] = B[n - 1]
                n -= 1
            index -= 1

        # B has elements left
        while n > 0:
            A[index] = B[n - 1]
            n -= 1
            index -= 1 
```

### C++

```
class Solution {
public:
    /**
     * @param A: sorted integer array A which has m elements,
     *           but size of A is m+n
     * @param B: sorted integer array B which has n elements
     * @return: void
     */
    void mergeSortedArray(int A[], int m, int B[], int n) {
        int index = m + n - 1;
        while (m > 0 && n > 0) {
            if (A[m - 1] > B[n - 1]) {
                A[index] = A[m - 1];
                --m;
            } else {
                A[index] = B[n - 1];
                --n;
            }
            --index;
        }

        // B has elements left
        while (n > 0) {
            A[index] = B[n - 1];
            --n;
            --index;
        }
    }
}; 
```

### Java

```
class Solution {
    /**
     * @param A: sorted integer array A which has m elements,
     *           but size of A is m+n
     * @param B: sorted integer array B which has n elements
     * @return: void
     */
    public void mergeSortedArray(int[] A, int m, int[] B, int n) {
        if (A == null || B == null) return;

        int index = m + n - 1;
        while (m > 0 && n > 0) {
            if (A[m - 1] > B[n - 1]) {
                A[index] = A[m - 1];
                m--;
            } else {
                A[index] = B[n - 1];
                n--;
            }
            index--;
        }

        // B has elements left
        while (n > 0) {
            A[index] = B[n - 1];
            n--;
            index--;
        }
    }
} 
```

### 源码分析

第一个 while 只能用条件与。

### 复杂度分析

最坏情况下需要遍历两个数组中所有元素，时间复杂度为 $$O(n)$$. 空间复杂度 $$O(1)$$.

## Merge Sorted Array II

## Question

*   lintcode: [(64) Merge Sorted Array II](http://www.lintcode.com/en/problem/merge-sorted-array-ii/)

```
Merge two given sorted integer array A and B into a new sorted integer array.

Example
A=[1,2,3,4]

B=[2,4,5,6]

return [1,2,2,3,4,4,5,6]

Challenge
How can you optimize your algorithm
if one array is very large and the other is very small? 
```

## 题解

上题要求 in-place, 此题要求返回新数组。由于可以生成新数组，故使用常规思路按顺序遍历即可。

### Python

```
class Solution:
    #@param A and B: sorted integer array A and B.
    #@return: A new sorted integer array
    def mergeSortedArray(self, A, B):
        if A is None or len(A) == 0:
            return B
        if B is None or len(B) == 0:
            return A

        C = []
        aLen, bLen = len(A), len(B)
        i, j = 0, 0
        while i < aLen and j < bLen:
            if A[i] < B[j]:
                C.append(A[i])
                i += 1
            else:
                C.append(B[j])
                j += 1

        # A has elements left
        while i < aLen:
            C.append(A[i])
            i += 1

        # B has elements left
        while j < bLen:
            C.append(B[j])
            j += 1

        return C 
```

### C++

```
class Solution {
public:
    /**
     * @param A and B: sorted integer array A and B.
     * @return: A new sorted integer array
     */
    vector<int> mergeSortedArray(vector<int> &A, vector<int> &B) {
        if (A.empty()) return B;
        if (B.empty()) return A;

        int aLen = A.size(), bLen = B.size();
        vector<int> C;
        int i = 0, j = 0;
        while (i < aLen && j < bLen) {
            if (A[i] < B[j]) {
                C.push_back(A[i]);
                ++i;
            } else {
                C.push_back(B[j]);
                ++j;
            }
        }

        // A has elements left
        while (i < aLen) {
            C.push_back(A[i]);
            ++i;
        }

        // B has elements left
        while (j < bLen) {
            C.push_back(B[j]);
            ++j;
        }

        return C;
    }
}; 
```

### Java

```
class Solution {
    /**
     * @param A and B: sorted integer array A and B.
     * @return: A new sorted integer array
     */
    public ArrayList<Integer> mergeSortedArray(ArrayList<Integer> A, ArrayList<Integer> B) {
        if (A == null || A.isEmpty()) return B;
        if (B == null || B.isEmpty()) return A;

        ArrayList<Integer> C = new ArrayList<Integer>();
        int aLen = A.size(), bLen = B.size();
        int i = 0, j = 0;
        while (i < aLen && j < bLen) {
            if (A.get(i) < B.get(j)) {
                C.add(A.get(i));
                i++;
            } else {
                C.add(B.get(j));
                j++;
            }
        }

        // A has elements left
        while (i < aLen) {
            C.add(A.get(i));
            i++;
        }

        // B has elements left
        while (j < bLen) {
            C.add(B.get(j));
            j++;
        }

        return C;
    }
} 
```

### 源码分析

分三步走，后面分别单独处理剩余的元素。

### 复杂度分析

遍历 A, B 数组各一次，时间复杂度 $$O(n)$$, 空间复杂度 $$O(1)$$.

#### Challenge

两个倒排列表，一个特别大，一个特别小，如何 Merge？此时应该考虑用一个二分法插入小的，即内存拷贝。

## Median

## Question

*   lintcode: [(80) Median](http://www.lintcode.com/en/problem/median/)

```
Given a unsorted array with integers, find the median of it.

A median is the middle number of the array after it is sorted.

If there are even numbers in the array, return the N/2-th number after sorted.

Example
Given [4, 5, 1, 2, 3], return 3

Given [7, 9, 4, 5], return 5

Challenge
O(n) time. 
```

## 题解

寻找未排序数组的中位数，简单粗暴的方法是先排序后输出中位数索引处的数，但是基于比较的排序算法的时间复杂度为 $$O(n \log n)$$, 不符合题目要求。线性时间复杂度的排序算法常见有计数排序、桶排序和基数排序，这三种排序方法的空间复杂度均较高，且依赖于输入数据特征（数据分布在有限的区间内），用在这里并不是比较好的解法。

由于这里仅需要找出中位数，即找出数组中前半个长度的较大的数，不需要进行完整的排序，说到这你是不是想到了快速排序了呢？快排的核心思想就是以基准为界将原数组划分为左小右大两个部分，用在这十分合适。快排的实现见 [Quick Sort](http://algorithm.yuanbin.me/zh-hans/basics_sorting/quick_sort.html), 由于调用一次快排后基准元素的最终位置是知道的，故递归的终止条件即为当基准元素的位置(索引)满足中位数的条件时(左半部分长度为原数组长度一半)即返回最终结果。由于函数原型中左右最小索引并不总是原数组的最小最大，故需要引入相对位置(长度)也作为其中之一的参数。若左半部分长度偏大，则下一次递归排除右半部分，反之则排除左半部分。

### Python

```
class Solution:
    """
    @param nums: A list of integers.
    @return: An integer denotes the middle number of the array.
    """
    def median(self, nums):
        if not nums:
            return -1
        return self.helper(nums, 0, len(nums) - 1, (1 + len(nums)) / 2)

    def helper(self, nums, l, u, size):
        if l >= u:
            return nums[u]

        m = l
        for i in xrange(l + 1, u + 1):
            if nums[i] < nums[l]:
                m += 1
                nums[m], nums[i] = nums[i], nums[m]

        # swap between m and l after partition, important!
        nums[m], nums[l] = nums[l], nums[m]

        if m - l + 1 == size:
            return nums[m]
        elif m - l + 1 > size:
            return self.helper(nums, l, m - 1, size)
        else:
            return self.helper(nums, m + 1, u, size - (m - l + 1)) 
```

### C++

```
class Solution {
public:
    /**
     * @param nums: A list of integers.
     * @return: An integer denotes the middle number of the array.
     */
    int median(vector<int> &nums) {
        if (nums.empty()) return 0;

        int len = nums.size();
        return helper(nums, 0, len - 1, (len + 1) / 2);
    }

private:
    int helper(vector<int> &nums, int l, int u, int size) {
        // if (l >= u) return nums[u];

        int m = l; // index m to track pivot
        for (int i = l + 1; i <= u; ++i) {
            if (nums[i] < nums[l]) {
                ++m;
                int temp = nums[i];
                nums[i] = nums[m];
                nums[m] = temp;
            }
        }

        // swap with the pivot
        int temp = nums[m];
        nums[m] = nums[l];
        nums[l] = temp;

        if (m - l + 1 == size) {
            return nums[m];
        } else if (m - l + 1 > size) {
            return helper(nums, l, m - 1, size);
        } else {
            return helper(nums, m + 1, u, size - (m - l + 1));
        }
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param nums: A list of integers.
     * @return: An integer denotes the middle number of the array.
     */
    public int median(int[] nums) {
        if (nums == null) return -1;

        return helper(nums, 0, nums.length - 1, (nums.length + 1) / 2);
    }

    // l: lower, u: upper, m: median
    private int helper(int[] nums, int l, int u, int size) {
        if (l >= u) return nums[u];

        int m = l;
        for (int i = l + 1; i <= u; i++) {
            if (nums[i] < nums[l]) {
                m++;
                int temp = nums[m];
                nums[m] = nums[i];
                nums[i] = temp;
            }
        }
        // swap between array[m] and array[l]
        // put pivot in the mid
        int temp = nums[m];
        nums[m] = nums[l];
        nums[l] = temp;

        if (m - l + 1 == size) {
            return nums[m];
        } else if (m - l + 1 > size) {
            return helper(nums, l, m - 1, size);
        } else {
            return helper(nums, m + 1, u, size - (m - l + 1));
        }
    }
} 
```

### 源码分析

以相对距离(长度)进行理解，递归终止步的条件一直保持不变(比较左半部分的长度)。

以题目中给出的样例进行分析，`size` 传入的值可为`(len(nums) + 1) / 2`, 终止条件为`m - l + 1 == size`, 含义为基准元素到索引为`l`的元素之间(左半部分)的长度(含)与`(len(nums) + 1) / 2`相等。若`m - l + 1 > size`, 即左半部分长度偏大，此时递归终止条件并未变化，因为`l`的值在下一次递归调用时并未改变，所以仍保持为`size`; 若`m - l + 1 < size`, 左半部分长度偏小，下一次递归调用右半部分，由于此时左半部分的索引值已变化，故`size`应改为下一次在右半部分数组中的终止条件`size - (m - l + 1)`, 含义为原长度`size`减去左半部分数组的长度`m - l + 1`.

### 复杂度分析

和快排类似，这里也有最好情况与最坏情况，平均情况下，索引`m`每次都处于中央位置，即每次递归后需要遍历的数组元素个数减半，故总的时间复杂度为 $$O(n (1 + 1/2 + 1/4 + ...)) = O(2n)$$, 最坏情况下为平方。使用了临时变量，空间复杂度为 $$O(1)$$, 满足题目要求。

## Partition Array by Odd and Even

## Question

*   lintcode: [(373) Partition Array by Odd and Even](http://www.lintcode.com/en/problem/partition-array-by-odd-and-even/)
*   [Segregate Even and Odd numbers - GeeksforGeeks](http://www.geeksforgeeks.org/segregate-even-and-odd-numbers/)

```
Partition an integers array into odd number first and even number second.

Example
Given [1, 2, 3, 4], return [1, 3, 2, 4]

Challenge
Do it in-place. 
```

## 题解

将数组中的奇数和偶数分开，使用『两根指针』的方法最为自然，奇数在前，偶数在后，若不然则交换之。

### Java

```
public class Solution {
    /**
     * @param nums: an array of integers
     * @return: nothing
     */
    public void partitionArray(int[] nums) {
        if (nums == null) return;

        int left = 0, right = nums.length - 1;
        while (left < right) {
            // odd number
            while (left < right && nums[left] % 2 != 0) {
                left++;
            }
            // even number
            while (left < right && nums[right] % 2 == 0) {
                right--;
            }
            // swap
            if (left < right) {
                int temp = nums[left];
                nums[left] = nums[right];
                nums[right] = temp;
            }
        }
    }
} 
```

### C++

```
 void partitionArray(vector<int> &nums) {
        if (nums.empty()) return;

        int i=0, j=nums.size()-1;
        while (i<j) {
            while (i<j && nums[i]%2!=0) i++;
            while (i<j && nums[j]%2==0) j--;
            if (i != j) swap(nums[i], nums[j]);
        }
    } 
```

### 源码分析

注意处理好边界即循环时保证`left < right`.

### 复杂度分析

遍历一次数组，时间复杂度为 $$O(n)$$, 使用了两根指针，空间复杂度 $$O(1)$$.

## Kth Largest Element

## Question

*   leetcode: [Kth Largest Element in an Array | LeetCode OJ](https://leetcode.com/problems/kth-largest-element-in-an-array/)
*   lintcode: [(5) Kth Largest Element](http://www.lintcode.com/en/problem/kth-largest-element/)

```
Find K-th largest element in an array.

Example
In array [9,3,2,4,8], the 3rd largest element is 4.

In array [1,2,3,4,5], the 1st largest element is 5,
2nd largest element is 4, 3rd largest element is 3 and etc.

Note
You can swap elements in the array

Challenge
O(n) time, O(1) extra memory. 
```

## 题解

找第 K 大数，基于比较的排序的方法时间复杂度为 $$O(n)$$, 数组元素无区间限定，故无法使用线性排序。由于只是需要找第 K 大数，这种类型的题通常需要使用快排的思想解决。[Quick Sort](http://algorithm.yuanbin.me/zh-hans/basics_sorting/quick_sort.html) 总结了一些经典模板。这里比较基准值最后的位置的索引值和 K 的大小关系即可递归求解。

### Java

```
class Solution {
    //param k : description of k
    //param numbers : array of numbers
    //return: description of return
    public int kthLargestElement(int k, ArrayList<Integer> numbers) {
        if (numbers == null || numbers.isEmpty()) return -1;

        int result = qSort(numbers, 0, numbers.size() - 1, k);
        return result;
    }

    private int qSort(ArrayList<Integer> nums, int l, int u, int k) {
        // l should not greater than u
        if (l >= u) return nums.get(u);

        // index m of nums
        int m = l;
        for (int i = l + 1; i <= u; i++) {
            if (nums.get(i) > nums.get(l)) {
                m++;
                Collections.swap(nums, m, i);
            }
        }
        Collections.swap(nums, m, l);

        if (m + 1 == k) {
            return nums.get(m);
        } else if (m + 1 > k) {
            return qSort(nums, l, m - 1, k);
        } else {
            return qSort(nums, m + 1, u, k);
        }
    }
} 
```

### 源码分析

递归的终止条件有两个，一个是左边界的值等于右边界(实际中其实不会有 l > u), 另一个则是索引值 `m + 1 == k`. 这里找的是第 K 大数，故为降序排列，for 循环中使用`nums.get(i) > nums.get(l)` 而不是小于号。

### 复杂度分析

最坏情况下需要遍历 $$ n + n - 1 + ... + 1 = O(n²)$$, 平均情况下 $$n + n/2 + n/4 + ... + 1 = O(2n)=O(n)$$. 故平均情况时间复杂度为 $$O(n)$$. 交换数组的值时使用了额外空间，空间复杂度 $$O(1)$$.