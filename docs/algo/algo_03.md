# 第 3 节 Integer Array - 整型数组（一）

本章主要总结与整型数组相关的题。

## Remove Element

## Question

*   leetcode: [Remove Element | LeetCode OJ](https://leetcode.com/problems/remove-element/)
*   lintcode: [(172) Remove Element](http://www.lintcode.com/en/problem/remove-element/)

```
Given an array and a value, remove all occurrences of that value in place and return the new length.

The order of elements can be changed, and the elements after the new length don't matter.

Example
Given an array [0,4,4,0,0,2,4,4], value=4

return 4 and front four elements of the array is [0,0,0,2] 
```

## 题解 1 - 使用容器

入门题，返回删除指定元素后的数组长度，使用容器操作非常简单。以 lintcode 上给出的参数为例，遍历容器内元素，若元素值与给定删除值相等，删除当前元素并往后继续遍历。

### C++

```
class Solution {
public:
    /**
     *@param A: A list of integers
     *@param elem: An integer
     *@return: The new length after remove
     */
    int removeElement(vector<int> &A, int elem) {
        for (vector<int>::iterator iter = A.begin(); iter < A.end(); ++iter) {
            if (*iter == elem) {
                iter = A.erase(iter);
                --iter;
            }
        }

        return A.size();
    }
}; 
```

### 源码分析

注意在遍历容器内元素和指定欲删除值相等时，需要先自减`--iter`, 因为`for`循环会对`iter`自增，`A.erase()`删除当前元素值并返回指向下一个元素的指针，一增一减正好平衡。如果改用`while`循环，则需注意访问数组时是否越界。

### 复杂度分析

<!--- 没啥好分析的，遍历一次数组 $$O(n)$$. --> 由于 vector 每次 erase 的复杂度是$$O(n)$$，我们遍历整个数组，最坏情况下，每个元素都与要删除的目标元素相等，每次都要删除元素的复杂度高达$$O(n²)$$ 观察此方法会如此低效的原因，是因为我们一次只删除一个元素，导致很多没必要的元素交换移动，如果能够将要删除的元素集中处理，则可以大幅增加效率，见题解 2。

### 题解 2 - 两根指针

由于题中明确暗示元素的顺序可变，且新长度后的元素不用理会。我们可以使用两根指针分别往前往后遍历，头指针用于指示当前遍历的元素位置，尾指针则用于在当前元素与欲删除值相等时替换当前元素，两根指针相遇时返回尾指针索引——即删除元素后「新数组」的长度。

### C++

```
class Solution {
public:
    int removeElement(int A[], int n, int elem) {
        for (int i = 0; i < n; ++i) {
            if (A[i] == elem) {
                A[i] = A[n - 1];
                --i;
                --n;
            }
        }

        return n;
    }
}; 
```

### 源码分析

遍历当前数组，`A[i] == elem`时将数组「尾部(以 n 为长度时的尾部)」元素赋给当前遍历的元素。同时自减`i`和`n`，原因见题解 1 的分析。需要注意的是`n`在遍历过程中可能会变化。

### 复杂度分析

此方法只遍历一次数组，且每个循环的操作至多也不过仅是常数次，因此时间复杂度是$$O(n)$$。

## Reference

*   [Remove Element | 九章算法](http://www.jiuzhang.com/solutions/remove-element/)

## Zero Sum Subarray

## Question

*   lintcode: [(138) Subarray Sum](http://www.lintcode.com/en/problem/subarray-sum/)
*   GeeksforGeeks: [Find if there is a subarray with 0 sum - GeeksforGeeks](http://www.geeksforgeeks.org/find-if-there-is-a-subarray-with-0-sum/)

```
Given an integer array, find a subarray where the sum of numbers is zero.
Your code should return the index of the first number and the index of the last number.

Example
Given [-3, 1, 2, -3, 4], return [0, 2] or [1, 3].

Note
There is at least one subarray that it's sum equals to zero. 
```

## 题解 1 - 两重 for 循环

题目中仅要求返回一个子串(连续)中和为 0 的索引，而不必返回所有可能满足题意的解。最简单的想法是遍历所有子串，判断其和是否为 0，使用两重循环即可搞定，最坏情况下时间复杂度为 $$O(n²)$$, 这种方法显然是极其低效的，极有可能会出现 TLE. 下面就不浪费篇幅贴代码了。

## 题解 2 - 比较子串和(TLE)

两重 for 循环显然是我们不希望看到的解法，那么我们再来分析下题意，题目中的对象是分析子串和，那么我们先从常见的对数组求和出发，$$f(i) = \sum *{0} ^{i} nums[i]$$ 表示从数组下标 0 开始至下标 i 的和。子串和为 0，也就意味着存在不同的 $$i*1$$ 和 $$i*2$$ 使得 $$f(i*1) - f(i*2) = 0$$, 等价于 $$f(i*1) = f(i_2)$$. 思路很快就明晰了，使用一 vector 保存数组中从 0 开始到索引`i`的和，在将值 push 进 vector 之前先检查 vector 中是否已经存在，若存在则将相应索引加入最终结果并返回。

### C++

```
class Solution {
public:
    /**
     * @param nums: A list of integers
     * @return: A list of integers includes the index of the first number
     *          and the index of the last number
     */
    vector<int> subarraySum(vector<int> nums){
        vector<int> result;

        int curr_sum = 0;
        vector<int> sum_i;
        for (int i = 0; i != nums.size(); ++i) {
            curr_sum += nums[i];

            if (0 == curr_sum) {
                result.push_back(0);
                result.push_back(i);
                return result;
            }

            vector<int>::iterator iter = find(sum_i.begin(), sum_i.end(), curr_sum);
            if (iter != sum_i.end()) {
                result.push_back(iter - sum_i.begin() + 1);
                result.push_back(i);
                return result;
            }

            sum_i.push_back(curr_sum);
        }

        return result;
    }
}; 
```

### 源码分析

使用`curr_sum`保存到索引`i`处的累加和，`sum_i`保存不同索引处的和。执行`sum_i.push_back`之前先检查`curr_sum`是否为 0，再检查`curr_sum`是否已经存在于`sum_i`中。是不是觉得这种方法会比题解 1 好？错！时间复杂度是一样一样的！根本原因在于`find`操作的时间复杂度为线性。与这种方法类似的有哈希表实现，哈希表的查找在理想情况下可认为是 $$O(1)$$.

### 复杂度分析

最坏情况下 $$O(n²)$$, 实测和题解 1 中的方法运行时间几乎一致。

## 题解 3 - 哈希表

终于到了祭出万能方法时候了，题解 2 可以认为是哈希表的雏形，而哈希表利用空间换时间的思路争取到了宝贵的时间资源 :)

### C++

```
class Solution {
public:
    /**
     * @param nums: A list of integers
     * @return: A list of integers includes the index of the first number
     *          and the index of the last number
     */
    vector<int> subarraySum(vector<int> nums){
        vector<int> result;
        // curr_sum for the first item, index for the second item
        map<int, int> hash;
        hash[0] = 0;

        int curr_sum = 0;
        for (int i = 0; i != nums.size(); ++i) {
            curr_sum += nums[i];
            if (hash.find(curr_sum) != hash.end()) {
                result.push_back(hash[curr_sum]);
                result.push_back(i);
                return result;
            } else {
                hash[curr_sum] = i + 1;
            }
        }

        return result;
    }
}; 
```

### 源码分析

为了将`curr_sum == 0`的情况也考虑在内，初始化哈希表后即赋予 `<0, 0>`. 给 `hash`赋值时使用`i + 1`, `push_back`时则不必再加 1.

由于 C++ 中的`map`采用红黑树实现，故其并非真正的「哈希表」，C++ 11 中引入的`unordered_map`用作哈希表效率更高，实测可由 1300ms 降至 1000ms.

### 复杂度分析

遍历求和时间复杂度为 $$O(n)$$, 哈希表检查键值时间复杂度为 $$O(\log L)$$, 其中 $$L$$ 为哈希表长度。如果采用`unordered_map`实现，最坏情况下查找的时间复杂度为线性，最好为常数级别。

## 题解 4 - 排序

除了使用哈希表，我们还可使用排序的方法找到两个子串和相等的情况。这种方法的时间复杂度主要集中在排序方法的实现。由于除了记录子串和之外还需记录索引，故引入`pair`记录索引，最后排序时先按照`sum`值来排序，然后再按照索引值排序。如果需要自定义排序规则可参考[^sort*pair*second].

### C++

```
class Solution {
public:
    /**
     * @param nums: A list of integers
     * @return: A list of integers includes the index of the first number
     *          and the index of the last number
     */
    vector<int> subarraySum(vector<int> nums){
        vector<int> result;
        if (nums.empty()) {
            return result;
        }

        const int num_size = nums.size();
        vector<pair<int, int> > sum_index(num_size + 1);
        for (int i = 0; i != num_size; ++i) {
            sum_index[i + 1].first = sum_index[i].first + nums[i];
            sum_index[i + 1].second = i + 1;
        }

        sort(sum_index.begin(), sum_index.end());
        for (int i = 1; i < num_size + 1; ++i) {
            if (sum_index[i].first == sum_index[i - 1].first) {
                result.push_back(sum_index[i - 1].second);
                result.push_back(sum_index[i].second - 1);
                return result;
            }
        }

        return result;
    }
}; 
```

### 源码分析

没啥好分析的，注意好边界条件即可。这里采用了链表中常用的「dummy」节点方法，`pair`排序后即为我们需要的排序结果。这种排序的方法需要先求得所有子串和然后再排序，最后还需要遍历排序后的数组，效率自然是比不上哈希表。但是在某些情况下这种方法有一定优势。

### 复杂度分析

遍历求子串和，时间复杂度为 $$O(n)$$, 空间复杂度 $$O(n)$$. 排序时间复杂度近似 $$O(n \log n)$$, 遍历一次最坏情况下时间复杂度为 $$O(n)$$. 总的时间复杂度可近似为 $$O(n \log n)$$. 空间复杂度 $$O(n)$$.

## 扩展

这道题的要求是找到一个即可，但是要找出所有满足要求的解呢？Stackoverflow 上有这道延伸题的讨论[^stackoverflow].

另一道扩展题来自 Google 的面试题 - [Find subarray with given sum - GeeksforGeeks](http://www.geeksforgeeks.org/find-subarray-with-given-sum/).

## Reference

*   [^stackoverflow]: [algorithm - Zero sum SubArray - Stack Overflow](http://stackoverflow.com/questions/5534063/zero-sum-subarray)
*   [^sort*pair*second]: [c++ - How do I sort a vector of pairs based on the second element of the pair? - Stack Overflow](http://stackoverflow.com/questions/279854/how-do-i-sort-a-vector-of-pairs-based-on-the-second-element-of-the-pair)

## Subarray Sum K

## Question

*   GeeksforGeeks: [Find subarray with given sum - GeeksforGeeks](http://www.geeksforgeeks.org/find-subarray-with-given-sum/)

```
Given an nonnegative integer array, find a subarray where the sum of numbers is k.
Your code should return the index of the first number and the index of the last number.

Example
Given [1, 4, 20, 3, 10, 5], sum k = 33, return [2, 4]. 
```

## 题解 1 - 哈希表

题 [Zero Sum Subarray | Data Structure and Algorithm](http://algorithm.yuanbin.me/zh-hans/integer_array/zero_sum_subarray.html) 的升级版，这道题求子串和为 K 的索引。首先我们可以考虑使用时间复杂度相对较低的哈希表解决。前一道题的核心约束条件为 $$f(i*1) - f(i*2) = 0$$，这道题则变为 $$f(i*1) - f(i*2) = k$$

### C++

```
#include <iostream>
#include <vector>
#include <map>

using namespace std;

class Solution {
public:
    /**
     * @param nums: A list of integers
     * @return: A list of integers includes the index of the first number
     *          and the index of the last number
     */
    vector<int> subarraySum(vector<int> nums, int k){
        vector<int> result;
        // curr_sum for the first item, index for the second item
        // unordered_map<int, int> hash;
        map<int, int> hash;
        hash[0] = 0;

        int curr_sum = 0;
        for (int i = 0; i != nums.size(); ++i) {
            curr_sum += nums[i];
            if (hash.find(curr_sum - k) != hash.end()) {
                result.push_back(hash[curr_sum - k]);
                result.push_back(i);
                return result;
            } else {
                hash[curr_sum] = i + 1;
            }
        }

        return result;
    }
};

int main(int argc, char *argv[])
{
    int int_array1[] = {1, 4, 20, 3, 10, 5};
    int int_array2[] = {1, 4, 0, 0, 3, 10, 5};
    vector<int> vec_array1;
    vector<int> vec_array2;
    for (int i = 0; i != sizeof(int_array1) / sizeof(int); ++i) {
        vec_array1.push_back(int_array1[i]);
    }
    for (int i = 0; i != sizeof(int_array2) / sizeof(int); ++i) {
        vec_array2.push_back(int_array2[i]);
    }

    Solution solution;
    vector<int> result1 = solution.subarraySum(vec_array1, 33);
    vector<int> result2 = solution.subarraySum(vec_array2, 7);

    cout << "result1 = [" << result1[0] << " ," << result1[1] << "]" << endl;
    cout << "result2 = [" << result2[0] << " ," << result2[1] << "]" << endl;

    return 0;
} 
```

### 源码分析

与 Zero Sum Subarray 题的变化之处有两个地方，第一个是判断是否存在哈希表中时需要使用`hash.find(curr_sum - k)`, 最终返回结果使用`result.push_back(hash[curr_sum - k]);`而不是`result.push_back(hash[curr_sum]);`

### 复杂度分析

略，见 [Zero Sum Subarray | Data Structure and Algorithm](http://algorithm.yuanbin.me/zh-hans/integer_array/zero_sum_subarray.html)

## 题解 2 - 利用单调函数特性

不知道细心的你是否发现这道题的隐含条件——**nonnegative integer array**, 这也就意味着子串和函数 $$f(i)$$ 为「单调不减」函数。单调函数在数学中可是重点研究的对象，那么如何将这种单调性引入本题中呢？不妨设 $$i*2 > i*1$$, 题中的解等价于寻找 $$f(i*2) - f(i*1) = k$$, 则必有 $$f(i_2) \geq k$$.

我们首先来举个实际例子帮助分析，以整数数组 {1, 4, 20, 3, 10, 5} 为例，要求子串和为 33 的索引值。首先我们可以构建如下表所示的子串和 $$f(i)$$.

```
| $$f(i)$$ | 1 | 5 | 25 | 28 | 38 |
| -- | -- | -- | -- | -- | -- |
| $$i$$ | 0 | 1 | 2 | 3 | 4 |
```

要使部分子串和为 33，则要求的第二个索引值必大于等于 4，如果索引值再继续往后遍历，则所得的子串和必大于等于 38，进而可以推断出索引 0 一定不是解。那现在怎么办咧？当然是把它扔掉啊！第一个索引值往后递推，直至小于 33 时又往后递推第二个索引值，于是乎这种技巧又可以认为是「两根指针」。

### C++

```
#include <iostream>
#include <vector>
#include <map>

using namespace std;

class Solution {
public:
    /**
     * @param nums: A list of integers
     * @return: A list of integers includes the index of the first number
     *          and the index of the last number
     */
    vector<int> subarraySum2(vector<int> &nums, int k){
        vector<int> result;

        int left_index = 0, curr_sum = 0;
        for (int i = 0; i != nums.size(); ++i) {
            while (curr_sum > k) {
                curr_sum -= nums[left_index];
                ++left_index;
            }

            if (curr_sum == k) {
                result.push_back(left_index);
                result.push_back(i - 1);
                return result;
            }
            curr_sum += nums[i];
        }
        return result;
    }
};

int main(int argc, char *argv[])
{
    int int_array1[] = {1, 4, 20, 3, 10, 5};
    int int_array2[] = {1, 4, 0, 0, 3, 10, 5};
    vector<int> vec_array1;
    vector<int> vec_array2;
    for (int i = 0; i != sizeof(int_array1) / sizeof(int); ++i) {
        vec_array1.push_back(int_array1[i]);
    }
    for (int i = 0; i != sizeof(int_array2) / sizeof(int); ++i) {
        vec_array2.push_back(int_array2[i]);
    }

    Solution solution;
    vector<int> result1 = solution.subarraySum2(vec_array1, 33);
    vector<int> result2 = solution.subarraySum2(vec_array2, 7);

    cout << "result1 = [" << result1[0] << " ," << result1[1] << "]" << endl;
    cout << "result2 = [" << result2[0] << " ," << result2[1] << "]" << endl;

    return 0;
} 
```

### 源码分析

使用`for`循环, 在`curr_sum > k`时使用`while`递减`curr_sum`, 同时递增左边索引`left_index`, 最后累加`curr_sum`。如果顺序不对就会出现 bug, 原因在于判断子串和是否满足条件时在递增之后(谢谢 @glbrtchen 汇报 bug)。

### 复杂度分析

看似有两重循环，由于仅遍历一次数组，且索引最多挪动和数组等长的次数。故最终时间复杂度近似为 $$O(2n)$$, 空间复杂度为 $$O(1)$$.

## Reference

*   [Find subarray with given sum - GeeksforGeeks](http://www.geeksforgeeks.org/find-subarray-with-given-sum/)

## Subarray Sum Closest

## Question

*   lintcode: [(139) Subarray Sum Closest](http://www.lintcode.com/en/problem/subarray-sum-closest/)

```
Given an integer array, find a subarray with sum closest to zero.
Return the indexes of the first number and last number.

Example
Given [-3, 1, 1, -3, 5], return [0, 2], [1, 3], [1, 1], [2, 2] or [0, 4]

Challenge
O(nlogn) time 
```

## 题解

题 [Zero Sum Subarray | Data Structure and Algorithm](http://algorithm.yuanbin.me/zh-hans/integer_array/zero_sum_subarray.html) 的变形题，由于要求的子串和不一定，故哈希表的方法不再适用，使用解法 4 - 排序即可在 $$O(n \log n)$$ 内解决。具体步骤如下：

1.  首先遍历一次数组求得子串和。
2.  对子串和排序。
3.  逐个比较相邻两项差值的绝对值，返回差值绝对值最小的两项。

### C++

```
class Solution {
public:
    /**
     * @param nums: A list of integers
     * @return: A list of integers includes the index of the first number
     *          and the index of the last number
     */
    vector<int> subarraySumClosest(vector<int> nums){
        vector<int> result;
        if (nums.empty()) {
            return result;
        }

        const int num_size = nums.size();
        vector<pair<int, int> > sum_index(num_size + 1);

        for (int i = 0; i < num_size; ++i) {
            sum_index[i + 1].first = sum_index[i].first + nums[i];
            sum_index[i + 1].second = i + 1;
        }

        sort(sum_index.begin(), sum_index.end());

        int min_diff = INT_MAX;
        int closest_index = 1;
        for (int i = 1; i < num_size + 1; ++i) {
            int sum_diff = abs(sum_index[i].first - sum_index[i - 1].first);
            if (min_diff > sum_diff) {
                min_diff = sum_diff;
                closest_index = i;
            }
        }

        int left_index = min(sum_index[closest_index - 1].second,\
                             sum_index[closest_index].second);
        int right_index = -1 + max(sum_index[closest_index - 1].second,\
                                   sum_index[closest_index].second);
        result.push_back(left_index);
        result.push_back(right_index);
        return result;
    }
}; 
```

### 源码分析

为避免对单个子串和是否为最小情形的单独考虑，我们可以采取类似链表 dummy 节点的方法规避，简化代码实现。故初始化`sum_index`时需要`num_size + 1`个。这里为避免 vector 反复扩充空间降低运行效率，使用`resize`一步到位。`sum_index`即最后结果中`left_index`和`right_index`等边界可以结合简单例子分析确定。

### 复杂度分析

1.  遍历一次求得子串和时间复杂度为 $$O(n)$$, 空间复杂度为 $$O(n+1)$$.
2.  对子串和排序，平均时间复杂度为 $$O(n \log n)$$.
3.  遍历排序后的子串和数组，时间复杂度为 $$O(n)$$.

总的时间复杂度为 $$O(n \log n)$$, 空间复杂度为 $$O(n)$$.

## 扩展

*   [algorithm - How to find the subarray that has sum closest to zero or a certain value t in O(nlogn) - Stack Overflow](http://stackoverflow.com/questions/16388930/how-to-find-the-subarray-that-has-sum-closest-to-zero-or-a-certain-value-t-in-o)

## Recover Rotated Sorted Array

## Question

*   lintcode: [(39) Recover Rotated Sorted Array](http://lintcode.com/en/problem/recover-rotated-sorted-array/)

```
Given a rotated sorted array, recover it to sorted array in-place.

Example
[4, 5, 1, 2, 3] -> [1, 2, 3, 4, 5]

Challenge
In-place, O(1) extra space and O(n) time.

Clarification
What is rotated array:

    - For example, the orginal array is [1,2,3,4], The rotated array of it can be [1,2,3,4], [2,3,4,1], [3,4,1,2], [4,1,2,3] 
```

首先可以想到逐步移位，但是这种方法显然太浪费时间，不可取。下面介绍利器『三步翻转法』，以`[4, 5, 1, 2, 3]`为例。

1.  首先找到分割点`5`和`1`
2.  翻转前半部分`4, 5`为`5, 4`，后半部分`1, 2, 3`翻转为`3, 2, 1`。整个数组目前变为`[5, 4, 3, 2, 1]`
3.  最后整体翻转即可得`[1, 2, 3, 4, 5]`

由以上 3 个步骤可知其核心为『翻转』的 in-place 实现。使用两个指针，一个指头，一个指尾，使用 for 循环移位交换即可。

### Java

```
public class Solution {
    /**
     * @param nums: The rotated sorted array
     * @return: The recovered sorted array
     */
    public void recoverRotatedSortedArray(ArrayList<Integer> nums) {
        if (nums == null || nums.size() <= 1) {
            return;
        }

        int pos = 1;
        while (pos < nums.size()) { // find the break point
            if (nums.get(pos - 1) > nums.get(pos)) {
                break;
            }
            pos++;
        }
        myRotate(nums, 0, pos - 1);
        myRotate(nums, pos, nums.size() - 1);
        myRotate(nums, 0, nums.size() - 1);
    }

    private void myRotate(ArrayList<Integer> nums, int left, int right) { // in-place rotate
        while (left < right) {
            int temp = nums.get(left);
            nums.set(left, nums.get(right));
            nums.set(right, temp);
            left++;
            right--;
        }
    }
} 
```

### C++

```
/**
 * forked from
 * http://www.jiuzhang.com/solutions/recover-rotated-sorted-array/
 */
class Solution {
private:
    void reverse(vector<int> &nums, vector<int>::size_type start, vector<int>::size_type end) {
        for (vector<int>::size_type i = start, j = end; i < j; ++i, --j) {
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
        }
    }

public:
    void recoverRotatedSortedArray(vector<int> &nums) {
        for (vector<int>::size_type index = 0; index != nums.size() - 1; ++index) {
            if (nums[index] > nums[index + 1]) {
                reverse(nums, 0, index);
                reverse(nums, index + 1, nums.size() - 1);
                reverse(nums, 0, nums.size() - 1);

                return;
            }
        }
    }
}; 
```

### 源码分析

首先找到分割点，随后分三步调用翻转函数。简单起见可将`vector<int>::size_type`替换为`int`