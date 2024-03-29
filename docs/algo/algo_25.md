# 第 25 节 Dynamic Programming - 动态规划（一）

动态规划是一种「分治」的思想，通俗一点来说就是「大事化小，小事化无」的艺术。在将大问题化解为小问题的「分治」过程中，保存对这些小问题已经处理好的结果，并供后面处理更大规模的问题时直接使用这些结果。嗯，感觉讲了和没讲一样，还是不会使用动规的思想解题...

下面看看知乎上的熊大大对动规比较「正经」的描述。

> 动态规划是通过拆分问题，定义问题状态和状态之间的关系，使得问题能够以递推（或者说分治）的方式去解决。

以上定义言简意赅，可直接用于实战指导，不愧是参加过 NOI 的。

动规的思想虽然好理解，但是要真正活用起来就需要下点功夫了。建议看看下面知乎上的回答。

动态规划最重要的两个要点：

1.  状态(状态不太好找，可先从转化方程入手分析)
2.  状态间的转化方程(从题目的隐含条件出发寻找递推关系)

其他的要点则是如初始化状态的确定(由状态和转化方程得知)，需要的结果(状态转移的终点)

动态规划问题中一般从以下四个角度考虑：

1.  状态(State)
2.  状态间的转移方程(Function)
3.  状态的初始化(Initialization)
4.  返回结果(Answer)

动规适用的情形：

1.  最大值/最小值
2.  有无可行解
3.  求方案个数(如果需要列出所有方案，则一定不是动规，因为全部方案为指数级别复杂度，所有方案需要列出时往往用递归)
4.  给出的数据不可随便调整位置

## 单序列(DP_Sequence)

单序列动态规划的状态通常定义为：数组前 i 个位置, 数字, 字母 或者 以第 i 个为... 返回结果通常为数组的最后一个元素。

按照动态规划的四要素，此类题可从以下四个角度分析。

1.  State: f[i] 前 i 个位置/数字/字母...
2.  Function: f[i] = f[i-1]... 找递推关系
3.  Initialization: 根据题意进行必要的初始化
4.  Answer: f[n-1]

## 双序列(DP*Two*Sequence)

一般有两个数组或者两个字符串，计算其匹配关系。双序列中常用二维数组表示状态转移关系，但往往可以使用滚动数组的方式对空间复杂度进行优化。举个例子，以题 [Distinct Subsequences](http://algorithm.yuanbin.me/zh-hans/dynamic_programming/distinct_subsequences.html) 为例，状态转移方程如下：

```
f[i+1][j+1] = f[i][j+1] + f[i][j] (if S[i] == T[j])
f[i+1][j+1] = f[i][j+1] (if S[i] != T[j]) 
```

从以上转移方程可以看出 `f[i+1][*]` 只与其前一个状态 `f[i][*]` 有关，而对于 `f[*][j]` 来说则基于当前索引又与前一个索引有关，故我们以递推的方式省略第一维的空间，并以第一维作为外循环，内循环为 j, 由递推关系可知在使用滚动数组时，若内循环 j 仍然从小到大遍历，那么对于 `f[j+1]` 来说它得到的 `f[j]` 则是当前一轮(`f[i+1][j]`)的值，并不是需要的 `f[i][j]` 的值。所以若想得到上一轮的结果，必须在内循环使用逆推的方式进行。文字表述比较模糊，可以自行画一个二维矩阵的转移矩阵来分析，认识到这一点非常重要。

小结一下，使用滚动数组的核心在于：

1.  状态转移矩阵中只能取 `f[i+1][*]` 和 `f[i][*]`, 这是使用滚动数组的前提。
2.  外循环使用 i, 内循环使用 j 并同时使用逆推，这是滚动数组使用的具体实践。

代码如下：

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

纸上得来终觉浅，绝知此事要躬行。光说不练假把戏，下面就来几道 DP 的题练练手。

### Reference

1.  [什么是动态规划？动态规划的意义是什么？ - 知乎](http://www.zhihu.com/question/23995189) - 熊大大和王勐的回答值得细看，适合作为动态规划的科普和入门。维基百科上对动态规划的描述感觉太过学术。
2.  [动态规划：从新手到专家](http://www.hawstein.com/posts/dp-novice-to-advanced.html) - Topcoder 上的一篇译作。

## Triangle - Find the minimum path sum from top to bottom

## Question

*   lintcode: [(109) Triangle](http://www.lintcode.com/en/problem/triangle/)

```
Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.

Note
Bonus point if you are able to do this using only O(n) extra space, where n is the total number of rows in the triangle.

Example
For example, given the following triangle

[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
The minimum path sum from top to bottom is 11 (i.e., 2 + 3 + 5 + 1 = 11). 
```

## 题解

题中要求最短路径和，每次只能访问下行的相邻元素，将 triangle 视为二维坐标。此题方法较多，下面分小节详述。

### Method 1 - Traverse without hashmap

首先考虑最容易想到的方法——递归遍历，逐个累加所有自上而下的路径长度，最后返回这些不同的路径长度的最小值。由于每个点往下都有 2 条路径，使用此方法的时间复杂度约为 $$O(2^n)$$, 显然是不可接受的解，不过我们还是先看看其实现思路。

### C++ Traverse without hashmap

```
class Solution {
public:
    /**
     * @param triangle: a list of lists of integers.
     * @return: An integer, minimum path sum.
     */
    int minimumTotal(vector<vector<int> > &triangle) {
        if (triangle.empty()) {
            return -1;
        }

        int result = INT_MAX;
        dfs(0, 0, 0, triangle, result);

        return result;
    }

private:
    void dfs(int x, int y, int sum, vector<vector<int> > &triangle, int &result) {
        const int n = triangle.size();
        if (x == n) {
            if (sum < result) {
                result = sum;
            }
            return;
        }

        dfs(x + 1, y, (sum + triangle[x][y]), triangle, result);
        dfs(x + 1, y + 1, (sum + triangle[x][y]), triangle, result);
    }
}; 
```

### 源码分析

`dfs()`的循环终止条件为`x == n`，而不是`x == n - 1`，主要是方便在递归时 sum 均可使用`sum + triangle[x][y]`，而不必根据不同的 y 和 y+1 改变，代码实现相对优雅一些。理解方式则变为从第 x 行走到第 x+1 行时的最短路径和，也就是说在此之前并不将第 x 行的元素值计算在内。

这种遍历的方法时间复杂度如此之高的主要原因是因为在 n 较大时递归计算了之前已经得到的结果，而这些结果计算一次后即不再变化，可再次利用。因此我们可以使用 hashmap 记忆已经计算得到的结果从而对其进行优化。

### Method 2 - Divide and Conquer without hashmap

既然可以使用递归遍历，当然也可以使用「分治」的方法来解。「分治」与之前的遍历区别在于「分治」需要返回每次「分治」后的计算结果，下面看代码实现。

### C++ Divide and Conquer without hashmap

```
class Solution {
public:
    /**
     * @param triangle: a list of lists of integers.
     * @return: An integer, minimum path sum.
     */
    int minimumTotal(vector<vector<int> > &triangle) {
        if (triangle.empty()) {
            return -1;
        }

        int result = dfs(0, 0, triangle);

        return result;
    }

private:
    int dfs(int x, int y, vector<vector<int> > &triangle) {
        const int n = triangle.size();
        if (x == n) {
            return 0;
        }

        return min(dfs(x + 1, y, triangle), dfs(x + 1, y + 1, triangle))  + triangle[x][y];
    }
}; 
```

使用「分治」的方法代码相对简洁一点，接下来我们使用 hashmap 保存 triangle 中不同坐标的点计算得到的路径和。

### Method 3 - Divide and Conquer with hashmap

新建一份大小和 triangle 一样大小的 hashmap，并对每个元素赋以`INT_MIN`以做标记区分。

### C++ Divide and Conquer with hashmap

```
class Solution {
public:
    /**
     * @param triangle: a list of lists of integers.
     * @return: An integer, minimum path sum.
     */
    int minimumTotal(vector<vector<int> > &triangle) {
        if (triangle.empty()) {
            return -1;
        }

        vector<vector<int> > hashmap(triangle);
        for (int i = 0; i != hashmap.size(); ++i) {
            for (int j = 0; j != hashmap[i].size(); ++j) {
                hashmap[i][j] = INT_MIN;
            }
        }
        int result = dfs(0, 0, triangle, hashmap);

        return result;
    }

private:
    int dfs(int x, int y, vector<vector<int> > &triangle, vector<vector<int> > &hashmap) {
        const int n = triangle.size();
        if (x == n) {
            return 0;
        }

        // INT_MIN means no value yet
        if (hashmap[x][y] != INT_MIN) {
            return hashmap[x][y];
        }
        int x1y = dfs(x + 1, y, triangle, hashmap);
        int x1y1 = dfs(x + 1, y + 1, triangle, hashmap);
        hashmap[x][y] =  min(x1y, x1y1) + triangle[x][y];

        return hashmap[x][y];
    }
}; 
```

由于已经计算出的最短路径值不再重复计算，计算复杂度由之前的 $$O(2^n)$$，变为 $$O(n²)$$, 每个坐标的元素仅计算一次，故共计算的次数为 $$1+2+...+n \approx O(n²)$$.

### Method 4 - Dynamic Programming

从主章节中对动态规划的简介我们可以知道使用动态规划的难点和核心在于**状态的定义及转化方程的建立**。那么问题来了，到底如何去找适合这个问题的状态及转化方程呢？

我们仔细分析题中可能的状态和转化关系，发现从`triangle`中坐标为 $$triangle[x][y]$$ 的元素出发，其路径只可能为 $$triangle[x][y]->triangle[x+1][y]$$ 或者 $$triangle[x][y]->triangle[x+1][y+1]$$. 以点 $$(x,y)$$ 作为参考，那么可能的状态 $$f(x,y)$$ 就可以是：

1.  从 $$(x,y)$$ 出发走到最后一行的最短路径和
2.  从 $$(0,0)$$ 走到 $$(x,y)$$的最短路径和

如果选择 1 作为状态，则相应的状态转移方程为： $$f*1(x,y) = min{f*1(x+1, y), f_1(x+1, y+1)} + triangle[x][y]$$

如果选择 2 作为状态，则相应的状态转移方程为： $$f*2(x,y) = min{f*2(x-1, y), f_2(x-1, y-1)} + triangle[x][y]$$

两个状态所对应的初始状态分别为 $$f*1(n-1, y), 0 \leq y \leq n-1$$ 和 $$f*2(0,0)$$. 在代码中应注意考虑边界条件。下面分别就这种不同的状态进行动态规划。

### C++ From Bottom to Top

```
class Solution {
public:
    /**
     * @param triangle: a list of lists of integers.
     * @return: An integer, minimum path sum.
     */
    int minimumTotal(vector<vector<int> > &triangle) {
        if (triangle.empty()) {
            return -1;
        }

        vector<vector<int> > hashmap(triangle);

        // get the total row number of triangle
        const int N = triangle.size();
        for (int i = 0; i != N; ++i) {
            hashmap[N-1][i] = triangle[N-1][i];
        }

        for (int i = N - 2; i >= 0; --i) {
            for (int j = 0; j < i + 1; ++j) {
                hashmap[i][j] = min(hashmap[i + 1][j], hashmap[i + 1][j + 1]) + triangle[i][j];
            }
        }

        return hashmap[0][0];
    }
}; 
```

### 源码分析

1.  异常处理
2.  使用 hashmap 保存结果
3.  初始化`hashmap[N-1][i]`, 由于是自底向上，故初始化时保存最后一行元素
4.  使用自底向上的方式处理循环
5.  最后返回结果 hashmap[0][0]

从空间利用角度考虑也可直接使用 triangle 替代 hashmap，但是此举会改变 triangle 的值，不推荐。

### C++ From Top to Bottom

```
class Solution {
public:
    /**
     * @param triangle: a list of lists of integers.
     * @return: An integer, minimum path sum.
     */
    int minimumTotal(vector<vector<int> > &triangle) {
        if (triangle.empty()) {
            return -1;
        }

        vector<vector<int> > hashmap(triangle);

        // get the total row number of triangle
        const int N = triangle.size();
        //hashmap[0][0] = triangle[0][0];
        for (int i = 1; i != N; ++i) {
            for (int j = 0; j <= i; ++j) {
                if (j == 0) {
                    hashmap[i][j] = hashmap[i - 1][j];
                }
                if (j == i) {
                    hashmap[i][j] = hashmap[i - 1][j - 1];
                }
                if ((j > 0) && (j < i)) {
                    hashmap[i][j] = min(hashmap[i - 1][j], hashmap[i - 1][j - 1]);
                }
                hashmap[i][j] += triangle[i][j];
            }
        }

        int result = INT_MAX;
        for (int i = 0; i != N; ++i) {
            result = min(result, hashmap[N - 1][i]);
        }
        return result;
    }
}; 
```

#### 源码解析

自顶向下的实现略微有点复杂，在寻路时需要考虑最左边和最右边的边界，还需要在最后返回结果时比较最小值。

## Backpack

## Question

*   lintcode: [(92) Backpack](http://www.lintcode.com/en/problem/backpack/)

### Problem Statement

Given *n* items with size $$A*i$$, an integer _m* denotes the size of a backpack. How full you can fill this backpack?

#### Example

If we have `4` items with size `[2, 3, 5, 7]`, the backpack size is 11, we can select `[2, 3, 5]`, so that the max size we can fill this backpack is `10`. If the backpack size is `12`. we can select `[2, 3, 7]` so that we can fulfill the backpack.

You function should return the max size we can fill in the given backpack.

#### Note

You can not divide any item into small pieces.

#### Challenge

O(n x m) time and O(m) memory.

O(n x m) memory is also acceptable if you do not know how to optimize memory.

## 题解 1

本题是典型的 01 背包问题，每种类型的物品最多只能选择一件。参考前文 [Knapsack](http://algorithm.yuanbin.me/zh-hans/basics_algorithm/knapsack.html) 中总结的解法，这个题中可以将背包的 size 理解为传统背包中的重量；题目问的是能达到的最大 size, 故可将每个背包的 size 类比为传统背包中的价值。

考虑到数组索引从 0 开始，故定义状态`bp[i + 1][j]`为前 `i` 个物品中选出重量不超过`j`时总价值的最大值。状态转移方程则为分`A[i] > j` 与否两种情况考虑。初始化均为 0，相当于没有放任何物品。

### Java

```
public class Solution {
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A: Given n items with size A[i]
     * @return: The maximum size
     */
    public int backPack(int m, int[] A) {
        if (A == null || A.length == 0) return 0;

        final int M = m;
        final int N = A.length;
        int[][] bp = new int[N + 1][M + 1];

        for (int i = 0; i < N; i++) {
            for (int j = 0; j <= M; j++) {
                if (A[i] > j) {
                    bp[i + 1][j] = bp[i][j];
                } else {
                    bp[i + 1][j] = Math.max(bp[i][j], bp[i][j - A[i]] + A[i]);
                }
            }
        }

        return bp[N][M];
    }
} 
```

### 源码分析

注意索引及初始化的值，尤其是 N 和 M 的区别，内循环处可等于 M。

### 复杂度分析

两重 for 循环，时间复杂度为 $$O(m \times n)$$, 二维矩阵的空间复杂度为 $$O(m \times n)$$, 一维矩阵的空间复杂度为 $$O(m)$$.

## 题解 2

接下来看看 [九章算法](http://www.jiuzhang.com/solutions/backpack/) 的题解，**这种解法感觉不是很直观，推荐使用题解 1 的解法。**

1.  状态: result[i][S] 表示前 i 个物品，取出一些物品能否组成体积和为 S 的背包
2.  状态转移方程: $$f[i][S] = f[i-1][S-A[i]] ~or~ f[i-1][S]$$ (A[i]为第 i 个物品的大小)
    *   欲从前 i 个物品中取出一些组成体积和为 S 的背包，可从两个状态转换得到。
        1.  $$f[i-1][S-A[i]]$$: **放入第 i 个物品**，前 $$i-1$$ 个物品能否取出一些体积和为 $$S-A[i]$$ 的背包。
        2.  $$f[i-1][S]$$: **不放入第 i 个物品**，前 $$i-1$$ 个物品能否取出一些组成体积和为 S 的背包。
3.  状态初始化: $$f[1 \cdots n][0]=true; ~f[0][1 \cdots m]=false$$. 前 1~n 个物品组成体积和为 0 的背包始终为真，其他情况为假。
4.  返回结果: 寻找使 $$f[n][S]$$ 值为 true 的最大 S ($$1 \leq S \leq m$$)

### C++ - 2D vector

```
class Solution {
public:
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A: Given n items with size A[i]
     * @return: The maximum size
     */
    int backPack(int m, vector<int> A) {
        if (A.empty() || m < 1) {
            return 0;
        }

        const int N = A.size() + 1;
        const int M = m + 1;
        vector<vector<bool> > result;
        result.resize(N);
        for (vector<int>::size_type i = 0; i != N; ++i) {
            result[i].resize(M);
            std::fill(result[i].begin(), result[i].end(), false);
        }

        result[0][0] = true;
        for (int i = 1; i != N; ++i) {
            for (int j = 0; j != M; ++j) {
                if (j < A[i - 1]) {
                    result[i][j] = result[i - 1][j];
                } else {
                    result[i][j] = result[i - 1][j] || result[i - 1][j - A[i - 1]];
                }
            }
        }

        // return the largest i if true
        for (int i = M; i > 0; --i) {
            if (result[N - 1][i - 1]) {
                return (i - 1);
            }
        }
        return 0;
    }
}; 
```

### 源码分析

1.  异常处理
2.  初始化结果矩阵，注意这里需要使用`resize`而不是`reserve`，否则可能会出现段错误
3.  实现状态转移逻辑，一定要分`j < A[i - 1]`与否来讨论
4.  返回结果，只需要比较`result[N - 1][i - 1]`的结果，返回 true 的最大值

状态转移逻辑中代码可以进一步简化，即：

```
 for (int i = 1; i != N; ++i) {
            for (int j = 0; j != M; ++j) {
                result[i][j] = result[i - 1][j];
                if (j >= A[i - 1] && result[i - 1][j - A[i - 1]]) {
                    result[i][j] = true;
                }
            }
        } 
```

考虑背包问题的核心——状态转移方程，如何优化此转移方程？原始方案中用到了二维矩阵来保存 result，注意到 result 的第 i 行仅依赖于第 i-1 行的结果，那么能否用一维数组来代替这种隐含的关系呢？我们**在内循环 j 处递减即可**。如此即可避免`result[i][S]`的值由本轮`result[i][S-A[i]]`递推得到。

### C++ - 1D vector

```
class Solution {
public:
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A: Given n items with size A[i]
     * @return: The maximum size
     */
    int backPack(int m, vector<int> A) {
        if (A.empty() || m < 1) {
            return 0;
        }

        const int N = A.size();
        vector<bool> result;
        result.resize(m + 1);
        std::fill(result.begin(), result.end(), false);

        result[0] = true;
        for (int i = 0; i != N; ++i) {
            for (int j = m; j >= 0; --j) {
                if (j >= A[i] && result[j - A[i]]) {
                    result[j] = true;
                }
            }
        }

        // return the largest i if true
        for (int i = m; i > 0; --i) {
            if (result[i]) {
                return i;
            }
        }
        return 0;
    }
}; 
```

### 复杂度分析

两重 for 循环，时间复杂度均为 $$O(m \times n)$$, 二维矩阵的空间复杂度为 $$O(m \times n)$$, 一维矩阵的空间复杂度为 $$O(m)$$.

## Reference

*   《挑战程序设计竞赛》第二章
*   [Lintcode: Backpack - neverlandly - 博客园](http://www.cnblogs.com/EdwardLiu/p/4269149.html)
*   [九章算法 | 背包问题](http://www.jiuzhang.com/problem/58/)
*   [崔添翼 § 翼若垂天之云 › 《背包问题九讲》2.0 alpha1](http://cuitianyi.com/blog/%E3%80%8A%E8%83%8C%E5%8C%85%E9%97%AE%E9%A2%98%E4%B9%9D%E8%AE%B2%E3%80%8B2-0-alpha1/)

## Backpack II

## Question

*   lintcode: [(125) Backpack II](http://www.lintcode.com/en/problem/backpack-ii/)

### Problem Statement

Given *n* items with size $$Ai$$ and value Vi, and a backpack with size *m*. What's the maximum value can you put into the backpack?

#### Example

Given 4 items with size `[2, 3, 5, 7]` and value `[1, 5, 2, 4]`, and a backpack with size `10`. The maximum value is `9`.

#### Note

You cannot divide item into small pieces and the total size of items you choose should smaller or equal to m.

#### Challenge

O(n x m) memory is acceptable, can you do it in O(m) memory?

## 题解

首先定义状态 $$K(i,w)$$ 为前 $$i$$ 个物品放入 size 为 $$w$$ 的背包中所获得的最大价值，则相应的状态转移方程为： $$K(i,w) = \max {K(i-1, w), K(i-1, w - w*i) + v*i}$$

详细分析过程见 [Knapsack](http://algorithm.yuanbin.me/zh-hans/basics_algorithm/knapsack.html)

### C++ - 2D vector for result

```
class Solution {
public:
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A & V: Given n items with size A[i] and value V[i]
     * @return: The maximum value
     */
    int backPackII(int m, vector<int> A, vector<int> V) {
        if (A.empty() || V.empty() || m < 1) {
            return 0;
        }
        const int N = A.size() + 1;
        const int M = m + 1;
        vector<vector<int> > result;
        result.resize(N);
        for (vector<int>::size_type i = 0; i != N; ++i) {
            result[i].resize(M);
            std::fill(result[i].begin(), result[i].end(), 0);
        }

        for (vector<int>::size_type i = 1; i != N; ++i) {
            for (int j = 0; j != M; ++j) {
                if (j < A[i - 1]) {
                    result[i][j] = result[i - 1][j];
                } else {
                    int temp = result[i - 1][j - A[i - 1]] + V[i - 1];
                    result[i][j] = max(temp, result[i - 1][j]);
                }
            }
        }

        return result[N - 1][M - 1];
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A & V: Given n items with size A[i] and value V[i]
     * @return: The maximum value
     */
    public int backPackII(int m, int[] A, int V[]) {
        if (A == null || V == null || A.length == 0 || V.length == 0) return 0;

        final int N = A.length;
        final int M = m;
        int[][] bp = new int[N + 1][M + 1];
        for (int i = 0; i < N; i++) {
            for (int j = 0; j <= M; j++) {
                if (A[i] > j) {
                    bp[i + 1][j] = bp[i][j];
                } else {
                    bp[i + 1][j] = Math.max(bp[i][j], bp[i][j - A[i]] + V[i]);
                }
            }
        }

        return bp[N][M];
    }
} 
```

### 源码分析

1.  使用二维矩阵保存结果 result
2.  返回 result 矩阵的右下角元素——背包 size 限制为 m 时的最大价值

按照第一题 backpack 的思路，这里可以使用一维数组进行空间复杂度优化。优化方法为逆序求`result[j]`，优化后的代码如下：

### C++ 1D vector for result

```
class Solution {
public:
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A & V: Given n items with size A[i] and value V[i]
     * @return: The maximum value
     */
    int backPackII(int m, vector<int> A, vector<int> V) {
        if (A.empty() || V.empty() || m < 1) {
            return 0;
        }

        const int M = m + 1;
        vector<int> result;
        result.resize(M);
        std::fill(result.begin(), result.end(), 0);

        for (vector<int>::size_type i = 0; i != A.size(); ++i) {
            for (int j = m; j >= 0; --j) {
                if (j < A[i]) {
                    // result[j] = result[j];
                } else {
                    int temp = result[j - A[i]] + V[i];
                    result[j] = max(temp, result[j]);
                }
            }
        }

        return result[M - 1];
    }
}; 
```

## Reference

*   [Lintcode: Backpack II - neverlandly - 博客园](http://www.cnblogs.com/EdwardLiu/p/4272300.html)
*   [九章算法 | 背包问题](http://www.jiuzhang.com/problem/58/)

## Minimum Path Sum

*   tags: [DP_Matrix]

## Question

*   lintcode: [(110) Minimum Path Sum](http://www.lintcode.com/en/problem/minimum-path-sum/)

```
Given a m x n grid filled with non-negative numbers, find a path from top left to bottom right which minimizes the sum of all numbers along its path.

Note
You can only move either down or right at any point in time. 
```

## 题解

1.  State: f[x][y] 从坐标(0,0)走到(x,y)的最短路径和
2.  Function: f[x][y] = (x, y) + min{f[x-1][y], f[x][y-1]}
3.  Initialization: f[0][0] = A[0][0], f[i][0] = sum(0,0 -> i,0), f[0][i] = sum(0,0 -> 0,i)
4.  Answer: f[m-1][n-1]

注意最后返回为 f[m-1][n-1]而不是 f[m][n].

首先看看如下正确但不合适的答案，OJ 上会出现 TLE。 未使用 hashmap 并且使用了递归的错误版本。

### C++ dfs without hashmap: ~~Wrong answer~~

```
class Solution {
public:
    /**
     * @param grid: a list of lists of integers.
     * @return: An integer, minimizes the sum of all numbers along its path
     */
    int minPathSum(vector<vector<int> > &grid) {
        if (grid.empty()) {
            return 0;
        }

        const int m = grid.size() - 1;
        const int n = grid[0].size() - 1;

        return helper(grid, m, n);

    }

private:
    int helper(vector<vector<int> > &grid_in, int x, int y) {
        if (0 == x && 0 == y) {
            return grid_in[0][0];
        }
        if (0 == x) {
            return helper(grid_in, x, y - 1) + grid_in[x][y];
        }
        if (0 == y) {
            return helper(grid_in, x - 1, y) + grid_in[x][y];
        }

        return grid_in[x][y] + min(helper(grid_in, x - 1, y), helper(grid_in, x, y - 1));
    }
}; 
```

使用迭代思想进行求解的正确实现：

### C++ Iterative

```
class Solution {
public:
    /**
     * @param grid: a list of lists of integers.
     * @return: An integer, minimizes the sum of all numbers along its path
     */
    int minPathSum(vector<vector<int> > &grid) {
        if (grid.empty() || grid[0].empty()) {
            return 0;
        }

        const int M = grid.size();
        const int N = grid[0].size();
        vector<vector<int> > ret(M, vector<int> (N, 0));

        ret[0][0] = grid[0][0];
        for (int i = 1; i != M; ++i) {
            ret[i][0] = grid[i][0] + ret[i - 1][0];
        }
        for (int i = 1; i != N; ++i) {
            ret[0][i] = grid[0][i] + ret[0][i - 1];
        }

        for (int i = 1; i != M; ++i) {
            for (int j = 1; j != N; ++j) {
                ret[i][j] = grid[i][j] + min(ret[i - 1][j], ret[i][j - 1]);
            }
        }

        return ret[M - 1][N - 1];
    }
}; 
```

### 源码分析

1.  异常处理，不仅要对 grid 还要对 grid[0]分析
2.  对返回结果矩阵进行初始化，注意 ret[0][0]须单独初始化以便使用 ret[i-1]
3.  递推时 i 和 j 均从 1 开始
4.  返回结果 ret[M-1][N-1]，注意下标是从 0 开始的

此题还可进行空间复杂度优化，和背包问题类似，使用一维数组代替二维矩阵也行，具体代码可参考 [水中的鱼: [LeetCode] Minimum Path Sum 解题报告](http://fisherlei.blogspot.sg/2012/12/leetcode-minimum-path-sum.html)

优化空间复杂度，要么对行遍历进行优化，要么对列遍历进行优化，通常我们习惯先按行遍历再按列遍历，有状态转移方程 f[x][y] = (x, y) + min{f[x-1][y], f[x][y-1]} 知，想要优化行遍历，那么 f[y]保存的值应为第 x 行第 y 列的和。由于无行下标信息，故初始化时仅能对第一个元素初始化，分析时建议画图理解。

### C++ 1D vector

```
class Solution {
public:
    /**
     * @param grid: a list of lists of integers.
     * @return: An integer, minimizes the sum of all numbers along its path
     */
    int minPathSum(vector<vector<int> > &grid) {
        if (grid.empty() || grid[0].empty()) {
            return 0;
        }

        const int M = grid.size();
        const int N = grid[0].size();
        vector<int> ret(N, INT_MAX);

        ret[0] = 0;

        for (int i = 0; i != M; ++i) {
            ret[0] =  ret[0] + grid[i][0];
            for (int j = 1; j != N; ++j) {
                ret[j] = grid[i][j] + min(ret[j], ret[j - 1]);
            }
        }

        return ret[N - 1];
    }
}; 
```

初始化时需要设置为`INT_MAX`，便于`i = 0`时取`ret[j]`.