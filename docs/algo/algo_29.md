# 第 29 节 Dynamic Programming - 动态规划（五）

## Interleaving String

## Question

*   leetcode: [Interleaving String | LeetCode OJ](https://leetcode.com/problems/interleaving-string/)
*   lintcode: [(29) Interleaving String](http://www.lintcode.com/en/problem/interleaving-string/)

```
Given three strings: s1, s2, s3,
determine whether s3 is formed by the interleaving of s1 and s2.

Example
For s1 = "aabcc", s2 = "dbbca"

When s3 = "aadbbcbcac", return true.
When s3 = "aadbbbaccc", return false.
Challenge
O(n2) time or better 
```

## 题解 1 - bug

题目意思是 s3 是否由 s1 和 s2 交叉构成，不允许跳着从 s1 和 s2 挑选字符。那么直觉上可以对三个字符串设置三个索引，首先从 s3 中依次取字符，然后进入内循环，依次从 s1 和 s2 中取首字符，若能匹配上则进入下一次循环，否则立即返回 false. 我们先看代码，再分析 bug 之处。

### Java

```
public class Solution {
    /**
     * Determine whether s3 is formed by interleaving of s1 and s2.
     * @param s1, s2, s3: As description.
     * @return: true or false.
     */
    public boolean isInterleave(String s1, String s2, String s3) {
        int len1 = (s1 == null) ? 0 : s1.length();
        int len2 = (s2 == null) ? 0 : s2.length();
        int len3 = (s3 == null) ? 0 : s3.length();

        if (len3 != len1 + len2) return false;

        int i1 = 0, i2 = 0;
        for (int i3 = 0; i3 < len3; i3++) {
            boolean result = false;
            if (i1 < len1 && s1.charAt(i1) == s3.charAt(i3)) {
                i1++;
                result = true;
                continue;
            }
            if (i2 < len2 && s2.charAt(i2) == s3.charAt(i3)) {
                i2++;
                result = true;
                continue;
            }

            // return instantly if both s1 and s2 can not pair with s3
            if (!result) return false;
        }

        return true;
    }
} 
```

### 源码分析

异常处理部分：首先求得 s1, s2, s3 的字符串长度，随后用索引 i1, i2, i3 巧妙地避开了繁琐的 null 检测。这段代码能过前面的一部分数据，但在 lintcode 的第 15 个 test 跪了。不想马上看以下分析的可以自己先 debug 下。

我们可以注意到以上代码还有一种情况并未考虑到，那就是当 s1[i1] 和 s2[i2] 均和 s3[i3] 相等时，我们可以拿 s1 或者 s2 去匹配，那么问题来了，由于不允许跳着取，那么可能出现在取了 s1 中的字符后，接下来的 s1 和 s2 首字符都无法和 s3 匹配到，因此原本应该返回 true 而现在返回 false. 建议将以上代码贴到 OJ 上看看测试用例。

以上 bug 可以通过加入对 `(s1[i1] == s3[i3]) && (s2[i2] == s3[i3])` 这一特殊情形考虑，即分两种情况递归调用 isInterleave, 只不过 s1, s2, s3 为新生成的字符串。

### 复杂度分析

遍历一次 s3, 时间复杂度为 $$O(n)$$, 空间复杂度 $$O(1)$$.

## 题解 2

在 `(s1[i1] == s3[i3]) && (s2[i2] == s3[i3])` 时分两种情况考虑，即让 s1[i1] 和 s3[i3] 配对或者 s2[i2] 和 s3[i3] 配对，那么嵌套调用时新生成的字符串则分别为 `s1[1+i1:], s2[i2], s3[1+i3:]` 和 `s1[i1:], s2[1+i2], s3[1+i3:]`. 嵌套调用结束后立即返回最终结果，因为递归调用时整个结果已经知晓，不立即返回则有可能会产生错误结果，递归调用并未影响到调用处的 i1 和 i2.

### Python

```
class Solution:
    """
    @params s1, s2, s3: Three strings as description.
    @return: return True if s3 is formed by the interleaving of
             s1 and s2 or False if not.
    @hint: you can use [[True] * m for i in range (n)] to allocate a n*m matrix.
    """
    def isInterleave(self, s1, s2, s3):
        len1 = 0 if s1 is None else len(s1)
        len2 = 0 if s2 is None else len(s2)
        len3 = 0 if s3 is None else len(s3)

        if len3 != len1 + len2:
            return False

        i1, i2 = 0, 0
        for i3 in xrange(len(s3)):
            result = False
            if (i1 < len1 and s1[i1] == s3[i3]) and \
               (i1 < len1 and s1[i1] == s3[i3]):
                # s1[1+i1:], s2[i2:], s3[1+i3:]
                case1 = self.isInterleave(s1[1 + i1:], s2[i2:], s3[1 + i3:])
                # s1[i1:], s2[1+i2:], s3[1+i3:]
                case2 = self.isInterleave(s1[i1:], s2[1 + i2:], s3[1 + i3:])
                return case1 or case2

            if i1 < len1 and s1[i1] == s3[i3]:
                i1 += 1
                result = True
                continue

            if i2 < len2 and s2[i2] == s3[i3]:
                i2 += 1
                result = True
                continue

            # return instantly if both s1 and s2 can not pair with s3
            if not result:
                return False

        return True 
```

### C++

```
class Solution {
public:
    /**
     * Determine whether s3 is formed by interleaving of s1 and s2.
     * @param s1, s2, s3: As description.
     * @return: true of false.
     */
    bool isInterleave(string s1, string s2, string s3) {
        int len1 = s1.size();
        int len2 = s2.size();
        int len3 = s3.size();

        if (len3 != len1 + len2) return false;

        int i1 = 0, i2 = 0;
        for (int i3 = 0; i3 < len3; ++i3) {
            bool result = false;
            if (i1 < len1 && s1[i1] == s3[i3] &&
                i2 < len2 && s2[i2] == s3[i3]) {
                // s1[1+i1:], s2[i2:], s3[1+i3:]
                bool case1 = isInterleave(s1.substr(1 + i1), s2.substr(i2), s3.substr(1 + i3));
                // s1[i1:], s2[1+i2:], s3[1+i3:]
                bool case2 = isInterleave(s1.substr(i1), s2.substr(1 + i2), s3.substr(1 + i3));
                // return instantly
                return case1 || case2;
            }

            if (i1 < len1 && s1[i1] == s3[i3]) {
                i1++;
                result = true;
                continue;
            }

            if (i2 < len2 && s2[i2] == s3[i3]) {
                i2++;
                result = true;
                continue;
            }

            // return instantly if both s1 and s2 can not pair with s3
            if (!result) return false;
        }

        return true;
    }
}; 
```

### Java

```
public class Solution {
    /**
     * Determine whether s3 is formed by interleaving of s1 and s2.
     * @param s1, s2, s3: As description.
     * @return: true or false.
     */
    public boolean isInterleave(String s1, String s2, String s3) {
        int len1 = (s1 == null) ? 0 : s1.length();
        int len2 = (s2 == null) ? 0 : s2.length();
        int len3 = (s3 == null) ? 0 : s3.length();

        if (len3 != len1 + len2) return false;

        int i1 = 0, i2 = 0;
        for (int i3 = 0; i3 < len3; i3++) {
            boolean result = false;
            if (i1 < len1 && s1.charAt(i1) == s3.charAt(i3) &&
                i2 < len2 && s2.charAt(i2) == s3.charAt(i3)) {
                // s1[1+i1:], s2[i2:], s3[1+i3:]
                boolean case1 = isInterleave(s1.substring(1 + i1), s2.substring(i2), s3.substring(1 + i3));
                // s1[i1:], s2[1+i2:], s3[1+i3:]
                boolean case2 = isInterleave(s1.substring(i1), s2.substring(1 + i2), s3.substring(1 + i3));
                // return instantly
                return case1 || case2;
            }

            if (i1 < len1 && s1.charAt(i1) == s3.charAt(i3)) {
                i1++;
                result = true;
                continue;
            }

            if (i2 < len2 && s2.charAt(i2) == s3.charAt(i3)) {
                i2++;
                result = true;
                continue;
            }

            // return instantly if both s1 and s2 can not pair with s3
            if (!result) return false;
        }

        return true;
    }
} 
```

## 题解 3 - 动态规划

看过题解 1 和 题解 2 的思路后动规的状态和状态方程应该就不难推出了。按照经典的序列规划，不妨假设状态 f[i1][i2][i3] 为 s1 的前 i1 个字符和 s2 的前 i2 个字符是否能交叉构成 s3 的前 i3 个字符，那么根据 s1[i1], s2[i2], s3[i3]的匹配情况可以分为 8 种情况讨论。咋一看这似乎十分麻烦，但实际上我们注意到其实还有一个隐含条件：`len3 == len1 + len2`, 故状态转移方程得到大幅简化。

新的状态可定义为 f[i1][i2], 含义为 s1 的前`i1`个字符和 s2 的前 `i2`个字符是否能交叉构成 s3 的前 `i1 + i2` 个字符。根据 `s1[i1] == s3[i3]` 和 `s2[i2] == s3[i3]` 的匹配情况可建立状态转移方程为：

```
f[i1][i2] = (s1[i1 - 1] == s3[i1 + i2 - 1] && f[i1 - 1][i2]) ||
            (s2[i2 - 1] == s3[i1 + i2 - 1] && f[i1][i2 - 1]) 
```

这道题的初始化有点 trick, 考虑到空串的可能，需要单独初始化 `f[*][0]` 和 `f[0][*]`.

### Python

```
class Solution:
    """
    @params s1, s2, s3: Three strings as description.
    @return: return True if s3 is formed by the interleaving of
             s1 and s2 or False if not.
    @hint: you can use [[True] * m for i in range (n)] to allocate a n*m matrix.
    """
    def isInterleave(self, s1, s2, s3):
        len1 = 0 if s1 is None else len(s1)
        len2 = 0 if s2 is None else len(s2)
        len3 = 0 if s3 is None else len(s3)

        if len3 != len1 + len2:
            return False

        f = [[True] * (1 + len2) for i in xrange (1 + len1)]
        # s1[i1 - 1] == s3[i1 + i2 - 1] && f[i1 - 1][i2]
        for i in xrange(1, 1 + len1):
            f[i][0] = s1[i - 1] == s3[i - 1] and f[i - 1][0]
        # s2[i2 - 1] == s3[i1 + i2 - 1] && f[i1][i2 - 1]
        for i in xrange(1, 1 + len2):
            f[0][i] = s2[i - 1] == s3[i - 1] and f[0][i - 1]
        # i1 >= 1, i2 >= 1
        for i1 in xrange(1, 1 + len1):
            for i2 in xrange(1, 1 + len2):
                case1 = s1[i1 - 1] == s3[i1 + i2 - 1] and f[i1 - 1][i2]
                case2 = s2[i2 - 1] == s3[i1 + i2 - 1] and f[i1][i2 - 1]
                f[i1][i2] = case1 or case2

        return f[len1][len2] 
```

### C++

```
class Solution {
public:
    /**
     * Determine whether s3 is formed by interleaving of s1 and s2.
     * @param s1, s2, s3: As description.
     * @return: true of false.
     */
    bool isInterleave(string s1, string s2, string s3) {
        int len1 = s1.size();
        int len2 = s2.size();
        int len3 = s3.size();

        if (len3 != len1 + len2) return false;

        vector<vector<bool> > f(1 + len1, vector<bool>(1 + len2, true));
        // s1[i1 - 1] == s3[i1 + i2 - 1] && f[i1 - 1][i2]
        for (int i = 1; i <= len1; ++i) {
            f[i][0] = s1[i - 1] == s3[i - 1] && f[i - 1][0];
        }
        // s2[i2 - 1] == s3[i1 + i2 - 1] && f[i1][i2 - 1]
        for (int i = 1; i <= len2; ++i) {
            f[0][i] = s2[i - 1] == s3[i - 1] && f[0][i - 1];
        }
        // i1 >= 1, i2 >= 1
        for (int i1 = 1; i1 <= len1; ++i1) {
            for (int i2 = 1; i2 <= len2; ++i2) {
                bool case1 = s1[i1 - 1] == s3[i1 + i2 - 1] && f[i1 - 1][i2];
                bool case2 = s2[i2 - 1] == s3[i1 + i2 - 1] && f[i1][i2 - 1];
                f[i1][i2] = case1 || case2;
            }
        }

        return f[len1][len2];
    }
}; 
```

### Java

```
public class Solution {
    /**
     * Determine whether s3 is formed by interleaving of s1 and s2.
     * @param s1, s2, s3: As description.
     * @return: true or false.
     */
    public boolean isInterleave(String s1, String s2, String s3) {
        int len1 = (s1 == null) ? 0 : s1.length();
        int len2 = (s2 == null) ? 0 : s2.length();
        int len3 = (s3 == null) ? 0 : s3.length();

        if (len3 != len1 + len2) return false;

        boolean [][] f = new boolean[1 + len1][1 + len2];
        f[0][0] = true;
        // s1[i1 - 1] == s3[i1 + i2 - 1] && f[i1 - 1][i2]
        for (int i = 1; i <= len1; i++) {
            f[i][0] = s1.charAt(i - 1) == s3.charAt(i - 1) && f[i - 1][0];
        }
        // s2[i2 - 1] == s3[i1 + i2 - 1] && f[i1][i2 - 1]
        for (int i = 1; i <= len2; i++) {
            f[0][i] = s2.charAt(i - 1) == s3.charAt(i - 1) && f[0][i - 1];
        }
        // i1 >= 1, i2 >= 1
        for (int i1 = 1; i1 <= len1; i1++) {
            for (int i2 = 1; i2 <= len2; i2++) {
                boolean case1 = s1.charAt(i1 - 1) == s3.charAt(i1 + i2 - 1) && f[i1 - 1][i2];
                boolean case2 = s2.charAt(i2 - 1) == s3.charAt(i1 + i2 - 1) && f[i1][i2 - 1];
                f[i1][i2] = case1 || case2;
            }
        }

        return f[len1][len2];
    }
} 
```

### 源码分析

为后面递推方便，初始化时数组长度多加 1，for 循环时需要注意边界(取到等号)。

### 复杂度分析

双重 for 循环，时间复杂度为 $$O(n²)$$, 使用了二维矩阵，空间复杂度 $$O(n²)$$. 其中空间复杂度可以优化。

## Reference

*   soulmachine 的 Interleaving String 部分
*   [Interleaving String 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/interleaving-string/)

## Maximum Subarray

## Question

*   leetcode: [Maximum Subarray | LeetCode OJ](https://leetcode.com/problems/maximum-subarray/)
*   lintcode: [(41) Maximum Subarray](http://www.lintcode.com/en/problem/maximum-subarray/)

```
Given an array of integers,
find a contiguous subarray which has the largest sum.

Example
Given the array [−2,2,−3,4,−1,2,1,−5,3],
the contiguous subarray [4,−1,2,1] has the largest sum = 6.

Note
The subarray should contain at least one number.

Challenge
Can you do it in time complexity O(n)? 
```

## 题解 1 - 贪心

求最大子数组和，即求区间和的最大值，不同子区间共有约 $$n²$$ 中可能，遍历虽然可解，但是时间复杂度颇高。

这里首先介绍一种巧妙的贪心算法，用`sum`表示当前子数组和，`maxSum`表示求得的最大子数组和。当`sum <= 0`时，累加数组中的元素只会使得到的和更小，故此时应将此部分和丢弃，使用此时遍历到的数组元素替代。需要注意的是由于有`maxSum`更新`sum`, 故直接丢弃小于 0 的`sum`并不会对最终结果有影响。即不会漏掉前面的和比后面的元素大的情况。

### Java

```
public class Solution {
    /**
     * @param nums: A list of integers
     * @return: A integer indicate the sum of max subarray
     */
    public int maxSubArray(ArrayList<Integer> nums) {
        // -1 is not proper for illegal input
        if (nums == null || nums.isEmpty()) return -1;

        int sum = 0, maxSub = Integer.MIN_VALUE;
        for (int num : nums) {
            // drop negtive sum
            sum = Math.max(sum, 0);
            sum += num;
            // update maxSub
            maxSub = Math.max(maxSub, sum);
        }

        return maxSub;
    }
} 
```

### 源码分析

贪心的实现较为巧妙，需要`sum`和`maxSub`配合运作才能正常工作。

### 复杂度分析

遍历一次数组，时间复杂度 $$O(n)$$, 使用了几个额外变量，空间复杂度 $$O(1)$$.

## 题解 2 - 动态规划 1(区间和)

求最大/最小这种字眼往往都可以使用动态规划求解，此题为单序列动态规划。我们可以先求出到索引 i 的子数组和，然后用子数组和的最大值减去最小值，最后返回最大值即可。用这种动态规划需要注意初始化条件和求和顺序。

### Java

```
public class Solution {
    /**
     * @param nums: A list of integers
     * @return: A integer indicate the sum of max subarray
     */
    public int maxSubArray(ArrayList<Integer> nums) {
        // -1 is not proper for illegal input
        if (nums == null || nums.isEmpty()) return -1;

        int sum = 0, minSum = 0, maxSub = Integer.MIN_VALUE;
        for (int num : nums) {
            minSum = Math.min(minSum, sum);
            sum += num;
            maxSub = Math.max(maxSub, sum - minSum);
        }

        return maxSub;
    }
} 
```

### 源码分析

首先求得当前的最小子数组和，初始化为 0，随后比较子数组和减掉最小子数组和的差值和最大区间和，并更新最大区间和。

### 复杂度分析

时间复杂度 $$O(n)$$, 使用了类似滚动数组的处理方式，空间复杂度 $$O(1)$$.

## 题解 3 - 动态规划 2(局部与全局)

这种动规的实现和题解 1 的思想几乎一模一样，只不过这里用局部最大值和全局最大值两个数组来表示。

### Java

```
public class Solution {
    /**
     * @param nums: A list of integers
     * @return: A integer indicate the sum of max subarray
     */
    public int maxSubArray(ArrayList<Integer> nums) {
        // -1 is not proper for illegal input
        if (nums == null || nums.isEmpty()) return -1;

        int size = nums.size();
        int[] local = new int[size];
        int[] global = new int[size];
        local[0] = nums.get(0);
        global[0] = nums.get(0);
        for (int i = 1; i < size; i++) {
            // drop local[i - 1] < 0
            local[i] = Math.max(nums.get(i), local[i - 1] + nums.get(i));
            // update global with local
            global[i] = Math.max(global[i - 1], local[i]);
        }

        return global[size - 1];
    }
} 
```

### 源码分析

由于局部最大值需要根据之前的局部值是否大于 0 进行更新，故方便起见初始化 local 和 global 数组的第一个元素为数组第一个元素。

### 复杂度分析

时间复杂度 $$O(n)$$, 空间复杂度也为 $$O(n)$$.

## Reference

*   《剑指 Offer》第五章
*   [Maximum Subarray 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/maximum-subarray/)

## Maximum Subarray II

## Question

*   lintcode: [(42) Maximum Subarray II](http://www.lintcode.com/en/problem/maximum-subarray-ii/)

```
Given an array of integers,
find two non-overlapping subarrays which have the largest sum.

The number in each subarray should be contiguous.

Return the largest sum.

Example
For given [1, 3, -1, 2, -1, 2],
the two subarrays are [1, 3] and [2, -1, 2] or [1, 3, -1, 2] and [2],
they both have the largest sum 7.

Note
The subarray should contain at least one number

Challenge
Can you do it in time complexity O(n) ? 
```

## 题解

严格来讲这道题这道题也可以不用动规来做，这里还是采用经典的动规解法。[Maximum Subarray](http://algorithm.yuanbin.me/zh-hans/dynamic_programming/maximum_subarray.html) 中要求的是数组中最大子数组和，这里是求不相重叠的两个子数组和的和最大值，做过买卖股票系列的题的话这道题就非常容易了，既然我们已经求出了单一子数组的最大和，那么我们使用隔板法将数组一分为二，分别求这两段的最大子数组和，求相加后的最大值即为最终结果。隔板前半部分的最大子数组和很容易求得，但是后半部分难道需要将索引从 0 开始依次计算吗？NO!!! 我们可以采用从后往前的方式进行遍历，这样时间复杂度就大大降低了。

### Java

```
public class Solution {
    /**
     * @param nums: A list of integers
     * @return: An integer denotes the sum of max two non-overlapping subarrays
     */
    public int maxTwoSubArrays(ArrayList<Integer> nums) {
        // -1 is not proper for illegal input
        if (nums == null || nums.isEmpty()) return -1;

        int size = nums.size();
        // get max sub array forward
        int[] maxSubArrayF = new int[size];
        forwardTraversal(nums, maxSubArrayF);
        // get max sub array backward
        int[] maxSubArrayB = new int[size];
        backwardTraversal(nums, maxSubArrayB);
        // get maximum subarray by iteration
        int maxTwoSub = Integer.MIN_VALUE;
        for (int i = 0; i < size - 1; i++) {
            // non-overlapping
            maxTwoSub = Math.max(maxTwoSub, maxSubArrayF[i] + maxSubArrayB[i + 1]);
        }

        return maxTwoSub;
    }

    private void forwardTraversal(List<Integer> nums, int[] maxSubArray) {
        int sum = 0, minSum = 0, maxSub = Integer.MIN_VALUE;
        int size = nums.size();
        for (int i = 0; i < size; i++) {
            minSum = Math.min(minSum, sum);
            sum += nums.get(i);
            maxSub = Math.max(maxSub, sum - minSum);
            maxSubArray[i] = maxSub;
        }
    }

    private void backwardTraversal(List<Integer> nums, int[] maxSubArray) {
        int sum = 0, minSum = 0, maxSub = Integer.MIN_VALUE;
        int size = nums.size();
        for (int i = size - 1; i >= 0; i--) {
            minSum = Math.min(minSum, sum);
            sum += nums.get(i);
            maxSub = Math.max(maxSub, sum - minSum);
            maxSubArray[i] = maxSub;
        }
    }
} 
```

### 源码分析

前向搜索和逆向搜索我们使用私有方法实现，可读性更高。注意是求非重叠子数组和，故求`maxTwoSub`时 i 的范围为`0, size - 2`, 前向数组索引为 i, 后向索引为 i + 1.

### 复杂度分析

前向和后向搜索求得最大子数组和，时间复杂度 $$O(2n)=O(n)$$, 空间复杂度 $$O(n)$$. 遍历子数组和的数组求最终两个子数组和的最大值，时间复杂度 $$O(n)$$. 故总的时间复杂度为 $$O(n)$$, 空间复杂度 $$O(n)$$.

## Longest Increasing Continuous subsequence

## Question

*   lintcode: [(397) Longest Increasing Continuous subsequence](http://www.lintcode.com/en/problem/longest-increasing-continuous-subsequence/)

### Problem Statement

Give you an integer array (index from 0 to n-1, where n is the size of this array)，find the longest increasing continuous subsequence in this array. (The definition of the longest increasing continuous subsequence here can be from right to left or from left to right)

#### Example

For `[5, 4, 2, 1, 3]`, the LICS is `[5, 4, 2, 1]`, return 4.

For `[5, 1, 2, 3, 4]`, the LICS is `[1, 2, 3, 4]`, return 4.

#### Note

O(n) time and O(1) extra space.

## 题解 1

题目只要返回最大长度，注意此题中的连续递增指的是双向的，即可递增也可递减。简单点考虑可分两种情况，一种递增，另一种递减，跟踪最大递增长度，最后返回即可。也可以在一个 for 循环中搞定，只不过需要增加一布尔变量判断之前是递增还是递减。

### Java - two for loop

```
public class Solution {
    /**
     * @param A an array of Integer
     * @return  an integer
     */
    public int longestIncreasingContinuousSubsequence(int[] A) {
        if (A == null || A.length == 0) return 0;

        int lics = 1, licsMax = 1, prev = A[0];
        // ascending order
        for (int a : A) {
            lics = (prev < a) ? lics + 1 : 1;
            licsMax = Math.max(licsMax, lics);
            prev = a;
        }
        // reset
        lics = 1;
        prev = A[0];
        // descending order
        for (int a : A) {
            lics = (prev > a) ? lics + 1 : 1;
            licsMax = Math.max(licsMax, lics);
            prev = a;
        }

        return licsMax;
    }
} 
```

### Java - one for loop

```
public class Solution {
    /**
     * @param A an array of Integer
     * @return  an integer
     */
    public int longestIncreasingContinuousSubsequence(int[] A) {
        if (A == null || A.length == 0) return 0;

        int start = 0, licsMax = 1;
        boolean ascending = false;
        for (int i = 1; i < A.length; i++) {
            // ascending order
            if (A[i - 1] < A[i]) {
                if (!ascending) {
                    ascending = true;
                    start = i - 1;
                }
            } else if (A[i - 1] > A[i]) {
            // descending order
                if (ascending) {
                    ascending = false;
                    start = i - 1;
                }
            } else {
                start = i - 1;
            }
            licsMax = Math.max(licsMax, i - start + 1);
        }

        return licsMax;
    }
} 
```

### 源码分析

使用两个 for 循环时容易在第二次循环忘记重置。使用一个 for 循环时使用下标来计数较为方便。

### 复杂度分析

时间复杂度 $$O(n)$$, 空间复杂度 $$O(1)$$.

## 题解 2 - 动态规划

除了题解 1 中分两种情况讨论外，我们还可以使用动态规划求解。状态转移方程容易得到——要么向右增长，要么向左增长。相应的状态`dp[i]`即为从索引 i 出发所能得到的最长连续递增子序列。这样就避免了分两个循环处理了，这种思想对此题的 follow up 有特别大的帮助。

### Java

```
public class Solution {
    /**
     * @param A an array of Integer
     * @return  an integer
     */
    public int longestIncreasingContinuousSubsequence(int[] A) {
        if (A == null || A.length == 0) return 0;

        int lics = 0;
        int[] dp = new int[A.length];
        for (int i = 0; i < A.length; i++) {
            if (dp[i] == 0) {
                lics = Math.max(lics, dfs(A, i, dp));
            }
        }

        return lics;
    }

    private int dfs(int[] A, int i, int[] dp) {
        if (dp[i] != 0) return dp[i];

        // increasing from xxx to left, right
        int left = 0, right = 0;
        // increasing from right to left
        if (i > 0 && A[i - 1] > A[i]) left = dfs(A, i - 1, dp);
        // increasing from left to right
        if (i + 1 < A.length && A[i + 1] > A[i]) right = dfs(A, i + 1, dp);

        dp[i] = 1 + Math.max(left, right);
        return dp[i];
    }
} 
```

### 源码分析

dfs 中使用记忆化存储避免重复递归，分左右两个方向递增，最后取较大值。这种方法对于数组长度较长时栈会溢出。

### 复杂度分析

时间复杂度 $$O(n)$$, 空间复杂度 $$(n)$$.

## Reference

*   [Lintcode: Longest Increasing Continuous subsequence | codesolutiony](https://codesolutiony.wordpress.com/2015/05/25/lintcode-longest-increasing-continuous-subsequence/)

## Longest Increasing Continuous subsequence II

## Question

*   lintcode: [(398) Longest Increasing Continuous subsequence II](http://www.lintcode.com/en/problem/longest-increasing-continuous-subsequence-ii/)

### Problem Statement

Give you an integer matrix (with row size n, column size m)，find the longest increasing continuous subsequence in this matrix. (The definition of the longest increasing continuous subsequence here can start at any row or column and go up/down/right/left any direction).

#### Example

Given a matrix:

```
[
  [1 ,2 ,3 ,4 ,5],
  [16,17,24,23,6],
  [15,18,25,22,7],
  [14,19,20,21,8],
  [13,12,11,10,9]
] 
```

return 25

#### Challenge

O(nm) time and memory.

## 题解

题 [Longest Increasing Continuous subsequence](http://algorithm.yuanbin.me/zh-hans/dynamic_programming/longest_increasing_continuous_subsequence.html) 的 follow up, 变成一道比较难的题了。从之前的一维 DP 变为现在的二维 DP，自增方向可从上下左右四个方向进行。需要结合 DFS 和动态规划两大重量级武器。

根据二维 DP 的通用方法，我们首先需要关注状态及状态转移方程，状态转移方程相对明显一点，即上下左右四个方向的元素值递增关系，根据此转移方程，**不难得到我们需要的状态为`dp[i][j]`——表示从坐标`(i, j)`出发所得到的最长连续递增子序列。**根据状态及转移方程我们不难得到初始化应该为 1 或者 0，这要视具体情况而定。

这里我们可能会纠结的地方在于自增的方向，平时见到的二维 DP 自增方向都是从小到大，而这里的增长方向却不一定。**这里需要突破思维定势的地方在于我们可以不理会从哪个方向自增，只需要处理自增和边界条件即可。**根据转移方程可以知道使用递归来解决是比较好的方式，这里关键的地方就在于递归的终止条件。比较容易想到的一个递归终止条件自然是当前元素是整个矩阵中的最大元素，索引朝四个方向出发都无法自增，因此返回 1\. 另外可以预想到的是如果不进行记忆化存储，递归过程中自然会产生大量重复计算，根据记忆化存储的通用方法，这里可以以结果是否为 0(初始化为 0 时)来进行区分。

### Java

```
public class Solution {
    /**
     * @param A an integer matrix
     * @return  an integer
     */
    public int longestIncreasingContinuousSubsequenceII(int[][] A) {
        if (A == null || A.length == 0 || A[0].length == 0) return 0;

        int lics = 0;
        int[][] dp = new int[A.length][A[0].length];
        for (int row = 0; row < A.length; row++) {
            for (int col = 0; col < A[0].length; col++) {
                if (dp[row][col] == 0) {
                    lics = Math.max(lics, dfs(A, row, col, dp));
                }
            }
        }

        return lics;
    }

    private int dfs(int[][] A, int row, int col, int[][] dp) {
        if (dp[row][col] != 0) {
            return dp[row][col];
        }

        // increasing from xxx to up, down, left, right
        int up = 0, down = 0, left = 0, right = 0;
        // increasing from down to up
        if (row > 0 && A[row - 1][col] > A[row][col]) {
            up = dfs(A, row - 1, col, dp);
        }
        // increasing from up to down
        if (row + 1 < A.length && A[row + 1][col] > A[row][col]) {
            down = dfs(A, row + 1, col, dp);
        }
        // increasing from right to left
        if (col > 0 && A[row][col - 1] > A[row][col]) {
            left = dfs(A, row, col - 1, dp);
        }
        // increasing from left to right
        if (col + 1 < A[0].length && A[row][col + 1] > A[row][col]) {
            right = dfs(A, row, col + 1, dp);
        }
        // return maximum of up, down, left, right
        dp[row][col] = 1 + Math.max(Math.max(up, down), Math.max(left, right));

        return dp[row][col];
    }
} 
```

### 源码分析

dfs 递归最深一层即矩阵中最大的元素处，然后逐层返回。这道题对状态`dp[i][j]`的理解很重要，否则会陷入对上下左右四个方向的迷雾中。

### 复杂度分析

由于引入了记忆化存储，时间复杂度逼近 $$O(mn)$$, 空间复杂度 $$O(mn)$$.

## Reference

*   [Lintcode: Longest Increasing Continuous subsequence II | codesolutiony](https://codesolutiony.wordpress.com/2015/05/25/lintcode-longest-increasing-continuous-subsequence-ii/)

## Maximal Square

## Question

*   leetcode: [Maximal Square | LeetCode OJ](https://leetcode.com/problems/maximal-square/)
*   lintcode: [Maximal Square](http://www.lintcode.com/en/problem/maximal-square/)

### Problem Statement

Given a 2D binary matrix filled with 0's and 1's, find the largest square containing all 1's and return its area.

#### Example

For example, given the following matrix:

```
1 0 1 0 0
1 0 1 1 1
1 1 1 1 1
1 0 0 1 0 
```

Return `4`.

## 题解

第一次遇到这个题是在嘀嘀打车现场面试中，首先把题意理解错了，而且动态规划的状态定义错了，没搞出来... 所以说明确题意非常重要！

题意是问矩阵中子正方形（不是长方形）的最大面积。也就是说我们的思路应该是去判断正方形这一子状态以及相应的状态转移方程。正方形的可能有边长为 1，2，3 等等... 边长为 2 的可由边长为 1 的转化而来，边长为 3 的可由边长为 2 的转化而来。那么问题来了，边长的转化是如何得到的？边长由 1 变为 2 容易得知，即左上、左边以及上边的值均为 1，边长由 2 变为 3 这一状态转移方程不容易直接得到。直观上来讲，我们需要边长为 3 的小正方形内格子中的数均为 1\. **抽象来讲也可以认为边长为 3 的正方形是由若干个边长为 2 的正方形堆叠得到的，这就是这道题的核心状态转移方程。**

令状态`dp[i][j]`表示为从左上角(不一定是`(0,0)`)到矩阵中坐标`(i,j)`为止能构成正方形的最大边长。那么有如下状态转移方程：

```
dp[i][j] = min(dp[i-1][j-1], dp[i-1][j], dp[i][j-1]) + 1; if matrix[i][j] == 1
dp[i][j] = 0; if matrix[i][j] = 0 
```

初始化直接用第一行和第一列即可。

### Java

```
public class Solution {
    /**
     * @param matrix: a matrix of 0 and 1
     * @return: an integer
     */
    public int maxSquare(int[][] matrix) {
        int side = 0;
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return side;
        }

        final int ROW = matrix.length, COL = matrix[0].length;
        int[][] dp = new int[ROW][COL];
        for (int i = 0; i < ROW; i++) {
            dp[i][0] = matrix[i][0];
            side = 1;
        }
        for (int i = 0; i < COL; i++) {
            dp[0][i] = matrix[0][i];
            side = 1;
        }

        for (int i = 1; i < ROW; i++) {
            side = Math.max(side, matrix[i][0]);
            for (int j = 1; j < COL; j++) {
                if (matrix[i][j] == 1) {
                    dp[i][j] = 1 + minTri(dp[i-1][j-1], dp[i-1][j], dp[i][j-1]);
                    side = Math.max(side, dp[i][j]);
                }
            }
        }

        return side * side;
    }

    private int minTri(int a, int b, int c) {
        return Math.min(a, Math.min(b, c));
    }
} 
```

### 源码分析

经典的动规实现三步走。先初始化，后转移方程，最后对结果做必要的处理（边长 side 的更新）。

### 复杂度分析

使用了二维矩阵，空间复杂度 $$O(mn)$$. 遍历一次原矩阵，时间复杂度 $$O(mn)$$.

### Follow up

题目问的是子正方形，如果问的是矩形呢？

转移方程仍然可以不变，但是遍历完之后需要做进一步处理，比如如果不是正方形的话可能会出现多个相同的边长值，此时需要对相同的边长值递增(按行或者按列)，相乘后保存，最后取最大输出。

## Reference

*   [Maximum size square sub-matrix with all 1s - GeeksforGeeks](http://www.geeksforgeeks.org/maximum-size-sub-matrix-with-all-1s-in-a-binary-matrix/)
*   [maximal-square/ 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/maximal-square/) - 空间复杂度可进一步优化(只保存最近的两行即可)