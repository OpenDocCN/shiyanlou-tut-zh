# 第 22 节 Exhaustive Search - 穷竭搜索（一）

穷竭搜索又称暴力搜索，指代将所有可能性列出来，然后再在其中寻找满足题目条件的解。常用求解方法和工具有：

1.  递归函数
2.  栈
3.  队列
4.  深度优先搜索(DFS, Depth-First Search)，又常称为回溯法
5.  广度优先搜索(BFS, Breadth-First Search)

1, 2, 3 往往在深搜或者广搜中体现。

## DFS

DFS 通常从某个状态开始，根据特定的规则转移状态，直至无法转移(节点为空)，然后回退到之前一步状态，继续按照指定规则转移状态，直至遍历完所有状态。

回溯法包含了多类问题，模板类似。

排列组合模板->搜索问题(是否要排序，哪些情况要跳过)

使用回溯法的一般步骤：

1.  确定所给问题的解空间：首先应明确定义问题的解空间，解空间中至少包含问题的一个解。
2.  确定结点的扩展搜索规则
3.  以深度优先方式搜索解空间，并在搜索过程中用剪枝函数避免无效搜索。

### BFS

BFS 从某个状态开始，搜索**所有可以到达的状态**，转移顺序为『初始状态->只需一次转移就可到达的所有状态->只需两次转移就可到达的所有状态->...』，所以对于同一个状态，BFS 只搜索一次，故时间复杂度为 $$O(states \times transfer_methods)$$. BFS 通常配合队列一起使用，搜索时先将状态加入到队列中，然后从队列顶端不断取出状态，再把从该状态可转移到的状态中尚未访问过的部分加入队列，知道队列为空或已找到解。因此 BFS 适合用于『由近及远』的搜索，比较适合用于求解最短路径、最少操作之类的问题。

## Reference

*   《挑战程序设计竞赛》Chaper 2.1 p26 最基础的“穷竭搜索”
*   [Steven Skiena: Lecture15 - Backtracking](https://raw.githubusercontent.com/billryan/algorithm-exercise/master/shared-files/docs/lecture15-backtracking.pdf)
*   [全面解析回溯法：算法框架与问题求解 - 五岳 - 博客园](http://www.cnblogs.com/wuyuegb2312/p/3273337.html)
*   [五大常用算法之四：回溯法 - 红脸书生 - 博客园](http://www.cnblogs.com/steven_oyj/archive/2010/05/22/1741376.html)
*   [演算法筆記 - Backtracking](http://www.csie.ntnu.edu.tw/~u91029/Backtracking.html)

## Subsets - 子集

## Question

*   leetcode: [Subsets | LeetCode OJ](https://leetcode.com/problems/subsets/)
*   lintcode: [(17) Subsets](http://www.lintcode.com/en/problem/subsets/)

### Problem Statement

Given a set of distinct integers, *nums*, return all possible subsets.

#### Note:

*   Elements in a subset must be in non-descending order.
*   The solution set must not contain duplicate subsets.

For example,
If *nums* = `[1,2,3]`, a solution is:

```
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
] 
```

## 题解

子集类问题类似 Combination，以输入数组`[1, 2, 3]`分析，根据题意，最终返回结果中子集类的元素应该按照升序排列，故首先需要对原数组进行排序。题目的第二点要求是子集不能重复，至此原题即转化为数学中的组合问题。我们首先尝试使用 DFS 进行求解，大致步骤如下：

1.  `[1] -> [1, 2] -> [1, 2, 3]`
2.  `[2] -> [2, 3]`
3.  `[3]`

将上述过程转化为代码即为对数组遍历，每一轮都保存之前的结果并将其依次加入到最终返回结果中。

### Iterative

### Python

```
class Solution:
    """
    @param S: The set of numbers.
    @return: A list of lists. See example.
    """
    def subsets(self, S):
        if not S:
            return []
        ret = []
        S.sort()
        n = len(S)
        # 000 -> []
        # 001 -> [1]
        # 010 -> [2]
        # ...
        # 111 -> [1, 2, 3]
        for i in xrange(2**n):
            tmp = []
            for j in xrange(n):
                if i & (1 << j):
                    tmp.append(S[j])
            ret.append(tmp)
        return ret 
```

### 源码分析

利用类似`bit map`的原理， 将 0 ~ $$2^n - 1$$个数值 map 到每个 index 上，如果 index 数值为 1，就将该 number 加入。比如输入是`[1 ,2 ,3]`, 那么当`i = 0`时，`0`也就是`000`， 那么`000 -> []`； 当`i = 1`时， `001 -> [1]`; 直到`i = 7`, `111 -> [1, 2, 3]`.

### Recursive

### Python

```
class Solution:
    # @param {integer[]} nums
    # @return {integer[][]}
    def subsets(self, nums):
        if nums is None:
            return []

        result = []
        nums.sort()
        self.dfs(nums, 0, [], result)
        return result

    def dfs(self, nums, pos, list_temp, ret):
        # append new object with []
        ret.append([] + list_temp)

        for i in xrange(pos, len(nums)):
            list_temp.append(nums[i])
            self.dfs(nums, i + 1, list_temp, ret)
            list_temp.pop() 
```

#### less code style

```
class Solution:
    """
    @param S: The set of numbers.
    @return: A list of lists. See example.
    """
    def subsets(self, S):
        ret = []
        self.helper(sorted(S), ret, [])
        return ret

    def helper(self, vals, ret, tmp):
        ret.append(tmp[:])
        for i, val in enumerate(vals):
            self.helper(vals[i + 1:], ret, tmp + [val]) 
```

### C++

```
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int> > result;
        if (nums.empty()) return result;

        sort(nums.begin(), nums.end());
        vector<int> list;
        dfs(nums, 0, list, result);

        return result;
    }

private:
    void dfs(vector<int>& nums, int pos, vector<int> &list,
             vector<vector<int> > &ret) {

        ret.push_back(list);

        for (int i = pos; i < nums.size(); ++i) {
            list.push_back(nums[i]);
            dfs(nums, i + 1, list, ret);
            list.pop_back();
        }
    }
}; 
```

### Java

```
public class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        List<Integer> list = new ArrayList<Integer>();
        if (nums == null || nums.length == 0) {
            return result;
        }

        Arrays.sort(nums);
        dfs(nums, 0, list, result);

        return result;
    }

    private void dfs(int[] nums, int pos, List<Integer> list,
                     List<List<Integer>> ret) {

        // add temp result first
        ret.add(new ArrayList<Integer>(list));

        for (int i = pos; i < nums.length; i++) {
            list.add(nums[i]);
            dfs(nums, i + 1, list, ret);
            list.remove(list.size() - 1);
        }
    }
} 
```

### 源码分析

Java 和 Python 的代码中在将临时 list 添加到最终结果时新生成了对象，(Python 使用`[] +`), 否则最终返回结果将随着`list` 的变化而变化。

**Notice: backTrack(num, i + 1, list, ret);中的『i + 1』不可误写为『pos + 1』，因为`pos`用于每次大的循环，`i`用于内循环，第一次写 subsets 的时候在这坑了很久... :(**

回溯法可用图示和函数运行的堆栈图来理解，强烈建议**使用图形和递归的思想**分析，以数组`[1, 2, 3]`进行分析。下图所示为`list`及`result`动态变化的过程，箭头向下表示`list.add`及`result.add`操作，箭头向上表示`list.remove`操作。

![Subsets 运行递归调用图](img/3b13a7cde2c67a19dbc1d10ec6aa7a6b.jpg)

### 复杂度分析

对原有数组排序，时间复杂度近似为 $$O(n \log n)$$. 状态数为所有可能的组合数 $$O(2^n)$$, 生成每个状态所需的时间复杂度近似为 $$O(1)$$, 如`[1] -> [1, 2]`, 故总的时间复杂度近似为 $$O(2^n)$$.

使用了临时空间`list`保存中间结果，`list` 最大长度为数组长度，故空间复杂度近似为 $$O(n)$$.

## Reference

*   [[NineChap 1.2] Permutation - Woodstock Blog](http://okckd.github.io/blog/2014/06/12/NineChap-Permutation/)
*   [九章算法 - subsets 模板](http://www.jiuzhang.com/solutions/subsets/)
*   [LeetCode: Subsets 解题报告 - Yu's Garden - 博客园](http://www.cnblogs.com/yuzhangcmu/p/4211815.html)

## Unique Subsets

## Question

*   leetcode: [Subsets II | LeetCode OJ](https://leetcode.com/problems/subsets-ii/)
*   lintcode: [(18) Unique Subsets](http://www.lintcode.com/en/problem/unique-subsets/)

### Problem Statement

Given a list of numbers that may has duplicate numbers, return all possible subsets.

#### Example

If ***S*** = `[1,2,2]`, a solution is:

```
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
] 
```

#### Note

Each element in a subset must be in **non-descending **order. The ordering between two subsets is free. The solution set must not contain duplicate subsets.

## 题解

此题在上一题的基础上加了有重复元素的情况，因此需要对回溯函数进行一定的剪枝，对于排列组合的模板程序，剪枝通常可以从两个地方出发，一是在返回结果`result.add`之前进行剪枝，另一个则是在`list.add`处剪枝，具体使用哪一种需要视情况而定，哪种简单就选谁。

由于此题所给数组不一定有序，故首先需要排序。有重复元素对最终结果的影响在于重复元素最多只能出现`n`次(重复个数为 n 时)。具体分析过程如下(此分析过程改编自 [九章算法](http://www.jiuzhang.com))。

以 $$[1, 2*1, 2*2]$$ 为例，若不考虑重复，组合有 $$[], [1], [1, 2*1], [1, 2*1, 2*2], [1, 2*2], [2*1], [2*1, 2*2], [2*2]$$. 其中重复的有 $$[1, 2*2], [2*2]$$. 从中我们可以看出只能从重复元素的第一个持续往下添加到列表中，而不能取第二个或之后的重复元素。参考上一题 Subsets 的模板，能代表「重复元素的第一个」即为 for 循环中的`pos`变量，`i == pos`时，`i`处所代表的变量即为某一层遍历中得「第一个元素」，因此去重时只需判断`i != pos && s[i] == s[i - 1]`(不是 i + 1, 可能索引越界，而 i 不等于 pos 已经能保证 i >= 1).

### C++

```
class Solution {
public:
    /**
     * @param S: A set of numbers.
     * @return: A list of lists. All valid subsets.
     */
    vector<vector<int> > subsetsWithDup(const vector<int> &S) {
        vector<vector<int> > result;
        if (S.empty()) {
            return result;
        }

        vector<int> list;
        vector<int> source(S);
        sort(source.begin(), source.end());
        backtrack(result, list, source, 0);

        return result;
    }

private:
    void backtrack(vector<vector<int> > &ret, vector<int> &list,
              vector<int> &s, int pos) {

        ret.push_back(list);

        for (int i = pos; i != s.size(); ++i) {
            if (i != pos && s[i] == s[i - 1]) {
                continue;
            }
            list.push_back(s[i]);
            backtrack(ret, list, s, i + 1);
            list.pop_back();
        }
    }
}; 
```

### Java

```
class Solution {
    /**
     * @param S: A set of numbers.
     * @return: A list of lists. All valid subsets.
     */
    public ArrayList<ArrayList<Integer>> subsetsWithDup(ArrayList<Integer> S) {
        ArrayList<ArrayList<Integer>> result = new ArrayList<ArrayList<Integer>>();
        if (S == null) return result;
        // 
        Collections.sort(S);
        List<Integer> list = new ArrayList<Integer>();
        dfs(S, 0, list, result);
        return result;
    }

    private void dfs(ArrayList<Integer> S, int pos, List<Integer> list, 
                     ArrayList<ArrayList<Integer>> result) {

        result.add(new ArrayList<Integer>(list));
        for (int i = pos; i < S.size(); i++) {
            // exlude duplicate
            if (i != pos && S.get(i) == S.get(i - 1)) {
                continue;
            }
            list.add(S.get(i));
            dfs(S, i + 1, list, result);
            list.remove(list.size() - 1);
        }
    }
} 
```

### 源码分析

相比前一道题多了去重的判断。

### 复杂度分析

和前一道题差不多，最坏情况下时间复杂度为 $$2^n$$. 空间复杂度为 $$O(n)$$.

## Reference

*   [Subsets II | 九章算法](http://www.jiuzhang.com/solutions/subsets-ii/)

## Permutations

## Question

*   leetcode: [Permutations | LeetCode OJ](https://leetcode.com/problems/permutations/)
*   lintcode: [(15) Permutations](http://www.lintcode.com/en/problem/permutations/)

### Problem Statement

Given a list of numbers, return all possible permutations.

#### Example

For nums = `[1,2,3]`, the permutations are:

```
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
] 
```

#### Challenge

Do it without recursion.

## 题解 1 - Recursion(using subsets template)

排列常见的有数字全排列，字符串排列等。

使用之前 [Subsets](http://algorithm.yuanbin.me/zh-hans/exhaustive_search/subsets.html) 的模板，但是在取结果时只能取`list.size() == nums.size()`的解，且在添加 list 元素的时候需要注意除重以满足全排列的要求。此题假设前提为输入数据中无重复元素。

### Python

```
class Solution:
    """
    @param nums: A list of Integers.
    @return: A list of permutations.
    """
    def permute(self, nums):
        alist = []
        result = [];
        if not nums:
            return result

        self.helper(nums, alist, result)

        return result

    def helper(self, nums, alist, ret):
        if len(alist) == len(nums):
            # new object
            ret.append([] + alist)
            return

        for i, item in enumerate(nums):
            if item not in alist:
                alist.append(item)
                self.helper(nums, alist, ret)
                alist.pop() 
```

### C++

```
class Solution {
public:
    /**
     * @param nums: A list of integers.
     * @return: A list of permutations.
     */
    vector<vector<int> > permute(vector<int> nums) {
        vector<vector<int> > result;
        if (nums.empty()) {
            return result;
        }

        vector<int> list;
        backTrack(result, list, nums);

        return result;
    }

private:
    void backTrack(vector<vector<int> > &result, vector<int> &list, \
                   vector<int> &nums) {
        if (list.size() == nums.size()) {
            result.push_back(list);
            return;
        }

        for (int i = 0; i != nums.size(); ++i) {
            // remove the element belongs to list
            if (find(list.begin(), list.end(), nums[i]) != list.end()) {
                continue;
            }
            list.push_back(nums[i]);
            backTrack(result, list, nums);
            list.pop_back();
        }
    }
}; 
```

### Java

```
public class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        if (nums == null || nums.length == 0) return result;

        List<Integer> list = new ArrayList<Integer>();
        dfs(nums, list, result);

        return result;
    }

    private void dfs(int[] nums, List<Integer> list, List<List<Integer>> result) {
        if (list.size() == nums.length) {
            result.add(new ArrayList<Integer>(list));
            return;
        }

        for (int i = 0; i < nums.length; i++) {
            if (list.contains(nums[i])) continue;
            list.add(nums[i]);
            dfs(nums, list, result);
            list.remove(list.size() - 1);
        }
    }
} 
```

### 源码分析

在除重时使用了标准库`find`(不可使用时间复杂度更低的`binary_search`，因为`list`中元素不一定有序)，时间复杂度为 $$O(N)$$, 也可使用`hashmap`记录`nums`中每个元素是否被添加到`list`中，这样一来空间复杂度为 $$O(N)$$, 查找的时间复杂度为 $$O(1)$$.

在`list.size() == nums.size()`时，已经找到需要的解，及时`return`避免后面不必要的`for`循环调用开销。

使用回溯法解题的**关键在于如何确定正确解及排除不符条件的解(剪枝)**。

### 复杂度分析

以状态数来分析，最终全排列个数应为 $$n!$$, 每个节点被遍历的次数为 $$(n-1)!$$, 故节点共被遍历的状态数为 $$O(n!)$$, 此为时间复杂度的下界，因为这里只算了合法条件下的遍历状态数。若不对 list 中是否包含 nums[i] 进行检查，则总的状态数应为 $$n^n$$ 种。

由于最终的排列结果中每个列表的长度都为 n, 各列表的相同元素并不共享，故时间复杂度的下界为 $$O(n \cdot n!)$$, 上界为 $$n \cdot n^n$$. 实测`helper`中 for 循环的遍历次数在 $$O(2n \cdot n!)$$ 以下，注意这里的时间复杂度并不考虑查找列表里是否包含重复元素。

## 题解 2 - Recursion

与题解 1 基于 subsets 的模板不同，这里我们直接从全排列的数学定义本身出发，要求给定数组的全排列，可将其模拟为某个袋子里有编号为 1 到 n 的球，将其放入 n 个不同的盒子怎么放？基本思路就是从袋子里逐个拿球放入盒子，直到袋子里的球拿完为止，拿完时即为一种放法。

### Python

```
class Solution:
    # @param {integer[]} nums
    # @return {integer[][]}
    def permute(self, nums):
        if nums is None:
            return [[]]
        elif len(nums) <= 1:
            return [nums]

        result = []
        for i, item in enumerate(nums):
            for p in self.permute(nums[:i] + nums[i + 1:]):
                result.append(p + [item])

        return result

class Solution2:
    # 类似 subset 的模版
    def permute(self, nums):
        if not nums:
            return []
        res = []
        self.helper(sorted(nums), res, [])
        return res

    def helper(self, nums, res, tmp):
        if not nums:
            res.append(tmp[:])
            return
        for i, num in enumerate(nums, 1):
            self.helper(nums[:i] + nums[i + 1:], res, tmp + [num]) 
```

### C++

```
class Solution {
public:
    /**
     * @param nums: A list of integers.
     * @return: A list of permutations.
     */
    vector<vector<int> > permute(vector<int>& nums) {
        vector<vector<int> > result;

        if (nums.size() == 1) {
            result.push_back(nums);
            return result;
        }

        for (int i = 0; i < nums.size(); ++i) {
            vector<int> nums_new = nums;
            nums_new.erase(nums_new.begin() + i);

            vector<vector<int> > res_tmp = permute(nums_new);
            for (int j = 0; j < res_tmp.size(); ++j) {
                vector<int> temp = res_tmp[j];
                temp.push_back(nums[i]);
                result.push_back(temp);
            }
        }

        return result;
    }
}; 
```

### Java

```
public class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        List<Integer> numsList = new ArrayList<Integer>();

        if (nums == null) {
            return result;
        } else {
            // convert int[] to List<Integer>
            for (int item : nums) numsList.add(item);
        }

        if (nums.length <= 1) {
            result.add(numsList);
            return result;
        }

        for (int i = 0; i < nums.length; i++) {
            int[] numsNew = new int[nums.length - 1];
            System.arraycopy(nums, 0, numsNew, 0, i);
            System.arraycopy(nums, i + 1, numsNew, i, nums.length - i - 1);

            List<List<Integer>> resTemp = permute(numsNew);
            for (List<Integer> temp : resTemp) {
                temp.add(nums[i]);
                result.add(temp);
            }
        }

        return result;
    }
} 
```

### 源码分析

Python 中使用`len()`时需要防止`None`, 递归终止条件为数组中仅剩一个元素或者为空，否则遍历`nums`数组，取出第`i`个元素并将其加入至最终结果。`nums[:i] + nums[i + 1:]`即为去掉第`i`个元素后的新列表。

Java 中 ArrayList 和 List 的类型转换需要特别注意。

### 复杂度分析

由于取的结果都是最终结果，无需去重判断，故时间复杂度为 $$O(n!)$$, 但是由于`nums[:i] + nums[i + 1:]`会产生新的列表，实际运行会比第一种方法慢不少。

## 题解 3 - Iteration

递归版的程序比较简单，咱们来个迭代的实现。非递归版的实现也有好几种，这里基于 C++ STL 中`next_permutation`的字典序实现方法。参考 Wikipedia 上的字典序算法，大致步骤如下：

1.  从后往前寻找索引满足 `a[k] < a[k + 1]`, 如果此条件不满足，则说明已遍历到最后一个。
2.  从后往前遍历，找到第一个比`a[k]`大的数`a[l]`, 即`a[k] < a[l]`.
3.  交换`a[k]`与`a[l]`.
4.  反转`k + 1 ~ n`之间的元素。

### Python

```
class Solution:
    # @param {integer[]} nums
    # @return {integer[][]}
    def permute(self, nums):
        if nums is None:
            return [[]]
        elif len(nums) <= 1:
            return [nums]

        # sort nums first
        nums.sort()

        result = []
        while True:
            result.append([] + nums)
            # step1: find nums[i] < nums[i + 1], Loop backwards
            i = 0
            for i in xrange(len(nums) - 2, -1, -1):
                if nums[i] < nums[i + 1]:
                    break
                elif i == 0:
                    return result
            # step2: find nums[i] < nums[j], Loop backwards
            j = 0
            for j in xrange(len(nums) - 1, i, -1):
                if nums[i] < nums[j]:
                    break
            # step3: swap betwenn nums[i] and nums[j]
            nums[i], nums[j] = nums[j], nums[i]
            # step4: reverse between [i + 1, n - 1]
            nums[i + 1:len(nums)] = nums[len(nums) - 1:i:-1]

        return result 
```

### C++

```
class Solution {
public:
    /**
     * @param nums: A list of integers.
     * @return: A list of permutations.
     */
    vector<vector<int> > permute(vector<int>& nums) {
        vector<vector<int> > result;
        if (nums.empty() || nums.size() <= 1) {
            result.push_back(nums);
            return result;
        }

        // sort nums first
        sort(nums.begin(), nums.end());
        for (;;) {
            result.push_back(nums);

            // step1: find nums[i] < nums[i + 1]
            int i = 0;
            for (i = nums.size() - 2; i >= 0; --i) {
                if (nums[i] < nums[i + 1]) {
                    break;
                } else if (0 == i) {
                    return result;
                }
            }

            // step2: find nums[i] < nums[j]
            int j = 0;
            for (j = nums.size() - 1; j > i; --j) {
                if (nums[i] < nums[j]) break;
            }

            // step3: swap betwenn nums[i] and nums[j]
            int temp = nums[j];
            nums[j] = nums[i];
            nums[i] = temp;

            // step4: reverse between [i + 1, n - 1]
            reverse(nums, i + 1, nums.size() - 1);
        }
        return result;
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

### Java - Array

```
class Solution {
    /**
     * @param nums: A list of integers.
     * @return: A list of permutations.
     */
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        if (nums == null || nums.length == 0) return result;

        // deep copy(do not change nums)
        int[] perm = Arrays.copyOf(nums, nums.length);
        // sort first!!!
        Arrays.sort(perm);

        while (true) {
            // step0: add perm into result
            List<Integer> tempList = new ArrayList<Integer>();
            for (int i : perm) tempList.add(i);
            result.add(tempList);

            // step1: search the first perm[k] < perm[k+1] backward
            int k = -1;
            for (int i = perm.length - 2; i >= 0; i--) {
                if (perm[i] < perm[i + 1]) {
                    k = i;
                    break;
                }
            }
            // if current rank is the largest, exit while loop
            if (k == -1) break;

            // step2: search the first perm[k] < perm[l] backward
            int l = perm.length - 1;
            while (l > k && perm[l] <= perm[k]) l--;

            // step3: swap perm[k] with perm[l]
            int temp = perm[k];
            perm[k] = perm[l];
            perm[l] = temp;

            // step4: reverse between k+1 and perm.length-1;
            reverse(perm, k + 1, perm.length - 1);
        }

        return result;
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

### Java - List

```
class Solution {
    /**
     * @param nums: A list of integers.
     * @return: A list of permutations.
     */
    public ArrayList<ArrayList<Integer>> permute(ArrayList<Integer> nums) {
        ArrayList<ArrayList<Integer>> result = new ArrayList<ArrayList<Integer>>();
        if (nums == null || nums.size() == 0) return result;

        // deep copy(do not change nums)
        List<Integer> perm = new ArrayList<Integer>(nums);
        // sort first!!!
        Collections.sort(perm);

        while (true) {
            // step0: add perm into result
            result.add(new ArrayList<Integer>(perm));

            // step1: search the first num[k] < num[k+1] backward
            int k = -1;
            for (int i = perm.size() - 2; i >= 0; i--) {
                if (perm.get(i) < perm.get(i + 1)) {
                    k = i;
                    break;
                }
            }
            // if current rank is the largest, exit while loop
            if (k == -1) break;

            // step2: search the first perm[k] < perm[l] backward
            int l = perm.size() - 1;
            while (l > k && perm.get(l) <= perm.get(k)) l--;

            // step3: swap perm[k] with perm[l]
            Collections.swap(perm, k, l);

            // step4: reverse between k+1 and perm.size()-1;
            reverse(perm, k + 1, perm.size() - 1);
        }

        return result;
    }

    private void reverse(List<Integer> nums, int lb, int ub) {
        for (int i = lb, j = ub; i < j; i++, j--) {
            Collections.swap(nums, i, j);
        }
    }
} 
```

### 源码分析

注意好字典序算法的步骤即可，对于 Java 来说其实可以首先将数组转化为 List, 相应的方法多一些。吐槽下 Lintcode 上的接口设计，总是见到一长串的`ArrayList`, 个人觉得采用 Leetcode 上的`List`更灵活（代码更短，哈哈），不知道 Lintcode 那样的接口设计有什么其他考虑吗？

### 复杂度分析

除了将 $$n!$$ 个元素添加至最终结果外，首先对元素排序，时间复杂度近似为 $$O(n \log n)$$, 反转操作近似为 $$O(n)$$, 故总的时间复杂度为 $$O(n!)$$. 除了保存结果的`result`外，其他空间可忽略不计，所以此题用生成器来实现较为高效，扩展题可见底下的 Python itertools 中的实现，从 n 个元素中选出 m 个进行全排列。

## Reference

*   [Permutation Generation](https://raw.githubusercontent.com/billryan/algorithm-exercise/master/shared-files/docs/permutation_generation.pdf) - Robert Sedgewick 的大作，总结了诸多 Permutation 的产生方法。
*   [Next lexicographical permutation algorithm](http://www.nayuki.io/page/next-lexicographical-permutation-algorithm) - 此题非递归方法更为详细的解释。
*   [Permutation - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Permutation#Generation_in_lexicographic_order) - 字典序实现。
*   [Programming Interview Questions 11: All Permutations of String | Arden DertatArden Dertat](http://www.ardendertat.com/2011/10/28/programming-interview-questions-11-all-permutations-of-string/)
*   [algorithm - complexity of recursive string permutation function - Stack Overflow](http://stackoverflow.com/questions/5363619/complexity-of-recursive-string-permutation-function)
*   [[leetcode]Permutations @ Python - 南郭子綦 - 博客园](http://www.cnblogs.com/zuoyuan/p/3758816.html)
*   [[leetcode] permutations 的讨论 - tuantuanls 的专栏 - 博客频道 - CSDN.NET](http://blog.csdn.net/tuantuanls/article/details/8717262)
*   [非递归排列算法（Permutation Generation）](http://arieshout.me/2012/04/non-recursive-permutation-generation.html)
*   [闲谈 permutations | HelloYou](http://helloyou2012.me/?p=133)
*   [9.7\. itertools — Functions creating iterators for efficient looping — Python 2.7.10 documentation](https://docs.python.org/2/library/itertools.html#itertools.permutations)

## Permutations II

## Question

*   leetcode: [Permutations II | LeetCode OJ](https://leetcode.com/problems/permutations-ii/)
*   lintcode: [(16) Permutations II](http://www.lintcode.com/en/problem/permutations-ii/)

### Problem Statement

Given a list of numbers with duplicate number in it. Find all **unique** permutations.

#### Example

For numbers `[1,2,2]` the unique permutations are:

```
[

    [1,2,2],

    [2,1,2],

    [2,2,1]

] 
```

#### Challenge

Do it without recursion.

## 题解 1 - backtracking

在上题的基础上进行剪枝，剪枝的过程和 [Unique Subsets](http://algorithm.yuanbin.me/zh-hans/exhaustive_search/unique_subsets.html) 一题极为相似。为了便于分析，我们可以先分析简单的例子，以 $$[1, 2*1, 2*2]$$ 为例。按照上题 Permutations 的解法，我们可以得到如下全排列。

1.  $$[1, 2*1, 2*2]$$
2.  $$[1, 2*2, 2*1]$$
3.  $$[2*1, 1, 2*2]$$
4.  $$[2*1, 2*2, 1]$$
5.  $$[2*2, 1, 2*1]$$
6.  $$[2*2, 2*1, 1]$$

从以上结果我们注意到`1`和`2`重复，`5`和`3`重复，`6`和`4`重复，从重复的解我们可以发现其共同特征均是第二个 $$2*2$$ 在前，而第一个 $$2*1$$ 在后，因此我们的**剪枝方法为：对于有相同的元素来说，我们只取不重复的一次。**嗯，这样说还是有点模糊，下面以 $$[1, 2*1, 2*2]$$ 和 $$[1, 2*2, 2*1]$$ 进行说明。

首先可以确定 $$[1, 2*1, 2*2]$$ 是我们要的一个解，此时`list`为 $$[1, 2*1, 2*2]$$, 经过两次`list.pop_back()`之后，`list`为 $$[1]$$, 如果不进行剪枝，那么接下来要加入`list`的将为 $$2*2$$, 那么我们剪枝要做的就是避免将 $$2*2$$ 加入到`list`中，如何才能做到这一点呢？我们仍然从上述例子出发进行分析，在第一次加入 $$2*2$$ 时，相对应的`visited[1]`为`true`(对应 $$2*1$$)，而在第二次加入 $$2*2$$ 时，相对应的`visited[1]`为`false`，因为在`list`为 $$[1, 2*1]$$ 时，执行`list.pop_back()`后即置为`false`。

一句话总结即为：在遇到当前元素和前一个元素相等时，如果前一个元素`visited[i - 1] == false`, 那么我们就跳过当前元素并进入下一次循环，这就是剪枝的关键所在。另一点需要特别注意的是这种剪枝的方法能使用的前提是提供的`nums`是有序数组，否则无效。

### C++

```
class Solution {
public:
    /**
     * @param nums: A list of integers.
     * @return: A list of unique permutations.
     */
    vector<vector<int> > permuteUnique(vector<int> &nums) {
        vector<vector<int> > ret;
        if (nums.empty()) {
            return ret;
        }

        // important! sort before call `backTrack`
        sort(nums.begin(), nums.end());
        vector<bool> visited(nums.size(), false);
        vector<int> list;
        backTrack(ret, list, visited, nums);

        return ret;
    }

private:
    void backTrack(vector<vector<int> > &result, vector<int> &list, \
                   vector<bool> &visited, vector<int> &nums) {
        if (list.size() == nums.size()) {
            result.push_back(list);
            // avoid unnecessary call for `for loop`, but not essential
            return;
        }

        for (int i = 0; i != nums.size(); ++i) {
            if (visited[i] || (i != 0 && nums[i] == nums[i - 1] \
                && !visited[i - 1])) {
                continue;
            }
            visited[i] = true;
            list.push_back(nums[i]);
            backTrack(result, list, visited, nums);
            list.pop_back();
            visited[i] = false;
        }
    }
}; 
```

### 源码分析

Unique Subsets 和 Unique Permutations 的源码模板非常经典！建议仔细研读并体会其中奥义。

后记：在理解 Unique Subsets 和 Unique Permutations 的模板我花了差不多一整天时间才基本理解透彻，建议在想不清楚某些问题时先分析简单的问题，在纸上一步一步分析直至理解完全。

## 题解 2 - 字典序

Permutation 的题使用字典序的做法其实更为简单，且为迭代的解法，效率也更高。代码和之前的 Permutations 那道题一模一样。

### Java

```
class Solution {
    /**
     * @param nums: A list of integers.
     * @return: A list of unique permutations.
     */
    public ArrayList<ArrayList<Integer>> permuteUnique(ArrayList<Integer> nums) {
        ArrayList<ArrayList<Integer>> result = new ArrayList<ArrayList<Integer>>();
        if (nums == null || nums.size() == 0) return result;

        // deep copy(do not change nums)
        List<Integer> perm = new ArrayList<Integer>(nums);
        // sort first!!!
        Collections.sort(perm);

        while (true) {
            // step0: add perm into result
            result.add(new ArrayList<Integer>(perm));

            // step1: search the first num[k] < num[k+1] backward
            int k = -1;
            for (int i = perm.size() - 2; i >= 0; i--) {
                if (perm.get(i) < perm.get(i + 1)) {
                    k = i;
                    break;
                }
            }
            // if current rank is the largest, exit while loop
            if (k == -1) break;

            // step2: search the first perm[k] < perm[l] backward
            int l = perm.size() - 1;
            while (l > k && perm.get(l) <= perm.get(k)) l--;

            // step3: swap perm[k] with perm[l]
            Collections.swap(perm, k, l);

            // step4: reverse between k+1 and perm.length-1;
            reverse(perm, k + 1, perm.size() - 1);
        }

        return result;
    }

    private void reverse(List<Integer> nums, int lb, int ub) {
        for (int i = lb, j = ub; i < j; i++, j--) {
            Collections.swap(nums, i, j);
        }
    }
} 
```

### 源码分析

见前一题，略。

### 复杂度分析

略

## Reference

*   [Permutation II | 九章算法](http://www.jiuzhang.com/solutions/permutations-ii/)