# 第 23 节 Exhaustive Search - 穷竭搜索（二）

## Next Permutation

## Question

*   leetcode: [Next Permutation | LeetCode OJ](https://leetcode.com/problems/next-permutation/)
*   lintcode: [(52) Next Permutation](http://www.lintcode.com/en/problem/next-permutation/)

### Problem Statement

Given a list of integers, which denote a permutation.

Find the next permutation in ascending order.

#### Example

For `[1,3,2,3]`, the next permutation is `[1,3,3,2]`

For `[4,3,2,1]`, the next permutation is `[1,2,3,4]`

#### Note

The list may contains duplicate integers.

## 题解

找下一个升序排列，C++ STL 源码剖析一书中有提及，[Permutations](http://algorithm.yuanbin.me/zh-hans/exhaustive_search/permutations.html) 一小节中也有详细介绍，下面简要介绍一下字典序算法：

1.  从后往前寻找索引满足 `a[k] < a[k + 1]`, 如果此条件不满足，则说明已遍历到最后一个。
2.  从后往前遍历，找到第一个比`a[k]`大的数`a[l]`, 即`a[k] < a[l]`.
3.  交换`a[k]`与`a[l]`.
4.  反转`k + 1 ~ n`之间的元素。

由于这道题中规定对于`[4,3,2,1]`, 输出为`[1,2,3,4]`, 故在第一步稍加处理即可。

### Python

```
class Solution:
    # @param num :  a list of integer
    # @return : a list of integer
    def nextPermutation(self, num):
        if num is None or len(num) <= 1:
            return num
        # step1: find nums[i] < nums[i + 1], Loop backwards
        i = 0
        for i in xrange(len(num) - 2, -1, -1):
            if num[i] < num[i + 1]:
                break
            elif i == 0:
                # reverse nums if reach maximum
                num = num[::-1]
                return num
        # step2: find nums[i] < nums[j], Loop backwards
        j = 0
        for j in xrange(len(num) - 1, i, -1):
            if num[i] < num[j]:
                break
        # step3: swap betwenn nums[i] and nums[j]
        num[i], num[j] = num[j], num[i]
        # step4: reverse between [i + 1, n - 1]
        num[i + 1:len(num)] = num[len(num) - 1:i:-1]

        return num 
```

### C++

```
class Solution {
public:
    /**
     * @param nums: An array of integers
     * @return: An array of integers that's next permuation
     */
    vector<int> nextPermutation(vector<int> &nums) {
        if (nums.empty() || nums.size() <= 1) {
            return nums;
        }
        // step1: find nums[i] < nums[i + 1]
        int i = 0;
        for (i = nums.size() - 2; i >= 0; --i) {
            if (nums[i] < nums[i + 1]) {
                break;
            } else if (0 == i) {
                // reverse nums if reach maximum
                reverse(nums, 0, nums.size() - 1);
                return nums;
            }
        }
        // step2: find nums[i] < nums[j]
        int j = 0;
        for (j = nums.size() - 1; j > i; --j) {
            if (nums[i] < nums[j]) break;
        }
        // step3: swap betwenn nums[i] and nums[j]
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
        // step4: reverse between [i + 1, n - 1]
        reverse(nums, i + 1, nums.size() - 1);

        return nums;

    }

private:
    void reverse(vector<int>& nums, int start, int end) {
        for (int i = start, j = end; i < j; ++i, --j) {
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
        }
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param nums: an array of integers
     * @return: return nothing (void), do not return anything, modify nums in-place instead
     */
    public void nextPermutation(int[] nums) {
        if (nums == null || nums.length == 0) return;

        // step1: search the first nums[k] < nums[k+1] backward
        int k = -1;
        for (int i = nums.length - 2; i >= 0; i--) {
            if (nums[i] < nums[i + 1]) {
                k = i;
                break;
            }
        }
        // if current rank is the largest, reverse it to smallest, return
        if (k == -1) {
            reverse(nums, 0, nums.length - 1);
            return;
        }

        // step2: search the first nums[k] < nums[l] backward
        int l = nums.length - 1;
        while (l > k && nums[l] <= nums[k]) l--;

        // step3: swap nums[k] with nums[l]
        int temp = nums[k];
        nums[k] = nums[l];
        nums[l] = temp;

        // step4: reverse between k+1 and nums.length-1;
        reverse(nums, k + 1, nums.length - 1);
    }

    private void reverse(int[] nums, int lb, int ub) {
        for (int i = lb, j = ub; i < j; i++, j--) {
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
        }
    }
} 
```

### 源码分析

和 Permutation 一小节类似，这里只需要注意在 step 1 中`i == -1`时需要反转之以获得最小的序列。对于有重复元素，只要在 step1 和 step2 中判断元素大小时不取等号即可。Lintcode 上给的注释要求（其实是 Leetcode 上的要求）和实际给出的输出不一样。

### 复杂度分析

最坏情况下，遍历两次原数组，反转一次数组，时间复杂度为 $$O(n)$$, 使用了 temp 临时变量，空间复杂度可认为是 $$O(1)$$.

## Previous Permuation

## Question

*   lintcode: [(51) Previous Permuation](http://www.lintcode.com/en/problem/previous-permuation/)

### Problem Statement

Given a list of integers, which denote a permutation.

Find the previous permutation in ascending order.

#### Example

For `[1,3,2,3]`, the previous permutation is `[1,2,3,3]`

For `[1,2,3,4]`, the previous permutation is `[4,3,2,1]`

#### Note

The list may contains duplicate integers.

## 题解

和前一题 [Next Permutation](http://algorithm.yuanbin.me/zh-hans/exhaustive_search/next_permutation.html) 非常类似，这里找上一个排列，仍然使用字典序算法，大致步骤如下：

1.  从后往前寻找索引满足 `a[k] > a[k + 1]`, 如果此条件不满足，则说明已遍历到最后一个。
2.  从后往前遍历，找到第一个比`a[k]`小的数`a[l]`, 即`a[k] > a[l]`.
3.  交换`a[k]`与`a[l]`.
4.  反转`k + 1 ~ n`之间的元素。

为何不从前往后呢？因为只有从后往前才能保证得到的是相邻的排列，可以举个实际例子自行分析。

### Python

```
class Solution:
    # @param num :  a list of integer
    # @return : a list of integer
    def previousPermuation(self, num):
        if num is None or len(num) <= 1:
            return num
        # step1: find nums[i] > nums[i + 1], Loop backwards
        i = 0
        for i in xrange(len(num) - 2, -1, -1):
            if num[i] > num[i + 1]:
                break
            elif i == 0:
                # reverse nums if reach maximum
                num = num[::-1]
                return num
        # step2: find nums[i] > nums[j], Loop backwards
        j = 0
        for j in xrange(len(num) - 1, i, -1):
            if num[i] > num[j]:
                break
        # step3: swap betwenn nums[i] and nums[j]
        num[i], num[j] = num[j], num[i]
        # step4: reverse between [i + 1, n - 1]
        num[i + 1:len(num)] = num[len(num) - 1:i:-1]

        return num 
```

### C++

```
class Solution {
public:
    /**
     * @param nums: An array of integers
     * @return: An array of integers that's previous permuation
     */
    vector<int> previousPermuation(vector<int> &nums) {
        if (nums.empty() || nums.size() <= 1) {
            return nums;
        }
        // step1: find nums[i] > nums[i + 1]
        int i = 0;
        for (i = nums.size() - 2; i >= 0; --i) {
            if (nums[i] > nums[i + 1]) {
                break;
            } else if (0 == i) {
                // reverse nums if reach minimum
                reverse(nums, 0, nums.size() - 1);
                return nums;
            }
        }
        // step2: find nums[i] > nums[j]
        int j = 0;
        for (j = nums.size() - 1; j > i; --j) {
            if (nums[i] > nums[j]) break;
        }
        // step3: swap betwenn nums[i] and nums[j]
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
        // step4: reverse between [i + 1, n - 1]
        reverse(nums, i + 1, nums.size() - 1);

        return nums;
    }

private:
    void reverse(vector<int>& nums, int start, int end) {
        for (int i = start, j = end; i < j; ++i, --j) {
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
        }
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param nums: A list of integers
     * @return: A list of integers that's previous permuation
     */
    public ArrayList<Integer> previousPermuation(ArrayList<Integer> nums) {
        ArrayList<Integer> perm = new ArrayList<Integer>(nums);
        if (nums == null || nums.size() == 0) return perm;

        // step1: search the first num[k] > num[k+1] backward
        int k = -1;
        for (int i = perm.size() - 2; i >= 0; i--) {
            if (perm.get(i) > perm.get(i + 1)) {
                k = i;
                break;
            }
        }
        // if current rank is the smallest, reverse it to largest, return
        if (k == -1) {
            reverse(perm, 0, perm.size() - 1);
            return perm;
        }

        // step2: search the first perm[k] > perm[l] backward
        int l = perm.size() - 1;
        while (l > k && perm.get(l) >= perm.get(k)) {
            l--;
        }

        // step3: swap perm[k] with perm[l]
        Collections.swap(perm, k, l);

        // step4: reverse between k+1 and perm.length-1;
        reverse(perm, k + 1, perm.size() - 1);

        return perm;
    }

    private void reverse(List<Integer> nums, int lb, int ub) {
        for (int i = lb, j = ub; i < j; i++, j--) {
            Collections.swap(nums, i, j);
        }
    }
} 
```

### 源码分析

和 Permutation 一小节类似，这里只需要注意在 step 1 中`i == -1`时需要反转之以获得最大的序列。对于有重复元素，只要在 step1 和 step2 中判断元素大小时不取等号即可。

### 复杂度分析

最坏情况下，遍历两次原数组，反转一次数组，时间复杂度为 $$O(n)$$, 使用了 temp 临时变量，空间复杂度可认为是 $$O(1)$$.

## Permutation Index

## Question

*   lintcode: [(197) Permutation Index](http://www.lintcode.com/en/problem/permutation-index/)

### Problem Statement

Given a permutation which contains no repeated number, find its index in all the permutations of these numbers, which are ordered in lexicographical order. The index begins at 1.

#### Example

Given [1,2,4], return 1.

## 题解

做过 next permutation 系列题的话自然能想到不断迭代直至最后一个，最后返回计数器的值即可。这种方法理论上自然是可行的，但是最坏情况下时间复杂度为 $$O(n!)$$, 显然是不能接受的。由于这道题只是列出某给定 permutation 的相对顺序(index), 故我们可从 permutation 的特点出发进行分析。

以序列`1, 2, 4`为例，其不同的排列共有 `3!=6` 种，以排列`[2, 4, 1]`为例，若将 1 置于排列的第一位，后面的排列则有 `2!=2` 种。将 2 置于排列的第一位，由于`[2, 4, 1]`的第二位 4 在 1, 2, 4 中为第 3 大数，故第二位可置 1 或者 2，那么相应的排列共有 `2 * 1! = 2`种，最后一位 1 为最小的数，故比其小的排列为 0。综上，可参考我们常用的十进制和二进制的转换，对于`[2, 4, 1]`, 可总结出其排列的`index`为`2! * (2 - 1) + 1! * (3 - 1) + 0! * (1 - 1) + 1`.

以上分析看似正确无误，实则有个关键的漏洞，在排定第一个数 2 后，第二位数只可为 1 或者 4，而无法为 2, **故在计算最终的 index 时需要动态计算某个数的相对大小。**按照从低位到高位进行计算，我们可通过两重循环得出到某个索引处值的相对大小。

### Python

```
class Solution:
    # @param {int[]} A an integer array
    # @return {long} a long integer
    def permutationIndex(self, A):
        if A is None or len(A) == 0:
            return 0

        index = 1
        factor = 1
        for i in xrange(len(A) - 1, -1, -1):
            rank = 0
            for j in xrange(i + 1, len(A)):
                if A[i] > A[j]:
                    rank += 1

            index += rank * factor
            factor *= (len(A) - i)

        return index 
```

### C++

```
class Solution {
public:
    /**
     * @param A an integer array
     * @return a long integer
     */
    long long permutationIndex(vector<int>& A) {
        if (A.empty()) return 0;

        long long index = 1;
        long long factor = 1;
        for (int i = A.size() - 1; i >= 0; --i) {
            int rank = 0;
            for (int j = i + 1; j < A.size(); ++j) {
                if (A[i] > A[j]) ++rank;
            }
            index += rank * factor;
            factor *= (A.size() - i);
        }

        return index;
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param A an integer array
     * @return a long integer
     */
    public long permutationIndex(int[] A) {
        if (A == null || A.length == 0) return 0L;

        long index = 1, fact = 1;
        for (int i = A.length - 1; i >= 0; i--) {
            // get rank in every iteration
            int rank = 0;
            for (int j = i + 1; j < A.length; j++) {
                if (A[i] > A[j]) rank++;
            }

            index += rank * fact;
            fact *= (A.length - i);
        }

        return index;
    }
} 
```

### 源码分析

注意 index 和 factor 的初始值，rank 的值每次计算时都需要重新置零，index 先自增，factorial 后自乘求阶乘。

### 复杂度分析

双重 for 循环，时间复杂度为 $$O(n²)$$. 使用了部分额外空间，空间复杂度 $$O(1)$$.

## Reference

*   [Permutation Index](http://www.geekviewpoint.com/java/numbers/permutation_index)

## Permutation Index II

## Question

*   lintcode: [(198) Permutation Index II](http://www.lintcode.com/en/problem/permutation-index-ii/)

### Problem Statement

Given a permutation which may contain repeated numbers, find its index in all the permutations of these numbers, which are ordered in lexicographical order. The index begins at 1.

#### Example

Given the permutation `[1, 4, 2, 2]`, return `3`.

## 题解

题 [Permutation Index](http://algorithm.yuanbin.me/zh-hans/exhaustive_search/permutation_index.html) 的扩展，这里需要考虑重复元素，有无重复元素最大的区别在于原来的`1!, 2!, 3!...`等需要除以重复元素个数的阶乘，颇有点高中排列组合题的味道。记录重复元素个数同样需要动态更新，引入哈希表这个万能的工具较为方便。

### Python

```
class Solution:
    # @param {int[]} A an integer array
    # @return {long} a long integer
    def permutationIndexII(self, A):
        if A is None or len(A) == 0:
            return 0

        index = 1
        factor = 1
        for i in xrange(len(A) - 1, -1, -1):
            hash_map = {A[i]: 1}
            rank = 0
            for j in xrange(i + 1, len(A)):
                if A[j] in hash_map.keys():
                    hash_map[A[j]] += 1
                else:
                    hash_map[A[j]] = 1
                # get rank
                if A[i] > A[j]:
                    rank += 1

            index += rank * factor / self.dupPerm(hash_map)
            factor *= (len(A) - i)

        return index

    def dupPerm(self, hash_map):
        if hash_map is None or len(hash_map) == 0:
            return 0
        dup = 1
        for val in hash_map.values():
            dup *= self.factorial(val)

        return dup

    def factorial(self, n):
        r = 1
        for i in xrange(1, n + 1):
            r *= i

        return r 
```

### C++

```
class Solution {
public:
    /**
     * @param A an integer array
     * @return a long integer
     */
    long long permutationIndexII(vector<int>& A) {
        if (A.empty()) return 0;

        long long index = 1;
        long long factor = 1;
        for (int i = A.size() - 1; i >= 0; --i) {
            int rank = 0;
            unordered_map<int, int> hash;
            ++hash[A[i]];
            for (int j = i + 1; j < A.size(); ++j) {
                ++hash[A[j]];

                if (A[i] > A[j]) {
                    ++rank;
                }
            }
            index += rank * factor / dupPerm(hash);
            factor *= (A.size() - i);
        }

        return index;
    }

private:
    long long dupPerm(unordered_map<int, int> hash) {
        if (hash.empty()) return 1;

        long long dup = 1;
        for (auto it = hash.begin(); it != hash.end(); ++it) {
            dup *= fact(it->second);
        }

        return dup;
    }

    long long fact(int num) {
        long long val = 1;
        for (int i = 1; i <= num; ++i) {
            val *= i;
        }

        return val;
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param A an integer array
     * @return a long integer
     */
    public long permutationIndexII(int[] A) {
        if (A == null || A.length == 0) return 0L;

        Map<Integer, Integer> hashmap = new HashMap<Integer, Integer>();
        long index = 1, fact = 1, multiFact = 1;
        for (int i = A.length - 1; i >= 0; i--) {
            // collect its repeated times and update multiFact
            if (hashmap.containsKey(A[i])) {
                hashmap.put(A[i], hashmap.get(A[i]) + 1);
                multiFact *= hashmap.get(A[i]);
            } else {
                hashmap.put(A[i], 1);
            }
            // get rank every turns
            int rank = 0;
            for (int j = i + 1; j < A.length; j++) {
                if (A[i] > A[j]) rank++;
            }
            // do divide by multiFact
            index += rank * fact / multiFact;
            fact *= (A.length - i);
        }

        return index;
    }
} 
```

### 源码分析

在计算重复元素个数的阶乘时需要注意更新`multiFact`的值即可，不必每次都去计算哈希表中的值。对元素`A[i]`需要加入哈希表 - `hash.put(A[i], 1);`，设想一下`2, 2, 1, 1`的计算即可知。

### 复杂度分析

双重 for 循环，时间复杂度为 $$O(n²)$$, 使用了哈希表，空间复杂度为 $$O(n)$$.

## Unique Binary Search Trees II

## Question

*   leetcode: [Unique Binary Search Trees II | LeetCode OJ](https://leetcode.com/problems/unique-binary-search-trees-ii/)
*   lintcode: [(164) Unique Binary Search Trees II](http://www.lintcode.com/en/problem/unique-binary-search-trees-ii/)

```
Given n, generate all structurally unique BST's
(binary search trees) that store values 1...n.

Example
Given n = 3, your program should return all 5 unique BST's shown below.

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3 
```

## 题解

题 [Unique Binary Search Trees](http://algorithm.yuanbin.me/zh-hans/math_and_bit_manipulation/unique_binary_search_trees.html) 的升级版，这道题要求的不是二叉搜索树的数目，而是要构建这样的树。分析方法仍然是可以借鉴的，核心思想为利用『二叉搜索树』的定义，如果以 i 为根节点，那么其左子树由[1, i - 1]构成，右子树由[i + 1, n] 构成。要构建包含 1 到 n 的二叉搜索树，只需遍历 1 到 n 中的数作为根节点，以`i`为界将数列分为左右两部分，小于`i`的数作为左子树，大于`i`的数作为右子树，使用两重循环将左右子树所有可能的组合链接到以`i`为根节点的节点上。

容易看出，以上求解的思路非常适合用递归来处理，接下来便是设计递归的终止步、输入参数和返回结果了。由以上分析可以看出递归严重依赖数的区间和`i`，那要不要将`i`也作为输入参数的一部分呢？首先可以肯定的是必须使用『数的区间』这两个输入参数，又因为`i`是随着『数的区间』这两个参数的，故不应该将其加入到输入参数中。分析方便，不妨设『数的区间』两个输入参数分别为`start`和`end`.

接下来谈谈终止步的确定，由于根据`i`拆分左右子树的过程中，递归调用的方法中入口参数会缩小，且存在`start <= i <= end`, 故终止步为`start > end`. 那要不要对`start == end`返回呢？保险起见可以先写上，后面根据情况再做删改。总结以上思路，简单的伪代码如下：

```
helper(start, end) {
    result;
    if (start > end) {
        result.push_back(NULL);
        return;
    } else if (start == end) {
        result.push_back(TreeNode(i));
        return;
    }

    // dfs
    for (int i = start; i <= end; ++i) {
        leftTree = helper(start, i - 1);
        rightTree = helper(i + 1, end);
        // link left and right sub tree to the root i
        for (j in leftTree ){
            for (k in rightTree) {
                root = TreeNode(i);
                root->left = leftTree[j];
                root->right = rightTree[k];
                result.push_back(root);
            }
        }
    }

    return result;
} 
```

大致的框架如上所示，我们来个简单的数据验证下，以[1, 2, 3]为例，调用堆栈图如下所示：

1.  helper(1,3)
    *   [leftTree]: helper(1, 0) ==> return NULL
    *   ---loop i = 2---
    *   [rightTree]: helper(2, 3)
        1.  [leftTree]: helper(2,1) ==> return NULL
        2.  [rightTree]: helper(3,3) ==> return node(3)
        3.  [for loop]: ==> return (2->3)
    *   ---loop i = 3---
        1.  [leftTree]: helper(2,2) ==> return node(2)
        2.  [rightTree]: helper(4,3) ==> return NULL
        3.  [for loop]: ==> return (3->2)
2.  ...

简单验证后可以发现这种方法的**核心为递归地构造左右子树并将其链接到相应的根节点中。**对于`start`和`end`相等的情况的，其实不必单独考虑，因为`start == end`时其左右子树均返回空，故在`for`循环中返回根节点。当然单独考虑可减少递归栈的层数，但实际测下来后发现运行时间反而变长了不少 :(

### Python

```
"""
Definition of TreeNode:
class TreeNode:
    def __init__(self, val):
        this.val = val
        this.left, this.right = None, None
"""
class Solution:
    # @paramn n: An integer
    # @return: A list of root
    def generateTrees(self, n):
        return self.helper(1, n)

    def helper(self, start, end):
        result = []
        if start > end:
            result.append(None)
            return result

        for i in xrange(start, end + 1):
            # generate left and right sub tree
            leftTree = self.helper(start, i - 1)
            rightTree = self.helper(i + 1, end)
            # link left and right sub tree to root(i)
            for j in xrange(len(leftTree)):
                for k in xrange(len(rightTree)):
                    root = TreeNode(i)
                    root.left = leftTree[j]
                    root.right = rightTree[k]
                    result.append(root)

        return result 
```

### C++

```
/**
 * Definition of TreeNode:
 * class TreeNode {
 * public:
 *     int val;
 *     TreeNode *left, *right;
 *     TreeNode(int val) {
 *         this->val = val;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     * @paramn n: An integer
     * @return: A list of root
     */
    vector<TreeNode *> generateTrees(int n) {
        return helper(1, n);
    }

private:
    vector<TreeNode *> helper(int start, int end) {
        vector<TreeNode *> result;
        if (start > end) {
            result.push_back(NULL);
            return result;
        }

        for (int i = start; i <= end; ++i) {
            // generate left and right sub tree
            vector<TreeNode *> leftTree = helper(start, i - 1);
            vector<TreeNode *> rightTree = helper(i + 1, end);
            // link left and right sub tree to root(i)
            for (int j = 0; j < leftTree.size(); ++j) {
                for (int k = 0; k < rightTree.size(); ++k) {
                    TreeNode *root = new TreeNode(i);
                    root->left = leftTree[j];
                    root->right = rightTree[k];
                    result.push_back(root);
                }
            }
        }

        return result;
    }
}; 
```

### Java

```
/**
 * Definition of TreeNode:
 * public class TreeNode {
 *     public int val;
 *     public TreeNode left, right;
 *     public TreeNode(int val) {
 *         this.val = val;
 *         this.left = this.right = null;
 *     }
 * }
 */
public class Solution {
    /**
     * @paramn n: An integer
     * @return: A list of root
     */
    public List<TreeNode> generateTrees(int n) {
        return helper(1, n);
    }

    private List<TreeNode> helper(int start, int end) {
        List<TreeNode> result = new ArrayList<TreeNode>();
        if (start > end) {
            result.add(null);
            return result;
        }

        for (int i = start; i <= end; i++) {
            // generate left and right sub tree
            List<TreeNode> leftTree = helper(start, i - 1);
            List<TreeNode> rightTree = helper(i + 1, end);
            // link left and right sub tree to root(i)
            for (TreeNode lnode: leftTree) {
                for (TreeNode rnode: rightTree) {
                    TreeNode root = new TreeNode(i);
                    root.left = lnode;
                    root.right = rnode;
                    result.add(root);
                }
            }
        }

        return result;
    }
} 
```

### 源码分析

1.  异常处理，返回 None/NULL/null.
2.  遍历 start->end, 递归得到左子树和右子树。
3.  两重`for`循环将左右子树的所有可能组合添加至最终返回结果。

注意 DFS 辅助方法`helper`中左右子树及返回根节点的顺序。

### 复杂度分析

递归调用，一个合理的数组区间将生成新的左右子树，时间复杂度为指数级别，使用的临时空间最后都被加入到最终结果，空间复杂度(堆)近似为 $$O(1)$$, 栈上的空间较大。

## Reference

*   [Code Ganker: Unique Binary Search Trees II -- LeetCode](http://codeganker.blogspot.com/2014/04/unique-binary-search-trees-ii-leetcode.html)
*   [水中的鱼: [LeetCode] Unique Binary Search Trees II, Solution](http://fisherlei.blogspot.com/2013/03/leetcode-unique-binary-search-trees-ii.html)
*   [Accepted Iterative Java solution. - Leetcode Discuss](https://leetcode.com/discuss/22821/accepted-iterative-java-solution)
*   [Unique Binary Search Trees II 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/unique-binary-search-trees-ii/)