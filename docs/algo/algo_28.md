# 第 28 节 Dynamic Programming - 动态规划（四）

## Best Time to Buy and Sell Stock

## Question

*   leetcode: [Best Time to Buy and Sell Stock | LeetCode OJ](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)
*   lintcode: [(149) Best Time to Buy and Sell Stock](http://www.lintcode.com/en/problem/best-time-to-buy-and-sell-stock/)

```
Say you have an array for
which the ith element is the price of a given stock on day i.

If you were only permitted to complete at most one transaction
(ie, buy one and sell one share of the stock),
design an algorithm to find the maximum profit.

Example
Given an example [3,2,3,1,2], return 1 
```

## 题解

最多只允许进行一次交易，显然我们只需要把波谷和波峰分别找出来就好了。但是这样的话问题又来了，有多个波峰和波谷时怎么办？——找出差值最大的一对波谷和波峰。故需要引入一个索引用于记录当前的波谷，结果即为当前索引值减去波谷的值。

### Python

```
class Solution:
    """
    @param prices: Given an integer array
    @return: Maximum profit
    """
    def maxProfit(self, prices):
        if prices is None or len(prices) <= 1:
            return 0

        profit = 0
        cur_price_min = 2**31 - 1
        for price in prices:
            profit = max(profit, price - cur_price_min)
            cur_price_min = min(cur_price_min, price)

        return profit 
```

### C++

```
class Solution {
public:
    /**
     * @param prices: Given an integer array
     * @return: Maximum profit
     */
    int maxProfit(vector<int> &prices) {
        if (prices.size() <= 1) return 0;

        int profit = 0;
        int cur_price_min = INT_MAX;
        for (int i = 0; i < prices.size(); ++i) {
            profit = max(profit, prices[i] - cur_price_min);
            cur_price_min = min(cur_price_min, prices[i]);
        }

        return profit;
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param prices: Given an integer array
     * @return: Maximum profit
     */
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length <= 1) return 0;

        int profit = 0;
        int curPriceMin = Integer.MAX_VALUE;
        for (int price : prices) {
            profit = Math.max(profit, price - curPriceMin);
            curPriceMin = Math.min(curPriceMin, price);
        }

        return profit;
    }
} 
```

### 源码分析

善用`max`和`min`函数，减少`if`的使用。

### 复杂度分析

遍历一次 prices 数组，时间复杂度为 $$O(n)$$, 使用了几个额外变量，空间复杂度为 $$O(1)$$.

## Reference

*   soulmachine 的卖股票系列

## Best Time to Buy and Sell Stock II

## Question

*   leetcode: [Best Time to Buy and Sell Stock II | LeetCode OJ](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)
*   lintcode: [(150) Best Time to Buy and Sell Stock II](http://www.lintcode.com/en/problem/best-time-to-buy-and-sell-stock-ii/)

```
Say you have an array for
which the ith element is the price of a given stock on day i.

Design an algorithm to find the maximum profit.
You may complete as many transactions as you like
(ie, buy one and sell one share of the stock multiple times).
However, you may not engage in multiple transactions at the same time
(ie, you must sell the stock before you buy again).

Example
Given an example [2,1,2,0,1], return 2 
```

## 题解

卖股票系列之二，允许进行多次交易，但是不允许同时进行多笔交易。直觉上我们可以找到连续的多对波谷波峰，在波谷买入，波峰卖出，稳赚不赔~ 那么这样是否比只在一个差值最大的波谷波峰处交易赚的多呢？即比上题的方案赚的多。简单的证明可先假设存在一单调上升区间，若人为改变单调区间使得区间内存在不少于一对波谷波峰，那么可以得到进行两次交易的差值之和比单次交易大，证毕。

好了，思路知道了——计算所有连续波谷波峰的差值之和。需要遍历求得所有波谷波峰的值吗？我最开始还真是这么想的，看了 soulmachine 的题解才发现原来可以把数组看成时间序列，只需要计算相邻序列的差值即可，只累加大于 0 的差值。

### Python

```
class Solution:
    """
    @param prices: Given an integer array
    @return: Maximum profit
    """
    def maxProfit(self, prices):
        if prices is None or len(prices) <= 1:
            return 0

        profit = 0
        for i in xrange(1, len(prices)):
            diff = prices[i] - prices[i - 1]
            if diff > 0:
                profit += diff

        return profit 
```

### C++

```
class Solution {
public:
    /**
     * @param prices: Given an integer array
     * @return: Maximum profit
     */
    int maxProfit(vector<int> &prices) {
        if (prices.size() <= 1) return 0;

        int profit = 0;
        for (int i = 1; i < prices.size(); ++i) {
            int diff = prices[i] - prices[i - 1];
            if (diff > 0) profit += diff;
        }

        return profit;
    }
}; 
```

### Java

```
class Solution {
    /**
     * @param prices: Given an integer array
     * @return: Maximum profit
     */
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length <= 1) return 0;

        int profit = 0;
        for (int i = 1; i < prices.length; i++) {
            int diff = prices[i] - prices[i - 1];
            if (diff > 0) profit += diff;
        }

        return profit;
    }
}; 
```

### 源码分析

核心在于将多个波谷波峰的差值之和的计算转化为相邻序列的差值，故 i 从 1 开始算起。

### 复杂度分析

遍历一次原数组，时间复杂度为 $$O(n)$$, 用了几个额外变量，空间复杂度为 $$O(1)$$.

## Reference

*   soulmachine 的卖股票系列

## Best Time to Buy and Sell Stock III

## Question

*   leetcode: [Best Time to Buy and Sell Stock III | LeetCode OJ](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/)
*   lintcode: [(151) Best Time to Buy and Sell Stock III](http://www.lintcode.com/en/problem/best-time-to-buy-and-sell-stock-iii/)

```
Say you have an array for
which the ith element is the price of a given stock on day i.

Design an algorithm to find the maximum profit.
You may complete at most two transactions.

Example
Given an example [4,4,6,1,1,4,2,5], return 6.

Note
You may not engage in multiple transactions at the same time
(ie, you must sell the stock before you buy again). 
```

## 题解

与前两道允许一次或者多次交易不同，这里只允许最多两次交易，且这两次交易不能交叉。咋一看似乎无从下手，我最开始想到的是找到排在前 2 个的波谷波峰，计算这两个差值之和。原理上来讲应该是可行的，但是需要记录 $$O(n²)$$ 个波谷波峰并对其排序，实现起来也比较繁琐。

除了以上这种直接分析问题的方法外，是否还可以借助分治的思想解决呢？最多允许两次不相交的交易，也就意味着这两次交易间存在某一分界线，考虑到可只交易一次，也可交易零次，故分界线的变化范围为第一天至最后一天，只需考虑分界线两边各自的最大利润，最后选出利润和最大的即可。

这种方法抽象之后则为首先将 [1,n] 拆分为 [1,i] 和 [i+1,n], 参考卖股票系列的第一题计算各自区间内的最大利润即可。[1,i] 区间的最大利润很好算，但是如何计算 [i+1,n] 区间的最大利润值呢？难道需要重复 n 次才能得到？注意到区间的右侧 n 是个不变值，我们从 [1, i] 计算最大利润是更新波谷的值，那么我们可否逆序计算最大利润呢？这时候就需要更新记录波峰的值了。逆向思维大法好！Talk is cheap, show me the code!

### Python

```
class Solution:
    """
    @param prices: Given an integer array
    @return: Maximum profit
    """
    def maxProfit(self, prices):
        if prices is None or len(prices) <= 1:
            return 0

        n = len(prices)
        # get profit in the front of prices
        profit_front = [0] * n
        valley = prices[0]
        for i in xrange(1, n):
            profit_front[i] = max(profit_front[i - 1], prices[i] - valley)
            valley = min(valley, prices[i])
        # get profit in the back of prices, (i, n)
        profit_back = [0] * n
        peak = prices[-1]
        for i in xrange(n - 2, -1, -1):
            profit_back[i] = max(profit_back[i + 1], peak - prices[i])
            peak = max(peak, prices[i])
        # add the profit front and back
        profit = 0
        for i in xrange(n):
            profit = max(profit, profit_front[i] + profit_back[i])

        return profit 
```

### C++

```
class Solution {
public:
    /**
     * @param prices: Given an integer array
     * @return: Maximum profit
     */
    int maxProfit(vector<int> &prices) {
        if (prices.size() <= 1) return 0;

        int n = prices.size();
        // get profit in the front of prices
        vector<int> profit_front = vector<int>(n, 0);
        for (int i = 1, valley = prices[0]; i < n; ++i) {
            profit_front[i] = max(profit_front[i - 1], prices[i] - valley);
            valley = min(valley, prices[i]);
        }
        // get profit in the back of prices, (i, n)
        vector<int> profit_back = vector<int>(n, 0);
        for (int i = n - 2, peak = prices[n - 1]; i >= 0; --i) {
            profit_back[i] = max(profit_back[i + 1], peak - prices[i]);
            peak = max(peak, prices[i]);
        }
        // add the profit front and back
        int profit = 0;
        for (int i = 0; i < n; ++i) {
            profit = max(profit, profit_front[i] + profit_back[i]);
        }

        return profit;
    }
}; 
```

### Java

```
class Solution {
    /**
     * @param prices: Given an integer array
     * @return: Maximum profit
     */
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length <= 1) return 0;

        // get profit in the front of prices
        int[] profitFront = new int[prices.length];
        profitFront[0] = 0;
        for (int i = 1, valley = prices[0]; i < prices.length; i++) {
            profitFront[i] = Math.max(profitFront[i - 1], prices[i] - valley);
            valley = Math.min(valley, prices[i]);
        }
        // get profit in the back of prices, (i, n)
        int[] profitBack = new int[prices.length];
        profitBack[prices.length - 1] = 0;
        for (int i = prices.length - 2, peak = prices[prices.length - 1]; i >= 0; i--) {
            profitBack[i] = Math.max(profitBack[i + 1], peak - prices[i]);
            peak = Math.max(peak, prices[i]);
        }
        // add the profit front and back
        int profit = 0;
        for (int i = 0; i < prices.length; i++) {
            profit = Math.max(profit, profitFront[i] + profitBack[i]);
        }

        return profit;
    }
}; 
```

### 源码分析

整体分为三大部分，计算前半部分的最大利润值，然后计算后半部分的最大利润值，最后遍历得到最终的最大利润值。

### 复杂度分析

三次遍历原数组，时间复杂度为 $$O(n)$$, 利用了若干和数组等长的数组，空间复杂度也为 $$O(n)$$.

## Reference

*   soulmachine 的卖股票系列

## Best Time to Buy and Sell Stock IV

## Question

*   leetcode: [Best Time to Buy and Sell Stock IV | LeetCode OJ](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/)
*   lintcode: [(393) Best Time to Buy and Sell Stock IV](http://www.lintcode.com/en/problem/best-time-to-buy-and-sell-stock-iv/)

```
Say you have an array for
which the ith element is the price of a given stock on day i.

Design an algorithm to find the maximum profit.
You may complete at most k transactions.

Example
Given prices = [4,4,6,1,1,4,2,5], and k = 2, return 6.

Note
You may not engage in multiple transactions at the same time
(i.e., you must sell the stock before you buy again).

Challenge
O(nk) time. 
```

## 题解 1

卖股票系列中最难的一道，较易实现的方法为使用动态规划，动规的实现又分为大约 3 大类方法，这里先介绍一种最为朴素的方法，过不了大量数据，会 TLE.

最多允许 k 次交易，由于一次增加收益的交易至少需要两天，故当 k >= n/2 时，此题退化为卖股票的第二道题，即允许任意多次交易。当 k < n/2 时，使用动规来求解，动规的几个要素如下：

f[i][j] 代表第 i 天为止交易 k 次获得的最大收益，那么将问题分解为前 x 天交易 k-1 次，第 x+1 天至第 i 天交易一次两个子问题，于是动态方程如下：

```
f[i][j] = max(f[x][j - 1] + profit(x + 1, i)) 
```

简便起见，初始化二维矩阵为 0，下标尽可能从 1 开始，便于理解。

### Python

```
class Solution:
    """
    @param k: an integer
    @param prices: a list of integer
    @return: an integer which is maximum profit
    """
    def maxProfit(self, k, prices):
        if prices is None or len(prices) <= 1 or k <= 0:
            return 0

        n = len(prices)
        # k >= prices.length / 2 ==> multiple transactions Stock II
        if k >= n / 2:
            profit_max = 0
            for i in xrange(1, n):
                diff = prices[i] - prices[i - 1]
                if diff > 0:
                    profit_max += diff
            return profit_max

        f = [[0 for i in xrange(k + 1)] for j in xrange(n + 1)]
        for j in xrange(1, k + 1):
            for i in xrange(1, n + 1):
                for x in xrange(0, i + 1):
                    f[i][j] = max(f[i][j], f[x][j - 1] + self.profit(prices, x + 1, i))

        return f[n][k]

    # calculate the profit of prices(l, u)
    def profit(self, prices, l, u):
        if l >= u:
            return 0
        valley = 2**31 - 1
        profit_max = 0
        for price in prices[l - 1:u]:
            profit_max = max(profit_max, price - valley)
            valley = min(valley, price)
        return profit_max 
```

### C++

```
class Solution {
public:
    /**
     * @param k: An integer
     * @param prices: Given an integer array
     * @return: Maximum profit
     */
    int maxProfit(int k, vector<int> &prices) {
        if (prices.size() <= 1 || k <= 0) return 0;

        int n = prices.size();
        // k >= prices.length / 2 ==> multiple transactions Stock II
        if (k >= n / 2) {
            int profit_max = 0;
            for (int i = 1; i < n; ++i) {
                int diff = prices[i] - prices[i - 1];
                if (diff > 0) {
                    profit_max += diff;
                }
            }
            return profit_max;
        }

        vector<vector<int> > f = vector<vector<int> >(n + 1, vector<int>(k + 1, 0));
        for (int j = 1; j <= k; ++j) {
            for (int i = 1; i <= n; ++i) {
                for (int x = 0; x <= i; ++x) {
                    f[i][j] = max(f[i][j], f[x][j - 1] + profit(prices, x + 1, i));
                }
            }
        }

        return f[n][k];
    }

private:
    int profit(vector<int> &prices, int l, int u) {
        if (l >= u) return 0;

        int valley = INT_MAX;
        int profit_max = 0;
        for (int i = l - 1; i < u; ++i) {
            profit_max = max(profit_max, prices[i] - valley);
            valley = min(valley, prices[i]);
        }

        return profit_max;
    }
}; 
```

### Java

```
class Solution {
    /**
     * @param k: An integer
     * @param prices: Given an integer array
     * @return: Maximum profit
     */
    public int maxProfit(int k, int[] prices) {
        if (prices == null || prices.length <= 1 || k <= 0) return 0;

        int n = prices.length;
        if (k >= n / 2) {
            int profit_max = 0;
            for (int i = 1; i < n; i++) {
                if (prices[i] - prices[i - 1] > 0) {
                    profit_max += prices[i] - prices[i - 1];
                }
            }
            return profit_max;
        }

        int[][] f = new int[n + 1][k + 1];
        for (int j = 1; j <= k; j++) {
            for (int i = 1; i <= n; i++) {
                for (int x = 0; x <= i; x++) {
                    f[i][j] = Math.max(f[i][j], f[x][j - 1] + profit(prices, x + 1, i));
                }
            }
        }

        return f[n][k];
    }

    private int profit(int[] prices, int l, int u) {
        if (l >= u) return 0;

        int valley = Integer.MAX_VALUE;
        int profit_max = 0;
        for (int i = l - 1; i < u; i++) {
            profit_max = Math.max(profit_max, prices[i] - valley);
            valley = Math.min(valley, prices[i]);
        }
        return profit_max;
    }
}; 
```

### 源码分析

注意 Python 中的多维数组初始化方式，不可简单使用`[[0] * k] * n]`, 具体原因是因为 Python 中的对象引用方式。可以优化的地方是 profit 方法及最内存循环。

### 复杂度分析

三重循环，时间复杂度近似为 $$O(n² \cdot k)$$, 使用了 f 二维数组，空间复杂度为 $$O(n \cdot k)$$.

## Reference

*   [[LeetCode] Best Time to Buy and Sell Stock I II III IV | 梁佳宾的网络日志](http://liangjiabin.com/blog/2015/04/leetcode-best-time-to-buy-and-sell-stock.html)
*   [Best Time to Buy and Sell Stock IV 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/best-time-to-buy-and-sell-stock-iv/)
*   [leetcode-Best Time to Buy and Sell Stock 系列 // 陈辉的技术博客](http://www.devhui.com/2015/02/23/Best-Time-to-Buy-and-Sell-Stock/)
*   [[LeetCode]Best Time to Buy and Sell Stock IV | 书影博客](http://bookshadow.com/weblog/2015/02/18/leetcode-best-time-to-buy-and-sell-stock-iv/)

## Distinct Subsequences

## Question

*   leetcode: [Distinct Subsequences | LeetCode OJ](https://leetcode.com/problems/distinct-subsequences/)
*   lintcode: [(118) Distinct Subsequences](http://www.lintcode.com/en/problem/distinct-subsequences/)

```
Given a string S and a string T, count the number of distinct subsequences of T in S.
A subsequence of a string is a new string
which is formed from the original string by deleting some (can be none) of the characters
without disturbing the relative positions of the remaining characters.
(ie, "ACE" is a subsequence of "ABCDE" while "AEC" is not).

Example
Given S = "rabbbit", T = "rabbit", return 3.
Challenge
Do it in O(n2) time and O(n) memory.

O(n2) memory is also acceptable if you do not know how to optimize memory. 
```

## 题解 1

首先分清 subsequence 和 substring 两者的区别，subsequence 可以是不连续的子串。题意要求 S 中子序列 T 的个数。如果不考虑程序实现，我们能想到的办法是逐个比较 S 和 T 的首字符，相等的字符删掉，不等时则删除 S 中的首字符，继续比较后续字符直至 T 中字符串被删完。这种简单的思路有这么几个问题，题目问的是子序列的个数，而不是是否存在，故在字符不等时不能轻易删除掉 S 中的字符。那么如何才能得知子序列的个数呢？

要想得知不同子序列的个数，那么我们就不能在 S 和 T 中首字符不等时简单移除 S 中的首字符了，取而代之的方法应该是先将 S 复制一份，再用移除 S 中首字符后的新字符串和 T 进行比较，这点和深搜中的剪枝函数的处理有点类似。

### Python

```
class Solution:
    # @param S, T: Two string.
    # @return: Count the number of distinct subsequences
    def numDistinct(self, S, T):
        if S is None or T is None:
            return 0
        if len(S) < len(T):
            return 0
        if len(T) == 0:
            return 1

        num = 0
        for i, Si in enumerate(S):
            if Si == T[0]:
                num += self.numDistinct(S[i + 1:], T[1:])

        return num 
```

### C++

```
class Solution {
public:
    /**
     * @param S, T: Two string.
     * @return: Count the number of distinct subsequences
     */
    int numDistinct(string &S, string &T) {
        if (S.size() < T.size()) return 0;
        if (T.empty()) return 1;

        int num = 0;
        for (int i = 0; i < S.size(); ++i) {
            if (S[i] == T[0]) {
                string Si = S.substr(i + 1);
                string t = T.substr(1);
                num += numDistinct(Si, t);
            }
        }

        return num;
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param S, T: Two string.
     * @return: Count the number of distinct subsequences
     */
    public int numDistinct(String S, String T) {
        if (S == null || T == null) return 0;
        if (S.length() < T.length()) return 0;
        if (T.length() == 0) return 1;

        int num = 0;
        for (int i = 0; i < S.length(); i++) {
            if (S.charAt(i) == T.charAt(0)) {
                // T.length() >= 1, T.substring(1) will not throw index error
                num += numDistinct(S.substring(i + 1), T.substring(1));
            }
        }

        return num;
    }
} 
```

### 源码分析

1.  对 null 异常处理(C++ 中对 string 赋 NULL 是错的，函数内部无法 handle 这种情况)
2.  S 字符串长度若小于 T 字符串长度，T 必然不是 S 的子序列，返回 0
3.  T 字符串长度为 0，证明 T 是 S 的子序列，返回 1

由于进入 for 循环的前提是 `T.length() >= 1`, 故当 T 的长度为 1 时，Java 中对 T 取子串`T.substring(1)`时产生的是空串`""`而并不抛出索引越界的异常。

### 复杂度分析

最好情况下，S 中没有和 T 相同的字符，时间复杂度为 $$O(n)$$; 最坏情况下，S 中的字符和 T 中字符完全相同，此时可以画出递归调用栈，发现和深搜非常类似，数学关系为 $$f(n) = \sum _{i = 1} ^{n - 1} f(i)$$, 这比 Fibonacci 的复杂度还要高很多。

## 题解 2 - Dynamic Programming

从题解 1 的复杂度分析中我们能发现由于存在较多的重叠子状态(相同子串被比较多次), 因此可以想到使用动态规划优化。但是动规的三大要素如何建立？由于本题为两个字符串之间的关系，故可以尝试使用双序列(DP*Two*Sequence)动规的思路求解。

定义`f[i][j]`为 S[0:i] 中子序列为 T[0:j] 的个数，接下来寻找状态转移关系，状态转移应从 f[i-1][j], f[i-1][j-1], f[i][j-1] 中寻找，接着寻找突破口——S[i] 和 T[j] 的关系。

1.  `S[i] == T[j]`: 两个字符串的最后一个字符相等，我们可以选择 S[i] 和 T[j] 配对，那么此时有 f[i][j] = f[i-1][j-1]; 若不使 S[i] 和 T[j] 配对，而是选择 S[0:i-1] 中的某个字符和 T[j] 配对，那么 f[i][j] = f[i-1][j]. 综合以上两种选择，可得知在`S[i] == T[j]`时有 f[i][j] = f[i-1][j-1] + f[i-1][j]
2.  `S[i] != T[j]`: 最后一个字符不等时，S[i] 不可能和 T[j] 配对，故 f[i][j] = f[i-1][j]

为便于处理第一个字符相等的状态(便于累加)，初始化 f[i][0]为 1, 其余为 0\. 这里对于 S 或 T 为空串时返回 0，返回 1 也能说得过去。

### Python

```
class Solution:
    # @param S, T: Two string.
    # @return: Count the number of distinct subsequences
    def numDistinct(self, S, T):
        if S is None or T is None:
            return 0
        if len(S) < len(T):
            return 0
        if len(T) == 0:
            return 1

        f = [[0 for i in xrange(len(T) + 1)] for j in xrange(len(S) + 1)]
        for i, Si in enumerate(S):
            f[i][0] = 1
            for j, Tj in enumerate(T):
                if Si == Tj:
                    f[i + 1][j + 1] = f[i][j + 1] + f[i][j]
                else:
                    f[i + 1][j + 1] = f[i][j + 1]

        return f[len(S)][len(T)] 
```

### C++

```
class Solution {
public:
    /**
     * @param S, T: Two string.
     * @return: Count the number of distinct subsequences
     */
    int numDistinct(string &S, string &T) {
        if (S.size() < T.size()) return 0;
        if (T.empty()) return 1;

        vector<vector<int> > f(S.size() + 1, vector<int>(T.size() + 1, 0));
        for (int i = 0; i < S.size(); ++i) {
            f[i][0] = 1;
            for (int j = 0; j < T.size(); ++j) {
                if (S[i] == T[j]) {
                    f[i + 1][j + 1] = f[i][j + 1] + f[i][j];
                } else {
                    f[i + 1][j + 1] = f[i][j + 1];
                }
            }
        }

        return f[S.size()][T.size()];
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param S, T: Two string.
     * @return: Count the number of distinct subsequences
     */
    public int numDistinct(String S, String T) {
        if (S == null || T == null) return 0;
        if (S.length() < T.length()) return 0;
        if (T.length() == 0) return 1;

        int[][] f = new int[S.length() + 1][T.length() + 1];
        for (int i = 0; i < S.length(); i++) {
            f[i][0] = 1;
            for (int j = 0; j < T.length(); j++) {
                if (S.charAt(i) == T.charAt(j)) {
                    f[i + 1][j + 1] = f[i][j + 1] + f[i][j];
                } else {
                    f[i + 1][j + 1] = f[i][j + 1];
                }
            }
        }

        return f[S.length()][T.length()];
    }
} 
```

### 源码分析

异常处理部分和题解 1 相同，初始化时维度均多一个元素便于处理。

### 复杂度分析

由于免去了重叠子状态的计算，双重 for 循环，时间复杂度为 $$O(n²)$$, 使用了二维矩阵保存状态，空间复杂度为 $$O(n²)$$. 空间复杂度可以通过滚动数组的方式优化，详见 [Dynamic Programming - 动态规划](http://algorithm.yuanbin.me/zh-hans/dynamic_programming/index.html).

空间复杂度优化之后的代码如下：

#### Java

```
public class Solution {
    /**
     * @param S, T: Two string.
     * @return: Count the number of distinct subsequences
     */
    public int numDistinct(String S, String T) {
        if (S == null || T == null) return 0;
        if (S.length() < T.length()) return 0;
        if (T.length() == 0) return 1;

        int[] f = new int[T.length() + 1];
        f[0] = 1;
        for (int i = 0; i < S.length(); i++) {
            for (int j = T.length() - 1; j >= 0; j--) {
                if (S.charAt(i) == T.charAt(j)) {
                        f[j + 1] += f[j];
                }
            }
        }

        return f[T.length()];
    }
} 
```

## Reference

*   [LeetCode: Distinct Subsequences（不同子序列的个数） - 亦忘却 _ 亦纪念](http://blog.csdn.net/abcbc/article/details/8978146)
*   soulmachine leetcode-cpp 中 Distinct Subsequences 部分
*   [Distinct Subsequences | Training dragons the hard way](http://traceformula.blogspot.com/2015/08/distinct-subsequences.html)