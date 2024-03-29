# 第 27 节 Dynamic Programming - 动态规划（三）

## Palindrome Partitioning II

*   tags: [DP_Sequence]

## Question

*   leetcode: [Palindrome Partitioning II | LeetCode OJ](https://leetcode.com/problems/palindrome-partitioning-ii/)
*   lintcode: [(108) Palindrome Partitioning II](http://www.lintcode.com/en/problem/palindrome-partitioning-ii/)

```
Given a string s, cut s into some substrings such that
every substring is a palindrome.

Return the minimum cuts needed for a palindrome partitioning of s.

Example
For example, given s = "aab",

Return 1 since the palindrome partitioning ["aa","b"] could be produced
using 1 cut. 
```

## 题解 1 - 仅对最小切割数使用动态规划

此题为难题，费了我九牛二虎之力才 bug-free :( 求最小切分数，非常明显的动规暗示。由问题出发可建立状态`f[i]` 表示到索引`i` 处时需要的最少切割数(即切割前 i 个字符组成的字符串)，状态转移方程为`f[i] = min{1 + f[j]}, where j < i and substring [j, i] is palindrome`, 判断区间[j, i] 是否为回文简单的方法可反转后比较。

### Python

```
class Solution:
    # @param s, a string
    # @return an integer
    def minCut(self, s):
        if not s:
            print 0

        cut = [i - 1 for i in xrange(1 + len(s))]

        for i in xrange(1 + len(s)):
            for j in xrange(i):
                # s[j:i] is palindrome
                if s[j:i] == s[j:i][::-1]:
                    cut[i] = min(cut[i], 1 + cut[j])
        return cut[-1] 
```

### 源码分析

1.  当 s 为 None 或者列表为空时返回 0
2.  初始化切割数数组
3.  子字符串的索引位置可为`[0, len(s) - 1]`, 内循环 j 比外循环 i 小，故可将 i 的最大值设为`1 + len(s)` 较为便利。
4.  回文的判断使用了`[::-1]` 对字符串进行反转
5.  最后返回数组最后一个元素

### 复杂度分析

两重循环，遍历的总次数为 $$1/2 \cdots n²)$$, 每次回文的判断时间复杂度为 $$O(len(s))$$, 故总的时间复杂度近似为 $$O(n³)$$. 在 s 长度较长时会 TLE. 使用了与 s 等长的辅助切割数数组，空间复杂度近似为 $$O(n)$$.

## 题解 2 - 使用动态规划计算子字符串回文状态

切割数部分使用的是动态规划，优化的空间不大，仔细瞅瞅可以发现在判断字符串是否为回文的部分存在大量重叠计算，故可引入动态规划进行优化，时间复杂度可优化至到平方级别。

定义状态 PaMat[i][j] 为区间 `[i,j]` 是否为回文的标志, 对应此状态的子问题可从回文的定义出发，如果字符串首尾字符相同且在去掉字符串首尾字符后字符串仍为回文，则原字符串为回文，相应的状态转移方程 `PaMat[i][j] = s[i] == s[j] && PaMat[i+1][j-1]`, 由于状态转移方程中依赖比`i`大的结果，故实现中需要从索引大的往索引小的递推，另外还需要考虑一些边界条件和初始化方式，做到 bug-free 需要点时间。

### Python

```
class Solution:
    # @param s, a string
    # @return an integer
    def minCut(self, s):
        if not s:
            print 0

        cut = [i - 1 for i in xrange(1 + len(s))]
        PaMatrix = self.getMat(s)

        for i in xrange(1 + len(s)):
            for j in xrange(i):
                if PaMatrix[j][i - 1]:
                    cut[i] = min(cut[i], cut[j] + 1)
        return cut[-1]

    def getMat(self, s):
        PaMat = [[True for i in xrange(len(s))] for j in xrange(len(s))]
        for i in xrange(len(s), -1, -1):
            for j in xrange(i, len(s)):
                if j == i:
                    PaMat[i][j] = True
        # not necessary if init with True
                # elif j == i + 1:
                #     PaMat[i][j] = s[i] == s[j]
                else:
                    PaMat[i][j] = s[i] == s[j] and PaMat[i + 1][j - 1]
        return PaMat 
```

### C++

```
class Solution {
public:
    int minCut(string s) {
        if (s.empty()) return 0;

        int len = s.size();
        vector<int> cut;
        for (int i = 0; i < 1 + len; ++i) {
            cut.push_back(i - 1);
        }
        vector<vector<bool> > mat = getMat(s);

        for (int i = 1; i < 1 + len; ++i) {
            for (int j = 0; j < i; ++j) {
                if (mat[j][i - 1]) {
                    cut[i] = min(cut[i], 1 + cut[j]);
                }
            }
        }

        return cut[len];
    }

    vector<vector<bool> > getMat(string s) {
        int len = s.size();
        vector<vector<bool> > mat = vector<vector<bool> >(len, vector<bool>(len, true));
        for (int i = len; i >= 0; --i) {
            for (int j = i; j < len; ++j) {
                if (j == i) {
                    mat[i][j] = true;
                } else if (j == i + 1) {
                    mat[i][j] = (s[i] == s[j]);
                } else {
                    mat[i][j] = ((s[i] == s[j]) && mat[i + 1][j - 1]);
                }
            }
        }

        return mat;
    }
}; 
```

### Java

```
public class Solution {
    public int minCut(String s) {
        if (s == null || s.length() == 0) return 0;

        int len = s.length();
        int[] cut = new int[1 + len];
        for (int i = 0; i < 1 + len; ++i) {
            cut[i] = i - 1;
        }
        boolean[][] mat = paMat(s);

        for (int i = 1; i < 1 + len; i++) {
            for (int j = 0; j < i; j++) {
                if (mat[j][i - 1]) {
                    cut[i] = Math.min(cut[i], 1 + cut[j]);
                }
            }
        }

        return cut[len];
    }

    private boolean[][] paMat(String s) {
        int len = s.length();
        boolean[][] mat = new boolean[len][len];

        for (int i = len - 1; i >= 0; i--) {
            for (int j = i; j < len; j++) {
                if (j == i) {
                    mat[i][j] = true;
                } else if (j == i + 1) {
                    mat[i][j] = (s.charAt(i) == s.charAt(j));
                } else {
                    mat[i][j] = (s.charAt(i) == s.charAt(j)) && mat[i + 1][j - 1];
                }
            }
        }

        return mat;
    }
} 
```

### 源码分析

初始化 cut 长度为`1 + len(s)`, `cut[0] = -1` 便于状态转移方程实现。在执行`mat[i][j] == ... mat[i + 1][j - 1]`时前提是`j - 1 > i + 1`, 所以才需要分情况赋值。使用`getMat` 得到字符串区间的回文矩阵，由于 cut 的长度为 1+len(s), 两重 for 循环时需要注意索引的取值，这个地方非常容易错。

### 复杂度分析

最坏情况下每次 for 循环都遍历 n 次，时间复杂度近似为 $$O(n²)$$, 使用了二维回文矩阵保存记忆化搜索结果，空间复杂度为 $$O(n²)$$.

## Reference

*   [Palindrome Partitioning II 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/palindrome-partitioning-ii/)
*   soulmachine 的 leetcode 题解

## Longest Common Subsequence

*   tags: [DP*Two*Sequence]

## Question

*   lintcode: [(77) Longest Common Subsequence](http://www.lintcode.com/en/problem/longest-common-subsequence/)

```
Given two strings, find the longest common subsequence (LCS).

Your code should return the length of LCS.

Have you met this question in a real interview? Yes
Example
For "ABCD" and "EDCA", the LCS is "A" (or "D", "C"), return 1.

For "ABCD" and "EACB", the LCS is "AC", return 2.

Clarification
What's the definition of Longest Common Subsequence?

https://en.wikipedia.org/wiki/Longest_common_subsequence_problem
http://baike.baidu.com/view/2020307.htm 
```

## 题解

求最长公共子序列的数目，注意这里的子序列可以不是连续序列，务必问清楚题意。求『最长』类的题目往往与动态规划有点关系，这里是两个字符串，故应为双序列动态规划。

这道题的状态很容易找，不妨先试试以`f[i][j]`表示字符串 A 的前 `i` 位和字符串 B 的前 `j` 位的最长公共子序列数目，那么接下来试试寻找其状态转移方程。从实际例子`ABCD`和`EDCA`出发，首先初始化`f`的长度为字符串长度加 1，那么有`f[0][0] = 0`, `f[0][*] = 0`, `f[*][0] = 0`, 最后应该返回`f[lenA][lenB]`. 即 f 中索引与字符串索引对应(字符串索引从 1 开始算起)，那么在 A 的第一个字符与 B 的第一个字符相等时，`f[1][1] = 1 + f[0][0]`, 否则`f[1][1] = max(f[0][1], f[1][0])`。

推而广之，也就意味着若`A[i] == B[j]`, 则分别去掉这两个字符后，原 LCS 数目减一，那为什么一定是 1 而不是 0 或者 2 呢？因为不管公共子序列是以哪个字符结尾，在`A[i] == B[j]`时 LCS 最多只能增加 1\. 而在`A[i] != B[j]`时，由于`A[i]` 或者 `B[j]` 不可能同时出现在最终的 LCS 中，故这个问题可进一步缩小，`f[i][j] = max(f[i - 1][j], f[i][j - 1])`. 需要注意的是这种状态转移方程只依赖最终的 LCS 数目，而不依赖于公共子序列到底是以第几个索引结束。

### Python

```
class Solution:
    """
    @param A, B: Two strings.
    @return: The length of longest common subsequence of A and B.
    """
    def longestCommonSubsequence(self, A, B):
        if not A or not B:
            return 0

        lenA, lenB = len(A), len(B)
        lcs = [[0 for i in xrange(1 + lenA)] for j in xrange(1 + lenB)]

        for i in xrange(1, 1 + lenA):
            for j in xrange(1, 1 + lenB):
                if A[i - 1] == B[j - 1]:
                    lcs[i][j] = 1 + lcs[i - 1][j - 1]
                else:
                    lcs[i][j] = max(lcs[i - 1][j], lcs[i][j - 1])
        return lcs[lenA][lenB] 
```

### C++

```
class Solution {
public:
    /**
     * @param A, B: Two strings.
     * @return: The length of longest common subsequence of A and B.
     */
    int longestCommonSubsequence(string A, string B) {
        if (A.empty()) return 0;
        if (B.empty()) return 0;

        int lenA = A.size();
        int lenB = B.size();
        vector<vector<int> > lcs = \
            vector<vector<int> >(1 + lenA, vector<int>(1 + lenB));

        for (int i = 1; i < 1 + lenA; i++) {
            for (int j = 1; j < 1 + lenB; j++) {
                if (A[i - 1] == B[j - 1]) {
                    lcs[i][j] = 1 + lcs[i - 1][j - 1];
                } else {
                    lcs[i][j] = max(lcs[i - 1][j], lcs[i][j - 1]);
                }
            }
        }

        return lcs[lenA][lenB];
    }
}; 
```

### Java

```
 public class Solution {
    /**
     * @param A, B: Two strings.
     * @return: The length of longest common subsequence of A and B.
     */
    public int longestCommonSubsequence(String A, String B) {
        if (A == null || A.length() == 0) return 0;
        if (B == null || B.length() == 0) return 0;

        int lenA = A.length();
        int lenB = B.length();
        int[][] lcs = new int[1 + lenA][1 + lenB];

        for (int i = 1; i < 1 + lenA; i++) {
            for (int j = 1; j < 1 + lenB; j++) {
                if (A.charAt(i - 1) == B.charAt(j - 1)) {
                    lcs[i][j] = 1 + lcs[i - 1][j - 1];
                } else {
                    lcs[i][j] = Math.max(lcs[i - 1][j], lcs[i][j - 1]);
                }
            }
        }

        return lcs[lenA][lenB];
    }
} 
```

### 源码分析

注意 Python 中的多维数组初始化方式，不可简单使用`[[0] * len(A)] * len(B)]`, 具体原因是因为 Python 中的对象引用方式 [^Stackoverflow]。

### 复杂度分析

两重 for 循环，时间复杂度为 $$O(lenA \times lenB)$$, 使用了二维数组，空间复杂度也为 $$O(lenA \times lenB)$$.

## Reference

*   [^Stackoverflow]: [Python multi-dimensional array initialization without a loop - Stack Overflow](http://stackoverflow.com/questions/3662475/python-multi-dimensional-array-initialization-without-a-loop)

## Edit Distance

*   tags: [DP*Two*Sequence]

## Question

*   leetcode: [Edit Distance | LeetCode OJ](https://leetcode.com/problems/edit-distance/)
*   lintcode: [(119) Edit Distance](http://www.lintcode.com/en/problem/edit-distance/)

```
Given two words word1 and word2, find the minimum number of steps required 
to convert word1 to word2\. (each operation is counted as 1 step.)

You have the following 3 operations permitted on a word:

Insert a character
Delete a character
Replace a character
Example
Given word1 = "mart" and word2 = "karma", return 3. 
```

## 题解 1 - 双序列动态规划

两个字符串比较，求最值，直接看似乎并不能直接找出解决方案，这时往往需要使用动态规划的思想寻找递推关系。使用双序列动态规划的通用做法，不妨定义`f[i][j]`为字符串 1 的前`i`个字符和字符串 2 的前`j`个字符的编辑距离，那么接下来寻找其递推关系。增删操作互为逆操作，即增或者删产生的步数都是一样的。故初始化时容易知道`f[0][j] = j, f[i][0] = i`, 接下来探讨`f[i][j]` 和`f[i - 1][j - 1]`的关系，和 LCS 问题类似，我们分两种情况讨论，即`word1[i] == word2[j]` 与否，第一种相等的情况有：

1.  `i == j`, 且有`word1[i] == word2[j]`, 则由`f[i - 1][j - 1] -> f[i][j]` 不增加任何操作，有`f[i][j] = f[i - 1][j - 1]`.
2.  `i != j`, 由于字符数不等，肯定需要增/删一个字符，但是增删 word1 还是 word2 是不知道的，故可取其中编辑距离的较小值，即`f[i][j] = 1 + min{f[i - 1][j], f[i][j - 1]}`.

第二种不等的情况有：

1.  `i == j`, 有`f[i][j] = 1 + f[i - 1][j - 1]`.
2.  `i != j`, 由于字符数不等，肯定需要增/删一个字符，但是增删 word1 还是 word2 是不知道的，故可取其中编辑距离的较小值，即`f[i][j] = 1 + min{f[i - 1][j], f[i][j - 1]}`.

最后返回`f[len(word1)][len(word2)]`

### Python

```
class Solution: 
    # @param word1 & word2: Two string.
    # @return: The minimum number of steps.
    def minDistance(self, word1, word2):
        len1, len2 = 0, 0
        if word1:
            len1 = len(word1)
        if word2:
            len2 = len(word2)
        if not word1 or not word2:
            return max(len1, len2)

        f = [[i + j for i in xrange(1 + len2)] for j in xrange(1 + len1)]

        for i in xrange(1, 1 + len1):
            for j in xrange(1, 1 + len2):
                if word1[i - 1] == word2[j - 1]:
                    f[i][j] = min(f[i - 1][j - 1], 1 + f[i - 1][j], 1 + f[i][j - 1])
                else:
                    f[i][j] = 1 + min(f[i - 1][j - 1], f[i - 1][j], f[i][j - 1])
        return f[len1][len2] 
```

### C++

```
class Solution {
public:
    /**
     * @param word1 & word2: Two string.
     * @return: The minimum number of steps.
     */
    int fistance(string word1, string word2) {
        if (word1.empty() || word2.empty()) {
            return max(word1.size(), word2.size());
        }

        int len1 = word1.size();
        int len2 = word2.size();
        vector<vector<int> > f = \
            vector<vector<int> >(1 + len1, vector<int>(1 + len2, 0));
        for (int i = 0; i <= len1; ++i) {
            f[i][0] = i;
        }
        for (int i = 0; i <= len2; ++i) {
            f[0][i] = i;
        }

        for (int i = 1; i <= len1; ++i) {
            for (int j = 1; j <= len2; ++j) {
                if (word1[i - 1] == word2[j - 1]) {
                    f[i][j] = min(f[i - 1][j - 1], 1 + f[i - 1][j]);
                    f[i][j] = min(f[i][j], 1 + f[i][j - 1]);
                } else {
                    f[i][j] = min(f[i - 1][j - 1], f[i - 1][j]);
                    f[i][j] = 1 + min(f[i][j], f[i][j - 1]);
                }
            }
        }

        return f[len1][len2];
    }
}; 
```

### Java

```
public class Solution {
    public int minDistance(String word1, String word2) {
        int len1 = 0, len2 = 0;
        if (word1 != null && word2 != null) {
            len1 = word1.length();
            len2 = word2.length();
        }
        if (word1 == null || word2 == null) {
            return Math.max(len1, len2);
        }

        int[][] f = new int[1 + len1][1 + len2];
        for (int i = 0; i <= len1; i++) {
            f[i][0] = i;
        }
        for (int i = 0; i <= len2; i++) {
            f[0][i] = i;
        }

        for (int i = 1; i <= len1; i++) {
            for (int j = 1; j <= len2; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    f[i][j] = Math.min(f[i - 1][j - 1], 1 + f[i - 1][j]);
                    f[i][j] = Math.min(f[i][j], 1 + f[i][j - 1]);
                } else {
                    f[i][j] = Math.min(f[i - 1][j - 1], f[i - 1][j]);
                    f[i][j] = 1 + Math.min(f[i][j], f[i][j - 1]);
                }
            }
        }

        return f[len1][len2];
    }
} 
```

### 源码解析

1.  边界处理
2.  初始化二维矩阵(Python 中初始化时 list 中 len2 在前，len1 在后)
3.  i, j 从 1 开始计数，比较 word1 和 word2 时注意下标
4.  返回`f[len1][len2]`

### 复杂度分析

两重 for 循环，时间复杂度为 $$O(len1 \cdot len2)$$. 使用二维矩阵，空间复杂度为 $$O(len1 \cdot len2)$$.

## Jump Game II

## Question

*   lintcode: [(117) Jump Game II](http://www.lintcode.com/en/problem/jump-game-ii/)

```
Given an array of non-negative integers,
you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the minimum number of jumps.

Example
Given array A = [2,3,1,1,4]

The minimum number of jumps to reach the last index is 2.
(Jump 1 step from index 0 to 1, then 3 steps to the last index.) 
```

## 题解(自顶向下-动态规划)

首先来看看使用动态规划的解法，由于复杂度较高在 A 元素较多时会出现 TLE，因为时间复杂度接近 $$O(n³)$$. 工作面试中给出动规的实现就挺好了。

1.  State: f[i] 从起点跳到这个位置最少需要多少步
2.  Function: f[i] = MIN(f[j]+1, j < i && j + A[j] >= i) 取出所有能从 j 到 i 中的最小值
3.  Initialization: f[0] = 0，即一个元素时不需移位即可到达
4.  Answer: f[n-1]

### C++ Dynamic Programming

```
class Solution {
public:
    /**
     * @param A: A list of lists of integers
     * @return: An integer
     */
    int jump(vector<int> A) {
        if (A.empty()) {
            return -1;
        }

        const int N = A.size() - 1;
        vector<int> steps(N, INT_MAX);
        steps[0] = 0;

        for (int i = 1; i != N + 1; ++i) {
            for (int j = 0; j != i; ++j) {
                if ((steps[j] != INT_MAX) && (j + A[j] >= i)) {
                    steps[i] = steps[j] + 1;
                    break;
                }
            }
        }

        return steps[N];
    }
}; 
```

### 源码分析

状态转移方程为

```
if ((steps[j] != INT_MAX) && (j + A[j] >= i)) {
    steps[i] = steps[j] + 1;
    break;
} 
```

其中 break 即体现了 MIN 操作，最开始满足条件的 j 即为最小步数。

## 题解(贪心法-自底向上)

使用动态规划解 Jump Game 的题复杂度均较高，这里可以使用贪心法达到线性级别的复杂度。

贪心法可以使用自底向上或者自顶向下，首先看看我最初使用自底向上做的。对 A 数组遍历，找到最小的下标`min_index`，并在下一轮中用此`min_index`替代上一次的`end`, 直至`min_index`为 0，返回最小跳数`jumps`。以下的实现有个 bug，细心的你能发现吗？

### C++ greedy from bottom to top, bug version

```
class Solution {
public:
    /**
     * @param A: A list of lists of integers
     * @return: An integer
     */
    int jump(vector<int> A) {
        if (A.empty()) {
            return -1;
        }

        const int N = A.size() - 1;
        int jumps = 0;
        int last_index = N;
        int min_index = N;

        for (int i = N - 1; i >= 0; --i) {
            if (i + A[i] >= last_index) {
                min_index = i;
            }

            if (0 == min_index) {
                return ++jumps;
            }

            if ((0 == i) && (min_index < last_index)) {
                ++jumps;
                last_index = min_index;
                i = last_index - 1;
            }
        }

        return jumps;
    }
}; 
```

### 源码分析

使用 jumps 记录最小跳数，last*index 记录离终点最远的坐标，min*index 记录此次遍历过程中找到的最小下标。

以上的 bug 在于当 min_index 为 1 时，i = 0, for 循环中仍有--i，因此退出循环，无法进入`if (0 == min_index)`语句，因此返回的结果会小 1 个。

### C++ greedy, from bottom to top

```
class Solution {
public:
    /**
     * @param A: A list of lists of integers
     * @return: An integer
     */
    int jump(vector<int> A) {
        if (A.empty()) {
            return 0;
        }

        const int N = A.size() - 1;
        int jumps = 0, end = N, min_index = N;

        while (end > 0) {
            for (int i = end - 1; i >= 0; --i) {
                if (i + A[i] >= end) {
                    min_index = i;
                }
            }

            if (min_index < end) {
                ++jumps;
                end = min_index;
            } else {
                // cannot jump to the end
                return -1;
            }
        }

        return jumps;
    }
}; 
```

### 源码分析

之前的 bug version 代码实在是太丑陋了，改写了个相对优雅的实现，加入了是否能到达终点的判断。在更新`min_index`的内循环中也可改为如下效率更高的方式：

```
 for (int i = 0; i != end; ++i) {
                if (i + A[i] >= end) {
                    min_index = i;
                    break;
                }
            } 
```

## 题解(贪心法-自顶向下)

看过了自底向上的贪心法，我们再来瞅瞅自顶向下的实现。自顶向下使用`farthest`记录当前坐标出发能到达的最远坐标，遍历当前`start`与`end`之间的坐标，若`i+A[i] > farthest`时更新`farthest`(寻找最小跳数)，当前循环遍历结束时递推`end = farthest`。`end >= A.size() - 1`时退出循环，返回最小跳数。

### C++

```
/**
 * http://www.jiuzhang.com/solutions/jump-game-ii/
 */
class Solution {
public:
    /**
     * @param A: A list of lists of integers
     * @return: An integer
     */
    int jump(vector<int> A) {
        if (A.empty()) {
            return 0;
        }

        const int N = A.size() - 1;
        int start = 0, end = 0, jumps = 0;

        while (end < N) {
            int farthest = end;
            for (int i = start; i <= end; ++i) {
                if (i + A[i] >= farthest) {
                    farthest = i + A[i];
                }
            }

            if (end < farthest) {
                ++jumps;
                start = end + 1;
                end = farthest;
            } else {
                // cannot jump to the end
                return -1;
            }
        }

        return jumps;
    }
}; 
```