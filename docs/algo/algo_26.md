# 第 26 节 Dynamic Programming - 动态规划（二）

## Unique Paths

*   tags: [DP_Matrix]

## Question

*   lintcode: [(114) Unique Paths](http://www.lintcode.com/en/problem/unique-paths/)

```
A robot is located at the top-left corner of a m x n grid
(marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time.
The robot is trying to reach the bottom-right corner of the grid
(marked 'Finish' in the diagram below).

How many possible unique paths are there?

Note
m and n will be at most 100. 
```

## 题解

题目要求：给定*m x n*矩阵，求左上角到右下角的路径总数，每次只能向左或者向右前进。按照动态规划中矩阵类问题的通用方法：

1.  State: f[m][n] 从起点到坐标(m,n)的路径数目
2.  Function: f[m][n] = f[m-1][n] + f[m][n-1] 分析终点与左边及右边节点的路径数，发现从左边或者右边到达终点的路径一定不会重合，相加即为唯一的路径总数
3.  Initialization: f[i][j] = 1, 到矩阵中任一节点均至少有一条路径，其实关键之处在于给第 0 行和第 0 列初始化，免去了单独遍历第 0 行和第 0 列进行初始化
4.  Answer: f[m - 1][n - 1]

### C++

```
class Solution {
public:
    /**
     * @param n, m: positive integer (1 <= n ,m <= 100)
     * @return an integer
     */
    int uniquePaths(int m, int n) {
        if (m < 1 || n < 1) {
            return 0;
        }

        vector<vector<int> > ret(m, vector<int>(n, 1));

        for (int i = 1; i != m; ++i) {
            for (int j = 1; j != n; ++j) {
                ret[i][j] = ret[i - 1][j] + ret[i][j - 1];
            }
        }

        return ret[m - 1][n - 1];
    }
}; 
```

### 源码分析

1.  异常处理，虽然题目有保证为正整数，但还是判断一下以防万一
2.  初始化二维矩阵，值均为 1
3.  按照转移矩阵函数进行累加
4.  任何`ret[m - 1][n - 1]`

## Unique Paths II

*   tags: [DP_Matrix]

## Question

*   lintcode: [(115) Unique Paths II](http://www.lintcode.com/en/problem/unique-paths-ii/)

```
Follow up for "Unique Paths":

Now consider if some obstacles are added to the grids.
How many unique paths would there be?

An obstacle and empty space is marked as 1 and 0 respectively in the grid.
Note
m and n will be at most 100.

Example
For example,
There is one obstacle in the middle of a 3x3 grid as illustrated below.

[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
The total number of unique paths is 2. 
```

## 题解

在上题的基础上加了 obstacal 这么一个限制条件，那么也就意味着凡是遇到障碍点，其路径数马上变为 0，需要注意的是初始化环节和上题有较大不同。首先来看看错误的初始化实现。

### C++ initialization error

```
class Solution {
public:
    /**
     * @param obstacleGrid: A list of lists of integers
     * @return: An integer
     */
    int uniquePathsWithObstacles(vector<vector<int> > &obstacleGrid) {
        if(obstacleGrid.empty() || obstacleGrid[0].empty()) {
            return 0;
        }

        const int M = obstacleGrid.size();
        const int N = obstacleGrid[0].size();

        vector<vector<int> > ret(M, vector<int>(N, 0));

        for (int i = 0; i != M; ++i) {
            if (0 == obstacleGrid[i][0]) {
                ret[i][0] = 1;
            }
        }
        for (int i = 0; i != N; ++i) {
            if (0 == obstacleGrid[0][i]) {
                ret[0][i] = 1;
            }
        }

        for (int i = 1; i != M; ++i) {
            for (int j = 1; j != N; ++j) {
                if (obstacleGrid[i][j]) {
                    ret[i][j] = 0;
                } else {
                    ret[i][j] = ret[i -1][j] + ret[i][j - 1];
                }
            }
        }

        return ret[M - 1][N - 1];
    }
}; 
```

### 源码分析

错误之处在于初始化第 0 行和第 0 列时，未考虑到若第 0 行/列有一个坐标出现障碍物，则当前行/列后的元素路径数均为 0！

### C++

```
class Solution {
public:
    /**
     * @param obstacleGrid: A list of lists of integers
     * @return: An integer
     */
    int uniquePathsWithObstacles(vector<vector<int> > &obstacleGrid) {
        if(obstacleGrid.empty() || obstacleGrid[0].empty()) {
            return 0;
        }

        const int M = obstacleGrid.size();
        const int N = obstacleGrid[0].size();

        vector<vector<int> > ret(M, vector<int>(N, 0));

        for (int i = 0; i != M; ++i) {
            if (obstacleGrid[i][0]) {
                break;
            } else {
                ret[i][0] = 1;
            }
        }
        for (int i = 0; i != N; ++i) {
            if (obstacleGrid[0][i]) {
                break;
            } else {
                ret[0][i] = 1;
            }
        }

        for (int i = 1; i != M; ++i) {
            for (int j = 1; j != N; ++j) {
                if (obstacleGrid[i][j]) {
                    ret[i][j] = 0;
                } else {
                    ret[i][j] = ret[i -1][j] + ret[i][j - 1];
                }
            }
        }

        return ret[M - 1][N - 1];
    }
}; 
```

### 源码分析

1.  异常处理
2.  初始化二维矩阵(全 0 阵)，尤其注意遇到障碍物时应`break`跳出当前循环
3.  递推路径数
4.  返回`ret[M - 1][N - 1]`

## Climbing Stairs

## Question

*   lintcode: [(111) Climbing Stairs](http://www.lintcode.com/en/problem/climbing-stairs/)

```
You are climbing a stair case. It takes n steps to reach to the top.

Each time you can either climb 1 or 2 steps.
In how many distinct ways can you climb to the top?

Example
Given an example n=3 , 1+1+1=2+1=1+2=3

return 3 
```

## 题解

题目问的是到达顶端的方法数，我们采用序列类问题的通用分析方法，可以得到如下四要素：

1.  State: f[i] 爬到第 i 级的方法数
2.  Function: f[i]=f[i-1]+f[i-2]
3.  Initialization: f[0]=1,f[1]=1
4.  Answer: f[n]

尤其注意状态转移方程的写法，f[i]只可能由两个中间状态转化而来，一个是 f[i-1]，由 f[i-1]到 f[i]其方法总数并未增加；另一个是 f[i-2]，由 f[i-2]到 f[i]隔了两个台阶，因此有 1+1 和 2 两个方法，因此容易写成 f[i]=f[i-1]+f[i-2]+1，但仔细分析后能发现，由 f[i-2]到 f[i]的中间状态 f[i-1]已经被利用过一次，故 f[i]=f[i-1]+f[i-2]. 使用动规思想解题时需要分清『重叠子状态』, 如果有重复的需要去重。

### C++

```
class Solution {
public:
    /**
     * @param n: An integer
     * @return: An integer
     */
    int climbStairs(int n) {
        if (n < 1) {
            return 0;
        }

        vector<int> ret(n + 1, 1);

        for (int i = 2; i != n + 1; ++i) {
            ret[i] = ret[i - 1] + ret[i - 2];
        }

        return ret[n];
    }
}; 
```

1.  异常处理
2.  初始化 n+1 个元素，初始值均为 1。之所以用 n+1 个元素是下标分析起来更方便
3.  状态转移方程
4.  返回 ret[n]

初始化 ret[0]也为 1，可以认为到第 0 级也是一种方法。

以上答案的空间复杂度为 $$O(n)$$，仔细观察后可以发现在状态转移方程中，我们可以使用三个变量来替代长度为 n+1 的数组。具体代码可参考 [climbing-stairs | 九章算法](http://www.jiuzhang.com/solutions/climbing-stairs/)

### Python

```
class Solution:
    def climbStairs(n):
        if n < 1:
            return 0

        l = r = 1
        for _ in xrange(n - 1):
            l, r = r, r + l
        return r 
```

### C++

```
class Solution {
public:
    /**
     * @param n: An integer
     * @return: An integer
     */
    int climbStairs(int n) {
        if (n < 1) {
            return 0;
        }

        int ret0 = 1, ret1 = 1, ret2 = 1;

        for (int i = 2; i != n + 1; ++i) {
            ret0 = ret1 + ret2;
            ret2 = ret1;
            ret1 = ret0;
        }

        return ret0;
    }
}; 
```

## Jump Game

## Question

*   lintcode:

[(116) Jump Game](http://www.lintcode.com/en/problem/jump-game/)

```
Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

Example
A = [2,3,1,1,4], return true.

A = [3,2,1,0,4], return false. 
```

## 题解(自顶向下-动态规划)

1.  State: f[i] 从起点出发能否达到 i
2.  Function: `f[i] = OR (f[j], j < i ~\&\&~ j + A[j] \geq i)`, 状态 $$j$$ 转移到 $$i$$, 所有小于 i 的下标 j 的元素中是否存在能从 j 跳转到 i 得
3.  Initialization: f[0] = true;
4.  Answer: 递推到第 N - 1 个元素时，f[N-1]

这种自顶向下的方法需要使用额外的 $$O(n)$$ 空间，保存小于 N-1 时的状态。且时间复杂度在恶劣情况下有可能变为 $$1 + 2 + \cdots + n = O(n²)$$, 出现 TLE 无法 AC 的情况，不过工作面试能给出这种动规的实现就挺好的了。

### C++ from top to bottom

```
class Solution {
public:
    /**
     * @param A: A list of integers
     * @return: The boolean answer
     */
    bool canJump(vector<int> A) {
        if (A.empty()) {
            return true;
        }

        vector<bool> jumpto(A.size(), false);
        jumpto[0] = true;

        for (int i = 1; i != A.size(); ++i) {
            for (int j = i - 1; j >= 0; --j) {
                if (jumpto[j] && (j + A[j] >= i)) {
                    jumpto[i] = true;
                    break;
                }
            }
        }

        return jumpto[A.size() - 1];
    }
}; 
```

## 题解(自底向上-贪心法)

题意为问是否能从起始位置到达最终位置，我们首先分析到达最终位置的条件，从坐标 i 出发所能到达最远的位置为 $$f[i] = i + A[i]$$，如果要到达最终位置，即存在某个 $$i$$ 使得$$f[i] \geq N - 1$$, 而想到达 $$i$$, 则又需存在某个 $$j$$ 使得 $$f[j] \geq i - 1$$. 依此类推直到下标为 0.

**以下分析形式虽为动态规划，实则贪心法！**

1.  State: f[i] 从 $$i$$ 出发能否到达最终位置
2.  Function: $$f[j] = j + A[j] \geq i$$, 状态 $$j$$ 转移到 $$i$$, 置为`true`
3.  Initialization: 第一个为`true`的元素为 `A.size() - 1`
4.  Answer: 递推到第 0 个元素时，若其值为`true`返回`true`

### C++ greedy, from bottom to top

```
class Solution {
public:
    /**
     * @param A: A list of integers
     * @return: The boolean answer
     */
    bool canJump(vector<int> A) {
        if (A.empty()) {
            return true;
        }

        int index_true = A.size() - 1;
        for (int i = A.size() - 1; i >= 0; --i) {
            if (i + A[i] >= index_true) {
                index_true = i;
            }
        }

        return 0 == index_true ? true : false;
    }
}; 
```

## 题解(自顶向下-贪心法)

针对上述自顶向下可能出现时间复杂度过高的情况，下面使用贪心思想对 i 进行递推，每次遍历 A 中的一个元素时更新最远可能到达的元素，最后判断最远可能到达的元素是否大于 `A.size() - 1`

### C++ greedy, from top to bottom

```
class Solution {
public:
    /**
     * @param A: A list of integers
     * @return: The boolean answer
     */
    bool canJump(vector<int> A) {
        if (A.empty()) {
            return true;
        }

        int farthest = A[0];

        for (int i = 1; i != A.size(); ++i) {
            if ((i <= farthest) && (i + A[i] > farthest)) {
                farthest = i + A[i];
            }
        }

        return farthest >= A.size() - 1;
    }
}; 
```

## Word Break

*   tags: [DP_Sequence]

## Question

*   leetcode: [Word Break | LeetCode OJ](https://leetcode.com/problems/word-break/)
*   lintcode: [(107) Word Break](http://www.lintcode.com/en/problem/word-break/)

```
Given a string s and a dictionary of words dict, determine if s can be
segmented into a space-separated sequence of one or more dictionary words.

For example, given
s = "leetcode",
dict = ["leet", "code"].

Return true because "leetcode" can be segmented as "leet code". 
```

## 题解

单序列(DP_Sequence) DP 题，由单序列动态规划的四要素可大致写出：

1.  State: `f[i]` 表示前`i`个字符能否根据词典中的词被成功分词。
2.  Function: `f[i] = or{f[j], j < i, letter in [j+1, i] can be found in dict}`, 含义为小于`i`的索引`j`中只要有一个`f[j]`为真且`j+1`到`i`中组成的字符能在词典中找到时，`f[i]`即为真，否则为假。具体实现可分为自顶向下或者自底向上。
3.  Initialization: `f[0] = true`, 数组长度为字符串长度 + 1，便于处理。
4.  Answer: `f[s.length]`

考虑到单词长度通常不会太长，故在`s`较长时使用自底向上效率更高。

### Python

```
class Solution:
    # @param s, a string
    # @param wordDict, a set<string>
    # @return a boolean
    def wordBreak(self, s, wordDict):
        if not s:
            return True
        if not wordDict:
            return False

        max_word_len = max([len(w) for w in wordDict])
        can_break = [True]
        for i in xrange(len(s)):
            can_break.append(False)
            for j in xrange(i, -1, -1):
                # optimize for too long interval
                if i - j + 1 > max_word_len:
                    break
                if can_break[j] and s[j:i + 1] in wordDict:
                    can_break[i + 1] = True
                    break
        return can_break[-1] 
```

### C++

```
class Solution {
public:
    bool wordBreak(string s, unordered_set<string>& wordDict) {
        if (s.empty()) return true;
        if (wordDict.empty()) return false;

        // get the max word length of wordDict
        int max_word_len = 0;
        for (unordered_set<string>::iterator it = wordDict.begin();
         it != wordDict.end(); ++it) {

            max_word_len = max(max_word_len, (*it).size());
        }

        vector<bool> can_break(s.size() + 1, false);
        can_break[0] = true;
        for (int i = 1; i <= s.size(); ++i) {
            for (int j = i - 1; j >= 0; --j) {
                // optimize for too long interval
                if (i - j > max_word_len) break;

                if (can_break[j] && 
            wordDict.find(s.substr(j, i - j)) != wordDict.end()) {

                    can_break[i] = true;
                    break;
                }
            }
        }

        return can_break[s.size()];
    }
}; 
```

### Java

```
public class Solution {
    public boolean wordBreak(String s, Set<String> wordDict) {
        if (s == null || s.length() == 0) return true;
        if (wordDict == null || wordDict.isEmpty()) return false;

        // get the max word length of wordDict
        int max_word_len = 0;
        for (String word : wordDict) {
            max_word_len = Math.max(max_word_len, word.length());
        }

        boolean[] can_break = new boolean[s.length() + 1];
        can_break[0] = true;
        for (int i = 1; i <= s.length(); i++) {
            for (int j = i - 1; j >= 0; j--) {
                // optimize for too long interval
                if (i - j > max_word_len) break;

                String word = s.substring(j, i);
                if (can_break[j] && wordDict.contains(word)) {
                    can_break[i] = true;
                    break;
                }
            }
        }

        return can_break[s.length()];
    }
} 
```

### 源码分析

Python 之类的动态语言无需初始化指定大小的数组，使用时下标`i`比 C++和 Java 版的程序少 1。使用自底向上的方法求解状态转移，首先遍历一次词典求得单词最大长度以便后续优化。

### 复杂度分析

1.  求解词典中最大单词长度，时间复杂度为词典长度乘上最大单词长度 $$O(L*D \cdot L*w)$$
2.  词典中找单词的时间复杂度为 $$O(1)$$(哈希表结构)
3.  两重 for 循环，内循环在超出最大单词长度时退出，故最坏情况下两重 for 循环的时间复杂度为 $$O(n L_w)$$.
4.  故总的时间复杂度近似为 $$O(n L_w)$$.
5.  使用了与字符串长度几乎等长的布尔数组和临时单词`word`，空间复杂度近似为 $$O(n)$$.

## Longest Increasing Subsequence

*   tags: [DP_Sequence]

## Question

*   lintcode: [(76) Longest Increasing Subsequence](http://www.lintcode.com/en/problem/longest-increasing-subsequence/)
*   [Dynamic Programming | Set 3 (Longest Increasing Subsequence) - GeeksforGeeks](http://www.geeksforgeeks.org/dynamic-programming-set-3-longest-increasing-subsequence/)

### Problem Statement

Given a sequence of integers, find the longest increasing subsequence (LIS).

You code should return the length of the LIS.

#### Example

For [5, 4, 1, 2, 3], the LIS is [1, 2, 3], return 3

For [4, 2, 4, 5, 3, 7], the LIS is [4, 4, 5, 7], return 4

#### Challenge

Time complexity O(n²) or O(nlogn)

#### Clarification

What's the definition of longest increasing subsequence?

*   The longest increasing subsequence problem is to find a subsequence of a given sequence in which the subsequence's elements are in sorted order, lowest to highest, and in which the subsequence is as long as possible. This subsequence is not necessarily contiguous, or unique.
*   https://en.wikipedia.org/wiki/Longest*common*subsequence_problem

## 题解

由题意知这种题应该是单序列动态规划题，结合四要素，可定义`f[i]`为前`i`个数字中的 LIC 数目，那么问题来了，接下来的状态转移方程如何写？似乎写不出来... 再仔细看看 LIS 的定义，状态转移的关键一环应该为数字本身而不是最后返回的结果(数目)，那么理所当然的，我们应定义`f[i]`为前`i`个数字中以第`i`个数字结尾的 LIS 长度，相应的状态转移方程为`f[i] = {1 + max{f[j]} where j < i, nums[j] < nums[i]}`, 该转移方程的含义为在所有满足以上条件的 j 中将最大的`f[j]` 赋予`f[i]`, 如果上式不满足，则`f[i] = 1`. 具体实现时不能直接使用`f[i] = 1 + max(f[j])`, 应为若`if f[i] < 1 + f[j], f[i] = 1 + f[j]`. 最后返回 `max(f[])`.

### Python

```
class Solution:
    """
    @param nums: The integer array
    @return: The length of LIS (longest increasing subsequence)
    """
    def longestIncreasingSubsequence(self, nums):
        if not nums:
            return 0

        lis = [1] * len(nums)
        for i in xrange(1, len(nums)):
            for j in xrange(i):
                if nums[j] <= nums[i] and lis[i] < 1 + lis[j]:
                    lis[i] = 1 + lis[j]
        return max(lis) 
```

### C++

```
class Solution {
public:
    /**
     * @param nums: The integer array
     * @return: The length of LIS (longest increasing subsequence)
     */
    int longestIncreasingSubsequence(vector<int> nums) {
        if (nums.empty()) return 0;

        int len = nums.size();
        vector<int> lis(len, 1);

        for (int i = 1; i < len; ++i) {
            for (int j = 0; j < i; ++j) {
                if (nums[j] <= nums[i] && (lis[i] < lis[j] + 1)) {
                    lis[i] = 1 + lis[j];
                }
            }
        }

        return *max_element(lis.begin(), lis.end());
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param nums: The integer array
     * @return: The length of LIS (longest increasing subsequence)
     */
    public int longestIncreasingSubsequence(int[] nums) {
        if (nums == null || nums.length == 0) return 0;

        int[] lis = new int[nums.length];
        Arrays.fill(lis, 1);

        for (int i = 1; i < nums.length; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] <= nums[i] && (lis[i] < lis[j] + 1)) {
                    lis[i] = lis[j] + 1;
                }
            }
        }

        // get the max lis
        int max_lis = 0;
        for (int i = 0; i < lis.length; i++) {
            if (lis[i] > max_lis) {
                max_lis = lis[i];
            }
        }

        return max_lis;
    }
} 
```

### 源码分析

1.  初始化数组，初始值为 1
2.  根据状态转移方程递推求得`lis[i]`
3.  遍历`lis` 数组求得最大值

### 复杂度分析

使用了与 nums 等长的空间，空间复杂度 $$O(n)$$. 两重 for 循环，最坏情况下 $$O(n²)$$, 遍历求得最大值，时间复杂度为 $$O(n)$$, 故总的时间复杂度为 $$O(n²)$$.

## Follow up

上述问题均只输出最大值，现在需要输出 LIS 中的每一个原始元素值。

## 题解 1 - LIS

由于以上递归推导式只能返回最大值，如果现在需要返回 LIS 中的每个元素，直观来讲，构成 LIS 数组中的值对应的原数组值即为我们想要的结果。我们不妨从后往前考虑，依次移除 lis[i] 数组中的值(减一)和索引，遇到和 lis[i]的值相等的 LIS 时即加入到最终返回结果。

### Java

```
import java.util.*;

public class Solution {
    /**
     * @param nums: The integer array
     * @return: LIS array
     */
    public int[] longestIncreasingSubsequence(int[] nums) {
        if (nums == null || nums.length == 0) return null;

        int[] lis = new int[nums.length];
        Arrays.fill(lis, 1);

        for (int i = 1; i < nums.length; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] <= nums[i] && (lis[i] < lis[j] + 1)) {
                    lis[i] = lis[j] + 1;
                }
            }
        }

        // get the max lis
        int max_lis = 0, index = 0;
        for (int i = 0; i < lis.length; i++) {
            if (lis[i] > max_lis) {
                max_lis = lis[i];
                index = i;
            }
        }

        // get result
        int[] result = new int[max_lis];
        for (int i = index; i >= 0; i--) {
            if (lis[i] == max_lis) {
                result[max_lis - 1] = nums[i];
                max_lis--;
            }
        }

        return result;
    }

    public static void main(String[] args) {
        int[] nums = new int[]{5, 4, 1, 2, 3};
        Solution sol = new Solution();
        int[] result = sol.longestIncreasingSubsequence(nums);
        for (int i : result) {
            System.out.println(i);
        }
    }
} 
```

关于`// get result` 那一节中为何`max_lis` 自减一定是会得到最终想要的结果？假如有和其一样的 lis 如何破？根据 DP 中状态的定义可知正好为其逆过程，只不过答案不唯一，反向输出的答案输出的是最靠右的结果。