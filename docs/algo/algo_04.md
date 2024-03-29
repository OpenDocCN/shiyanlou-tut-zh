# 第 4 节 Integer Array - 整型数组（二）

## Product of Array Exclude Itself

## Question

*   lintcode: [(50) Product of Array Exclude Itself](http://www.lintcode.com/en/problem/product-of-array-exclude-itself/)
*   GeeksforGeeks: [A Product Array Puzzle - GeeksforGeeks](http://www.geeksforgeeks.org/a-product-array-puzzle/)

```
Given an integers array A.

Define B[i] = A[0] * ... * A[i-1] * A[i+1] * ... * A[n-1], calculate B WITHOUT divide operation.

Example
For A=[1, 2, 3], return [6, 3, 2]. 
```

## 题解 1 - 左右分治

根据题意，有 $$result[i] = left[i] \cdot right[i]$$, 其中 $$left[i] = \prod _{j = 0} ^{i - 1} A[j]$$, $$right[i] = \prod _{j = i + 1} ^{n - 1} A[j]$$. 即将最后的乘积分为两部分求解，首先求得左半部分的值，然后求得右半部分的值。最后将左右两半部分乘起来即为解。

### C++

```
class Solution {
public:
    /**
     * @param A: Given an integers array A
     * @return: A long long array B and B[i]= A[0] * ... * A[i-1] * A[i+1] * ... * A[n-1]
     */
    vector<long long> productExcludeItself(vector<int> &nums) {
        const int nums_size = nums.size();
        vector<long long> result(nums_size, 1);
        if (nums.empty() || nums_size == 1) {
            return result;
        }

        vector<long long> left(nums_size, 1);
        vector<long long> right(nums_size, 1);
        for (int i = 1; i != nums_size; ++i) {
            left[i] = left[i - 1] * nums[i - 1];
            right[nums_size - i - 1] = right[nums_size - i] * nums[nums_size - i];
        }
        for (int i = 0; i != nums_size; ++i) {
            result[i] = left[i] * right[i];
        }

        return result;
    }
}; 
```

### 源码分析

一次`for`循环求出左右部分的连乘积，下标的确定可使用简单例子辅助分析。

### 复杂度分析

两次`for`循环，时间复杂度 $$O(n)$$. 使用了左右两半部分辅助空间，空间复杂度 $$O(2n)$$.

## 题解 2 - 原地求积

题解 1 中使用了左右两个辅助数组，但是仔细瞅瞅其实可以发现完全可以在最终返回结果`result`基础上原地计算左右两半部分的积。

### C++

```
class Solution {
public:
    /**
     * @param A: Given an integers array A
     * @return: A long long array B and B[i]= A[0] * ... * A[i-1] * A[i+1] * ... * A[n-1]
     */
    vector<long long> productExcludeItself(vector<int> &nums) {
        const int nums_size = nums.size();
        vector<long long> result(nums_size, 1);

        // solve the left part first
        for (int i = 1; i < nums_size; ++i) {
            result[i] = result[i - 1] * nums[i - 1];
        }

        // solve the right part
        long long temp = 1;
        for (int i = nums_size - 1; i >= 0; --i) {
            result[i] *= temp;
            temp *= nums[i];
        }

        return result;
    }
}; 
```

### 源码分析

计算左半部分的递推式不用改，计算右半部分的乘积时由于会有左半部分值的干扰，故使用`temp`保存连乘的值。注意`temp`需要使用`long long`, 否则会溢出。

### 复杂度分析

时间复杂度同上，空间复杂度为 $$O(1)$$.

## Partition Array

## Question

*   lintcode: [(31) Partition Array](http://www.lintcode.com/en/problem/partition-array/)

### Problem Statement

Given an array `nums` of integers and an int `k`, partition the array (i.e move the elements in "nums") such that:

*   All elements < *k* are moved to the *left*
*   All elements >= *k* are moved to the *right*

Return the partitioning index, i.e the first index *i* nums[*i*] >= *k*.

#### Example

If nums = `[3,2,2,1]` and `k=2`, a valid answer is `1`.

#### Note

You should do really partition in array *nums* instead of just counting the numbers of integers smaller than k.

If all elements in *nums* are smaller than *k*, then return *nums.length*

#### Challenge

Can you partition the array in-place and in O(n)?

## 题解 1 - 自左向右

容易想到的一个办法是自左向右遍历，使用`right`保存大于等于 k 的索引，`i`则为当前遍历元素的索引，总是保持`i >= right`, 那么最后返回的`right`即为所求。

### C++

```
class Solution {
public:
    int partitionArray(vector<int> &nums, int k) {
        int right = 0;
        const int size = nums.size();
        for (int i = 0; i < size; ++i) {
            if (nums[i] < k && i >= right) {
                int temp = nums[i];
                nums[i] = nums[right];
                nums[right] = temp;
                ++right;
            }
        }

        return right;
    }
}; 
```

### 源码分析

自左向右遍历，遇到小于 k 的元素时即和`right`索引处元素交换，并自增`right`指向下一个元素，这样就能保证`right`之前的元素一定小于 k. 注意`if`判断条件中`i >= right`不能是`i > right`, 否则需要对特殊情况如全小于 k 时的考虑，而且即使考虑了这一特殊情况也可能存在其他 bug. 具体是什么 bug 呢？欢迎提出你的分析意见~

### 复杂度分析

遍历一次数组，时间复杂度最少为 $$O(n)$$, 可能需要一定次数的交换。

## 题解 2 - 两根指针

有了解过 [Quick Sort](http://algorithm.yuanbin.me/zh-hans/basics_sorting/quick_sort.html) 的做这道题自然是分分钟的事，使用左右两根指针 $$left, right$$ 分别代表小于、大于等于 k 的索引，左右同时开工，直至 $$left > right$$.

### C++

```
class Solution {
public:
    int partitionArray(vector<int> &nums, int k) {
        int left = 0, right = nums.size() - 1;
        while (left <= right) {
            while (left <= right && nums[left] < k) ++left;
            while (left <= right && nums[right] >= k) --right;
            if (left <= right) {
                int temp = nums[left];
                nums[left] = nums[right];
                nums[right] = temp;
                ++left;
                --right;
            }
        }

        return left;
    }
}; 
```

### 源码分析

大循环能正常进行的条件为 $$left <= right$$, 对于左边索引，向右搜索直到找到小于 k 的索引为止；对于右边索引，则向左搜索直到找到大于等于 k 的索引为止。注意在使用`while`循环时务必进行越界检查！

找到不满足条件的索引时即交换其值，并递增`left`, 递减`right`. 紧接着进行下一次循环。最后返回`left`即可，当`nums`为空时包含在`left = 0`之中，不必单独特殊考虑，所以应返回`left`而不是`right`.

### 复杂度分析

只需要对整个数组遍历一次，时间复杂度为 $$O(n)$$, 相比题解 1，题解 2 对全小于 k 的数组效率较高，元素交换次数较少。

## Reference

*   [Partition Array | 九章算法](http://www.jiuzhang.com/solutions/partition-array/)

## First Missing Positive

## Question

*   leetcode: [First Missing Positive | LeetCode OJ](https://leetcode.com/problems/first-missing-positive/)
*   lintcode: [(189) First Missing Positive](http://www.lintcode.com/en/problem/first-missing-positive/)

```
Given an unsorted integer array, find the first missing positive integer.

Example
Given [1,2,0] return 3, and [3,4,-1,1] return 2.

Challenge
Your algorithm should run in O(n) time and uses constant space. 
```

## 题解

容易想到的方案是先排序，然后遍历求得缺的最小整数。排序算法中常用的基于比较的方法时间复杂度的理论下界为 $$O(n \log n)$$, 不符题目要求。常见的能达到线性时间复杂度的排序算法有 [基数排序](http://zh.wikipedia.org/wiki/%E5%9F%BA%E6%95%B0%E6%8E%92%E5%BA%8F)，[计数排序](http://algorithm.yuanbin.me/zh-hans/basics_sorting/counting_sort.html) 和 [桶排序](http://algorithm.yuanbin.me/zh-hans/basics_sorting/bucket_sort.html)。

基数排序显然不太适合这道题，计数排序对元素落在一定区间且重复值较多的情况十分有效，且需要额外的 $$O(n)$$ 空间，对这道题不太合适。最后就只剩下桶排序了，桶排序通常需要按照一定规则将值放入桶中，一般需要额外的 $$O(n)$$ 空间，咋看一下似乎不太适合在这道题中使用，但是若能设定一定的规则原地交换原数组的值呢？这道题的难点就在于这种规则的设定。

设想我们对给定数组使用桶排序的思想排序，第一个桶放 1，第二个桶放 2，如果找不到相应的数，则相应的桶的值不变(可能为负值，也可能为其他值)。

那么怎么才能做到原地排序呢？即若 $$A[i] = x$$, 则将 x 放到它该去的地方 - $$A[x - 1] = x$$, 同时将原来 $$A[x - 1]$$ 地方的值交换给 $$A[i]$$.

排好序后遍历桶，如果不满足 $$f[i] = i + 1$$, 那么警察叔叔就是它了！如果都满足条件怎么办？那就返回给定数组大小再加 1 呗。

### C++

```
class Solution {
public:
    /**
     * @param A: a vector of integers
     * @return: an integer
     */
    int firstMissingPositive(vector<int> A) {
        const int size = A.size();

        for (int i = 0; i < size; ++i) {
            while (A[i] > 0 && A[i] <= size && \
                  (A[i] != i + 1) && (A[i] != A[A[i] - 1])) {
                int temp = A[A[i] - 1];
                A[A[i] - 1] = A[i];
                A[i] = temp;
            }
        }

        for (int i = 0; i < size; ++i) {
            if (A[i] != i + 1) {
                return i + 1;
            }
        }

        return size + 1;
    }
}; 
```

### 源码分析

核心代码为那几行交换，但是要很好地处理各种边界条件则要下一番功夫了，要能正常的交换，需满足以下几个条件：

1.  `A[i]` 为正数，负数和零都无法在桶中找到生存空间...
2.  `A[i] \leq size` 当前索引处的值不能比原数组容量大，大了的话也没用啊，肯定不是缺的第一个正数。
3.  `A[i] != i + 1`, 都满足条件了还交换个毛线，交换也是自身的值。
4.  `A[i] != A[A[i] - 1]`, 避免欲交换的值和自身相同，否则有重复值时会产生死循环。

如果满足以上四个条件就可以愉快地交换彼此了，使用`while`循环处理，此时`i`并不自增，直到将所有满足条件的索引处理完。

注意交换的写法，若写成

```
int temp = A[i];
A[i] = A[A[i] - 1];
A[A[i] - 1] = temp; 
```

这又是满满的 bug :( 因为在第三行中`A[i]`已不再是之前的值，第二行赋值时已经改变，故源码中的写法比较安全。

最后遍历桶排序后的数组，若在数组大小范围内找到不满足条件的解，直接返回，否则就意味着原数组给的元素都是从 1 开始的连续正整数，返回数组大小加 1 即可。

### 复杂度分析

「桶排序」需要遍历一次原数组，考虑到`while`循环也需要一定次数的遍历，故时间复杂度至少为 $$O(n)$$. 最后求索引值最多遍历一次排序后数组，时间复杂度最高为 $$O(n)$$, 用到了`temp`作为中间交换变量，空间复杂度为 $$O(1)$$.

## Reference

*   [Find First Missing Positive | N00tc0d3r](http://n00tc0d3r.blogspot.com/2013/03/find-first-missing-positive.html)
*   [LeetCode: First Missing Positive 解题报告 - Yu's Garden - 博客园](http://www.cnblogs.com/yuzhangcmu/p/4200096.html)
*   [First Missing Positive | 九章算法](http://www.jiuzhang.com/solutions/first-missing-positive/)

## 2 Sum

## Question

*   leetcode: [Two Sum | LeetCode OJ](https://leetcode.com/problems/two-sum/)
*   lintcode: [(56) 2 Sum](http://www.lintcode.com/en/problem/2-sum/)

### Problem Statement

Given an array of integers, find two numbers such that they add up to a specific target number.

The function `twoSum` should return *indices* of the two numbers such that they add up to the target, where index1 must be less than index2\. Please note that your returned answers (both index1 and index2) are **NOT** zero-based.

#### Example

numbers=`[2, 7, 11, 15]`, target=`9`

return `[1, 2]`

#### Note

You may assume that each input would have exactly one solution

#### Challenge

Either of the following solutions are acceptable:

*   O(n) Space, O(nlogn) Time
*   O(n) Space, O(n) Time

## 题解 1 - 哈希表

找两数之和是否为`target`, 如果是找数组中一个值为`target`该多好啊！遍历一次就知道了，我只想说，too naive... 难道要将数组中所有元素的两两组合都求出来与`target`比较吗？时间复杂度显然为 $$O(n²)$$, 显然不符题目要求。找一个数时直接遍历即可，那么可不可以将两个数之和转换为找一个数呢？我们先来看看两数之和为`target`所对应的判断条件—— $$x*i + x*j = target$$, 可进一步转化为 $$x*i = target - x*j$$, 其中 $$i$$ 和 $$j$$ 为数组中的下标。一段神奇的数学推理就将找两数之和转化为了找一个数是否在数组中了！可见数学是多么的重要...

基本思路有了，现在就来看看怎么实现，显然我们需要额外的空间(也就是哈希表)来保存已经处理过的 $$x_j$$(**注意这里并不能先初始化哈希表，否则无法排除两个相同的元素相加为 target 的情况**), 如果不满足等式条件，那么我们就往后遍历，并把之前的元素加入到哈希表中，如果`target`减去当前索引后的值在哈希表中找到了，那么就将哈希表中相应的索引返回，大功告成！

### Python

```
class Solution:
    """
    @param numbers : An array of Integer
    @param target : target = numbers[index1] + numbers[index2]
    @return : [index1 + 1, index2 + 1] (index1 < index2)
    """
    def twoSum(self, numbers, target):
        hashdict = {}
        for i, item in enumerate(numbers):
            if (target - item) in hashdict:
                return (hashdict[target - item] + 1, i + 1)
            hashdict[item] = i

        return (-1, -1) 
```

### C++

```
class Solution {
public:
    /*
     * @param numbers : An array of Integer
     * @param target : target = numbers[index1] + numbers[index2]
     * @return : [index1+1, index2+1] (index1 < index2)
     */
    vector<int> twoSum(vector<int> &nums, int target) {
        vector<int> result;
        const int length = nums.size();
        if (0 == length) {
            return result;
        }

        // first value, second index
        unordered_map<int, int> hash(length);
        for (int i = 0; i != length; ++i) {
            if (hash.find(target - nums[i]) != hash.end()) {
                result.push_back(hash[target - nums[i]]);
                result.push_back(i + 1);
                return result;
            } else {
                hash[nums[i]] = i + 1;
            }
        }

        return result;
    }
}; 
```

### Java

```
public class Solution {
    /*
     * @param numbers : An array of Integer
     * @param target : target = numbers[index1] + numbers[index2]
     * @return : [index1 + 1, index2 + 1] (index1 < index2)
     */
    public int[] twoSum(int[] numbers, int target) {
        if (numbers == null || numbers.length == 0) return new int[]{0, 0};

        Map<Integer, Integer> hashmap = new HashMap<Integer, Integer>();
        int index1 = 0, index2 = 0;
        for (int i = 0; i < numbers.length; i++) {
            if (hashmap.containsKey(target - numbers[i])) {
                index1 = hashmap.get(target - numbers[i]);
                index2 = i;
                return new int[]{1 + index1, 1 + index2};
            } else {
                hashmap.put(numbers[i], i);
            }
        }

        return new int[]{0, 0};
    }
} 
```

### 源码分析

1.  异常处理。
2.  使用 C++ 11 中的哈希表实现`unordered_map`映射值和索引。Python 中的`dict`就是天然的哈希表。
3.  找到满足条件的解就返回，找不到就加入哈希表中。注意题中要求返回索引值的含义。

### 复杂度分析

哈希表用了和数组等长的空间，空间复杂度为 $$O(n)$$, 遍历一次数组，时间复杂度为 $$O(n)$$.

## 题解 2 - 排序后使用两根指针

但凡可以用空间换时间的做法，往往也可以使用时间换空间。另外一个容易想到的思路就是先对数组排序，然后使用两根指针分别指向首尾元素，逐步向中间靠拢，直至找到满足条件的索引为止。

### C++

```
class Solution {
public:
    /*
     * @param numbers : An array of Integer
     * @param target : target = numbers[index1] + numbers[index2]
     * @return : [index1+1, index2+1] (index1 < index2)
     */
    vector<int> twoSum(vector<int> &nums, int target) {
        vector<int> result;
        const int length = nums.size();
        if (0 == length) {
            return result;
        }

        // first num, second is index
        vector<pair<int, int> > num_index(length);
        // map num value and index
        for (int i = 0; i != length; ++i) {
            num_index[i].first = nums[i];
            num_index[i].second = i + 1;
        }

        sort(num_index.begin(), num_index.end());
        int start = 0, end = length - 1;
        while (start < end) {
            if (num_index[start].first + num_index[end].first > target) {
                --end;
            } else if(num_index[start].first + num_index[end].first == target) {
                int min_index = min(num_index[start].second, num_index[end].second);
                int max_index = max(num_index[start].second, num_index[end].second);
                result.push_back(min_index);
                result.push_back(max_index);
                return result;
            } else {
                ++start;
            }
        }

        return result;
    }
}; 
```

### 源码分析

1.  异常处理。
2.  使用`length`保存数组的长度，避免反复调用`nums.size()`造成性能损失。
3.  使用`pair`组合排序前的值和索引，避免排序后找不到原有索引信息。
4.  使用标准库函数排序。
5.  两根指针指头尾，逐步靠拢。

### 复杂度分析

遍历一次原数组得到`pair`类型的新数组，时间复杂度为 $$O(n)$$, 空间复杂度也为 $$O(n)$$. 标准库中的排序方法时间复杂度近似为 $$O(n \log n)$$, 两根指针遍历数组时间复杂度为 $$O(n)$$.

## 3 Sum

## Question

*   leetcode: [3Sum | LeetCode OJ](https://leetcode.com/problems/3sum/)
*   lintcode: [(57) 3 Sum](http://www.lintcode.com/en/problem/3-sum/)

```
Given an array S of n integers, are there elements a, b, c in S such that a + b + c = 0?
Find all unique triplets in the array which gives the sum of zero.

Example
For example, given array S = {-1 0 1 2 -1 -4}, A solution set is:

(-1, 0, 1)
(-1, -1, 2)
Note
Elements in a triplet (a,b,c) must be in non-descending order. (ie, a ≤ b ≤ c)

The solution set must not contain duplicate triplets. 
```

## 题解 1 - 排序 + 哈希表 + 2 Sum

相比之前的 [2 Sum](http://algorithm.yuanbin.me/zh-hans/integer_array/2_sum.html), 3 Sum 又多加了一个数，按照之前 2 Sum 的分解为『1 Sum + 1 Sum』的思路，我们同样可以将 3 Sum 分解为『1 Sum + 2 Sum』的问题，具体就是首先对原数组排序，排序后选出第一个元素，随后在剩下的元素中使用 2 Sum 的解法。

### Python

```
class Solution:
    """
    @param numbersbers : Give an array numbersbers of n integer
    @return : Find all unique triplets in the array which gives the sum of zero.
    """
    def threeSum(self, numbers):
        triplets = []
        length = len(numbers)
        if length < 3:
            return triplets

        numbers.sort()
        for i in xrange(length):
            target = 0 - numbers[i]
            # 2 Sum
            hashmap = {}
            for j in xrange(i + 1, length):
                item_j = numbers[j]
                if (target - item_j) in hashmap:
                    triplet = [numbers[i], target - item_j, item_j]
                    if triplet not in triplets:
                        triplets.append(triplet)
                else:
                    hashmap[item_j] = j

        return triplets 
```

### 源码分析

1.  异常处理，对长度小于 3 的直接返回。
2.  排序输入数组，有助于提高效率和返回有序列表。
3.  循环遍历排序后数组，先取出一个元素，随后求得 2 Sum 中需要的目标数。
4.  由于本题中最后返回结果不能重复，在加入到最终返回值之前查重。

由于排序后的元素已经按照大小顺序排列，且在 2 Sum 中先遍历的元素较小，所以无需对列表内元素再排序。

### 复杂度分析

排序时间复杂度 $$O(n \log n)$$, 两重`for`循环，时间复杂度近似为 $$O(n²)$$，使用哈希表(字典)实现，空间复杂度为 $$O(n)$$.

目前这段源码为比较简易的实现，leetcode 上的运行时间为 500 + ms, 还有较大的优化空间，嗯，后续再进行优化。

### C++

```
class Solution {
public:
    vector<vector<int> > threeSum(vector<int> &num) 
    {
        vector<vector<int> > result;
        if (num.size() < 3) return result;

        int ans = 0;

        sort(num.begin(), num.end());

        for (int i = 0;i < num.size() - 2; ++i)
        {
            if (i > 0 && num[i] == num[i - 1])  
                continue;
            int j = i + 1;
            int k = num.size() - 1;

            while (j < k)
            {
                ans = num[i] + num[j] + num[k];

                if (ans == 0)
                {
                    result.push_back({num[i], num[j], num[k]});
                    ++j;
                    while (j < num.size() && num[j] == num[j - 1])
                        ++j;
                    --k;
                    while (k >= 0 && num[k] == num[k + 1])
                        --k;
                }
                else if (ans > 0) 
                    --k;
                else 
                    ++j;
            }
        }

        return result;
    }
}; 
```

### 源码分析

同 python 解法不同，没有使用 hash map

```
S = {-1 0 1 2 -1 -4}
排序后：
S = {-4 -1 -1 0 1 2}
      ↑  ↑        ↑
      i  j        k
         →        ←
i 每轮只走一步，j 和 k 根据 S[i]+S[j]+S[k]=ans 和 0 的关系进行移动，且 j 只向后走（即 S[j]只增大），k 只向前走（即 S[k]只减小）
如果 ans>0 说明 S[k]过大，k 向前移；如果 ans<0 说明 S[j]过小，j 向后移；ans==0 即为所求。
至于如何取到所有解，看代码即可理解，不再赘述。 
```

### 复杂度分析

外循环 i 走了 n 轮,每轮 j 和 k 一共走 n-i 步，所以时间复杂度为$$O(n²)$$。 最终运行时间为 52ms

## Reference

*   [3Sum | 九章算法](http://www.jiuzhang.com/solutions/3sum/)
*   [A simply Python version based on 2sum - O(n²) - Leetcode Discuss](https://leetcode.com/discuss/32455/a-simply-python-version-based-on-2sum-o-n-2)

## 3 Sum Closest

## Question

*   leetcode: [3Sum Closest | LeetCode OJ](https://leetcode.com/problems/3sum-closest/)
*   lintcode: [(59) 3 Sum Closest](http://www.lintcode.com/en/problem/3-sum-closest/)

```
Given an array S of n integers, find three integers in S such that the sum is closest to a given number, target. 
Return the sum of the three integers. You may assume that each input would have exactly one solution.

For example, given array S = {-1 2 1 -4}, and target = 1.
The sum that is closest to the target is 2\. (-1 + 2 + 1 = 2). 
```

## 题解 1 - 排序 + 2 Sum + 两根指针 + 优化过滤

和 3 Sum 的思路接近，首先对原数组排序，随后将 3 Sum 的题拆解为『1 Sum + 2 Sum』的题，对于 Closest 的题使用两根指针而不是哈希表的方法较为方便。对于有序数组来说，在查找 Cloest 的值时其实是有较大的优化空间的。

### Python

```
class Solution:
    """
    @param numbers: Give an array numbers of n integer
    @param target : An integer
    @return : return the sum of the three integers, the sum closest target.
    """
    def threeSumClosest(self, numbers, target):
        result = 2**31 - 1
        length = len(numbers)
        if length < 3:
            return result

        numbers.sort()
        larger_count = 0
        for i, item_i in enumerate(numbers):
            start = i + 1
            end = length - 1
            # optimization 1 - filter the smallest sum greater then target
            if start < end:
                sum3_smallest = numbers[start] + numbers[start + 1] + item_i
                if sum3_smallest > target:
                    larger_count += 1
                    if larger_count > 1:
                        return result

            while (start < end):
                sum3 = numbers[start] + numbers[end] + item_i
                if abs(sum3 - target) < abs(result - target):
                    result = sum3

                # optimization 2 - filter the sum3 closest to target
                sum_flag = 0
                if sum3 > target:
                    end -= 1
                    if sum_flag == -1:
                        break
                    sum_flag = 1
                elif sum3 < target:
                    start += 1
                    if sum_flag == 1:
                        break
                    sum_flag = -1
                else:
                    return result

        return result 
```

### 源码分析

1.  leetcode 上不让自己导入`sys`包，保险起见就初始化了`result`为还算较大的数，作为异常的返回值。
2.  对数组进行排序。
3.  依次遍历排序后的数组，取出一个元素`item_i`后即转化为『2 Sum Cloest』问题。『2 Sum Cloest』的起始元素索引为`i + 1`，之前的元素不能参与其中。
4.  优化一——由于已经对原数组排序，故遍历原数组时比较最小的三个元素和`target`值，若第二次大于`target`果断就此罢休，后面的值肯定越来越大。
5.  两根指针求『2 Sum Cloest』，比较`sum3`和`result`与`target`的差值的绝对值，更新`result`为较小的绝对值。
6.  再度对『2 Sum Cloest』进行优化，仍然利用有序数组的特点，若处于『一大一小』的临界值时就可以马上退出了，后面的元素与`target`之差的绝对值只会越来越大。

### 复杂度分析

对原数组排序，平均时间复杂度为 $$O(n \log n)$$, 两重`for`循环，由于有两处优化，故最坏的时间复杂度才是 $$O(n²)$$, 使用了`result`作为临时值保存最接近`target`的值，两处优化各使用了一个辅助变量，空间复杂度 $$O(1)$$.

### C++

```
class Solution {
public:
    int threeSumClosest(vector<int> &num, int target) 
    {
        if (num.size() <= 3) return accumulate(num.begin(), num.end(), 0);
        sort (num.begin(), num.end());

        int result = 0, n = num.size(), temp;
        result = num[0] + num[1] + num[2];
        for (int i = 0; i < n - 2; ++i)
        {
            int j = i + 1, k = n - 1;
            while (j < k)
            {
                temp = num[i] + num[j] + num[k];

                if (abs(target - result) > abs(target - temp))
                    result = temp;
                if (result == target)
                    return result;
                ( temp > target ) ? --k : ++j;
            }
        }
        return result;
    }
}; 
```

### 源码分析

和前面 3Sum 解法相似，同理使用 i,j,k 三个指针进行循环。<br> 区别在于 3sum 中的 target 为 0，这里新增一个变量用于比较哪组数据与 target 更为相近

### 复杂度分析

时间复杂度同理为$$O(n²)$$ 运行时间 16ms

## Reference

*   [3Sum Closest | 九章算法](http://www.jiuzhang.com/solutions/3sum-closest/)