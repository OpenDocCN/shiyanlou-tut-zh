# 第 9 节 Math and Bit Manipulation - 数学和位运算（一）

## Reference

*   [位运算简介及实用技巧（一）：基础篇 | Matrix67: The Aha Moments](http://www.matrix67.com/blog/archives/263)
*   *cc150* chapter 8.5 and chapter 9.5

## Single Number

「找单数」系列题，技巧性较强，需要灵活运用位运算的特性。

## Question

*   lintcode: [(82) Single Number](http://www.lintcode.com/en/problem/single-number/)

```
Given 2*n + 1 numbers, every numbers occurs twice except one, find it.

Example
Given [1,2,2,1,3,4,3], return 4

Challenge
One-pass, constant extra space 
```

## 题解

根据题意，共有`2*n + 1`个数，且有且仅有一个数落单，要找出相应的「单数」。鉴于有空间复杂度的要求，不可能使用另外一个数组来保存每个数出现的次数，考虑到异或运算的特性，根据`x ^ x = 0`和`x ^ 0 = x`可将给定数组的所有数依次异或，最后保留的即为结果。

### C++

```
class Solution {
public:
    /**
     * @param A: Array of integers.
     * return: The single number.
     */
    int singleNumber(vector<int> &A) {
        if (A.empty()) {
            return -1;
        }
        int result = 0;

        for (vector<int>::iterator iter = A.begin(); iter != A.end(); ++iter) {
            result = result ^ *iter;
        }

        return result;
    }
}; 
```

### 源码分析

1.  异常处理(OJ 上对于空 vector 的期望结果为 0，但个人认为-1 更为合理)
2.  初始化返回结果`result`为 0，因为`x ^ 0 = x`

## Single Number II

## Question

*   leetcode: [Single Number II | LeetCode OJ](https://leetcode.com/problems/single-number-ii/)
*   lintcode: [(83) Single Number II](http://www.lintcode.com/en/problem/single-number-ii/)

### Problem Statement

Given `3*n + 1` numbers, every numbers occurs triple times except one, find it.

#### Example

Given `[1,1,2,3,3,3,2,2,4,1]` return `4`

#### Challenge

One-pass, constant extra space.

## 题解 1 - 逐位处理

上题 Single Number 用到了二进制中异或的运算特性，这题给出的元素数目为`3*n + 1`，因此我们很自然地想到如果有种运算能满足「三三运算」为 0 该有多好！对于三个相同的数来说，其相加的和必然是 3 的倍数，仅仅使用这一个特性还不足以将单数找出来，我们再来挖掘隐含的信息。以 3 为例，若使用不进位加法，三个 3 相加的结果为：

```
0011
0011
0011
----
0033 
```

注意到其中的奥义了么？三个相同的数相加，不仅其和能被 3 整除，其二进制位上的每一位也能被 3 整除！因此我们只需要一个和`int`类型相同大小的数组记录每一位累加的结果即可。时间复杂度约为 $$O((3n+1)\cdot sizeof(int) \cdot 8)$$

### Python

```
class Solution(object):
    def singleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if nums is None:
            return 0

        result = 0
        for i in xrange(32):
            bit_i_sum = 0
            for num in nums:
                bit_i_sum += ((num >> i) & 1)
            result |= ((bit_i_sum % 3) << i)
        return self.twos_comp(result, 32)

    def twos_comp(self, val, bits):
        """
        compute the 2's compliment of int value val
        e.g. -4 ==> 11100 == -(10000) + 01100
        """
        return -(val & (1 << (bits - 1))) | (val & ((1 << (bits - 1)) - 1)) 
```

### C++

```
class Solution {
public:
    /**
     * @param A : An integer array
     * @return : An integer
     */
    int singleNumberII(vector<int> &A) {
        if (A.empty()) {
            return 0;
        }

        int result = 0, bit_i_sum = 0;

        for (int i = 0; i != 8 * sizeof(int); ++i) {
            bit_i_sum = 0;
            for (int j = 0; j != A.size(); ++j) {
                // get the *i*th bit of A
                bit_i_sum += ((A[j] >> i) & 1);
            }
            // set the *i*th bit of result
            result |= ((bit_i_sum % 3) << i);
        }

        return result;
    }
}; 
```

### 源码解析

1.  异常处理
2.  循环处理返回结果`result`的`int`类型的每一位，要么自增 1，要么保持原值。注意`i`最大可取 $$8 \cdot sizeof(int) - 1$$, 字节数=>位数的转换
3.  对第`i`位处理完的结果模 3 后更新`result`的第`i`位，由于`result`初始化为 0，故使用或操作即可完成

Python 中的整数表示理论上可以是无限的（求出处），所以移位计算得到最终结果时需要转化为 2 的补码。此方法参考自 [Two's Complement in Python](http://stackoverflow.com/questions/1604464/twos-complement-in-python)

## Reference

[Single Number II - Leetcode Discuss](https://leetcode.com/discuss/857/constant-space-solution?show=2542) 中抛出了这么一道扩展题：

```
Given an array of integers, every element appears k times except for one. Find that single one which appears l times. 
```

@ranmocy 给出了如下经典解：

We need a array `x[i]` with size `k` for saving the bits appears `i` times. For every input number a, generate the new counter by `x[j] = (x[j-1] & a) | (x[j] & ~a)`. Except `x[0] = (x[k] & a) | (x[0] & ~a)`.

In the equation, the first part indicates the the carries from previous one. The second part indicates the bits not carried to next one.

Then the algorithms run in `O(kn)` and the extra space `O(k)`.

### Java

```
public class Solution {
    public int singleNumber(int[] A, int k, int l) {
        if (A == null) return 0;
        int t;
        int[] x = new int[k];
        x[0] = ~0;
        for (int i = 0; i < A.length; i++) {
            t = x[k-1];
            for (int j = k-1; j > 0; j--) {
                x[j] = (x[j-1] & A[i]) | (x[j] & ~A[i]);
            }
            x[0] = (t & A[i]) | (x[0] & ~A[i]);
        }
        return x[l];
    }
} 
```

## Single Number III

## Question

*   lintcode: [(84) Single Number III](http://www.lintcode.com/en/problem/single-number-iii/)

```
Given 2*n + 2 numbers, every numbers occurs twice except two, find them.

Example
Given [1,2,2,3,4,4,5,3] return 1 and 5

Challenge
O(n) time, O(1) extra space. 
```

## 题解

题 [Single Number](http://algorithm.yuanbin.me/zh-hans/math_and_bit_manipulation/single_number.html) 的 follow up, 不妨设最后两个只出现一次的数分别为 `x1, x2`. 那么遍历数组时根据两两异或的方法可得最后的结果为 `x1 ^ x2`, 如果我们要分别求得 `x1` 和 `x2`, 我们可以根据 `x1 ^ x2 ^ x1 = x2` 求得 `x2`, 同理可得 `x_1`. 那么问题来了，如何得到`x1`和`x2`呢？看起来似乎是个死循环。大多数人一般也就能想到这一步(比如我...)。

这道题的巧妙之处在于利用`x1 ^ x2`的结果对原数组进行了分组，进而将`x1`和`x2`分开了。具体方法则是利用了`x1 ^ x2`不为 0 的特性，如果`x1 ^ x2`不为 0，那么`x1 ^ x2`的结果必然存在某一二进制位不为 0（即为 1），我们不妨将最低位的 1 提取出来，由于在这一二进制位上`x1`和`x2`必然相异，即`x1`, `x2`中相应位一个为 0，另一个为 1，所以我们可以利用这个最低位的 1 将`x1`和`x2`分开。又由于除了`x1`和`x2`之外其他数都是成对出现，故与最低位的 1 异或时一定会抵消，十分之精妙！

### Java

```
public class Solution {
    /**
     * @param A : An integer array
     * @return : Two integers
     */
    public List<Integer> singleNumberIII(int[] A) {
        ArrayList<Integer> nums = new ArrayList<Integer>();
        if (A == null || A.length == 0) return nums;

        int x1xorx2 = 0;
        for (int i : A) {
            x1xorx2 ^= i;
        }

        // get the last 1 bit of x1xorx2, e.g. 1010 ==> 0010
        int last1Bit = x1xorx2 - (x1xorx2 & (x1xorx2 - 1));
        int single1 = 0, single2 = 0;
        for (int i : A) {
            if ((last1Bit & i) == 0) {
                single1 ^= i;
            } else {
                single2 ^= i;
            }
        }

        nums.add(single1);
        nums.add(single2);
        return nums;
    }
} 
```

### 源码分析

求一个数二进制位 1 的最低位方法为 `x1xorx2 - (x1xorx2 & (x1xorx2 - 1))`, 其他位运算的总结可参考 [Bit Manipulation](http://algorithm.yuanbin.me/zh-hans/basics_misc/bit_manipulation.html)。利用`last1Bit`可将数组的数分为两组，一组是相应位为 0，另一组是相应位为 1.

### 复杂度分析

两次遍历数组，时间复杂度 $$O(n)$$, 使用了部分额外空间，空间复杂度 $$O(1)$$.

## Reference

*   [Single Number III 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/single-number-iii/)

## O(1) Check Power of 2

## Question

*   lintcode: [(142) O(1) Check Power of 2](http://www.lintcode.com/en/problem/o1-check-power-of-2/)

```
Using O(1) time to check whether an integer n is a power of 2.

Example
For n=4, return true;

For n=5, return false;

Challenge
O(1) time 
```

## 题解

咋看起来挺简单的一道题目，可之前若是没有接触过神奇的位运算技巧遇到这种题就有点不知从哪入手了，咳咳，我第一次接触到这个题就是在七牛的笔试题中看到的，泪奔 :-(

简单点来考虑可以连除 2 求余，看最后的余数是否为 1，但是这种方法无法在 $$O(1)$$ 的时间内解出，所以我们必须要想点别的办法了。2 的整数幂若用二进制来表示，则其中必只有一个 1，其余全是 0，那么怎么才能用一个式子把这种特殊的关系表示出来了？传统的位运算如按位与、按位或和按位异或等均无法直接求解，我就不卖关子了，比较下`x - 1`和`x`的关系试试？以`x=4`为例。

```
0100 ==> 4
0011 ==> 3 
```

两个数进行按位与就为 0 了！如果不是 2 的整数幂则无上述关系，反证法可证之。

### Python

```
class Solution:
    """
    @param n: An integer
    @return: True or false
    """
    def checkPowerOf2(self, n):
        if n < 1:
            return False
        else:
            return (n & (n - 1)) == 0 
```

### C++

```
class Solution {
public:
    /*
     * @param n: An integer
     * @return: True or false
     */
    bool checkPowerOf2(int n) {
        if (1 > n) {
            return false;
        } else {
            return 0 == (n & (n - 1));
        }
    }
}; 
```

### Java

```
class Solution {
    /*
     * @param n: An integer
     * @return: True or false
     */
    public boolean checkPowerOf2(int n) {
        if (n < 1) {
            return false;
        } else {
            return (n & (n - 1)) == 0;
        }
    }
}; 
```

### 源码分析

除了考虑正整数之外，其他边界条件如小于等于 0 的整数也应考虑在内。在比较 0 和`(n & (n - 1))`的值时，需要用括号括起来避免优先级结合的问题。

### 复杂度分析

$$O(1)$$.

## 扩展

关于 2 的整数幂还有一道有意思的题，比如 [Next Power of 2 - GeeksforGeeks](http://www.geeksforgeeks.org/next-power-of-2/)，有兴趣的可以去围观下。

## Convert Integer A to Integer B

## Question

*   CC150, lintcode: [(181) Convert Integer A to Integer B](http://www.lintcode.com/en/problem/convert-integer-a-to-integer-b/)

```
Determine the number of bits required to convert integer A to integer B

Example
Given n = 31, m = 14,return 2

(31)10=(11111)2

(14)10=(01110)2 
```

## 题解

比较两个数不同的比特位个数，显然容易想到可以使用异或处理两个整数，相同的位上为 0，不同的位上为 1，故接下来只需将异或后 1 的个数求出即可。容易想到的方法是移位后和 1 按位与得到最低位的结果，使用计数器记录这一结果，直至最后操作数为 0 时返回最终值。这种方法需要遍历元素的每一位，有咩有更为高效的做法呢？还记得之前做过的 [O1 Check Power of 2](http://algorithm.yuanbin.me/zh-hans/math_and_bit_manipulation/o1_check_power_of_2.html) 吗？`x & (x - 1)`既然可以检查 2 的整数次幂，那么如何才能进一步得到所有 1 的个数呢？——将异或得到的数分拆为若干个 2 的整数次幂，计算得到有多少个 2 的整数次幂即可。

以上的分析过程对于正数来说是毫无问题的，但问题就在于如果出现了负数如何破？不确定的时候就来个实例测测看，以-2 为例，(-2) & (-2 - 1)的计算如下所示(简单起见这里以 8 位为准)：

```
 11111110 <==> -2   -2 <==> 11111110
+                          &
 11111111 <==> -1   -3 <==> 11111101
=                          =
 11111101                   11111100 
```

细心的你也许发现了对于负数来说，其表现也是我们需要的——`x & (x - 1)`的含义即为将二进制比特位的值为 1 的最低位置零。逐步迭代直至最终值为 0 时返回。

C/C++ 和 Java 中左溢出时会直接将高位丢弃，正好方便了我们的计算，但是在 Python 中就没这么幸运了，因为溢出时会自动转换类型，Orz... 所以使用 Python 时需要对负数专门处理，转换为求其补数中 0 的个数。

### Python

```
class Solution:
    """
    @param a, b: Two integer
    return: An integer
    """
    def bitSwapRequired(self, a, b):
        count = 0
        a_xor_b = a ^ b
        neg_flag = False
        if a_xor_b < 0:
            a_xor_b = abs(a_xor_b) - 1
            neg_flag = True
        while a_xor_b > 0:
            count += 1
            a_xor_b &= (a_xor_b - 1)

        # bit_wise = 32
        if neg_flag:
            count = 32 - count
        return count 
```

### C++

```
class Solution {
public:
    /**
     *@param a, b: Two integer
     *return: An integer
     */
    int bitSwapRequired(int a, int b) {
        int count = 0;
        int a_xor_b = a ^ b;
        while (a_xor_b != 0) {
            ++count;
            a_xor_b &= (a_xor_b - 1);
        }

        return count;
    }
}; 
```

### Java

```
class Solution {
    /**
     *@param a, b: Two integer
     *return: An integer
     */
    public static int bitSwapRequired(int a, int b) {
        int count = 0;
        int a_xor_b = a ^ b;
        while (a_xor_b != 0) {
            ++count;
            a_xor_b &= (a_xor_b - 1);
        }

        return count;
    }
}; 
```

### 源码分析

Python 中 int 溢出时会自动变为 long 类型，故处理负数时需要求补数中 0 的个数，间接求得原异或得到的数中 1 的个数。

考虑到负数的可能，C/C++, Java 中循环终止条件为`a_xor_b != 0`，而不是`a_xor_b > 0`.

### 复杂度分析

取决于异或后数中 1 的个数，`O(max(ones in a ^ b))`.

关于 Python 中位运算的一些坑总结在参考链接中。

## Reference

*   [BitManipulation - Python Wiki](https://wiki.python.org/moin/BitManipulation)
*   [5\. Expressions — Python 2.7.10rc0 documentation](https://docs.python.org/2/reference/expressions.html#shifting)
*   [Python 之位移操作符所带来的困惑 - 旁观者 - 博客园](http://www.cnblogs.com/zhengyun_ustc/archive/2009/10/14/shifting.html)

## Factorial Trailing Zeroes

## Question

*   leetcode: [Factorial Trailing Zeroes | LeetCode OJ](https://leetcode.com/problems/factorial-trailing-zeroes/)
*   lintcode: [(2) Trailing Zeros](http://www.lintcode.com/en/problem/trailing-zeros/)

```
Write an algorithm which computes the number of trailing zeros in n factorial.

Example
11! = 39916800, so the out should be 2

Challenge
O(log N) time 
```

## 题解 1 - Iterative

找阶乘数中末尾的连零数量，容易想到的是找相乘能为 10 的整数倍的数，如 $$2 \times 5$$, $$1 \times 10$$ 等，遥想当初做阿里笔试题时遇到过类似的题，当时想着算算 5 和 10 的个数就好了，可万万没想到啊，25 可以变为两个 5 相乘！真是蠢死了... 根据数论里面的知识，任何正整数都可以表示为它的质因数的乘积[^wikipedia]。所以比较准确的思路应该是计算质因数 5 和 2 的个数，取小的即可。质因数 2 的个数显然要大于 5 的个数，故只需要计算给定阶乘数中质因数中 5 的个数即可。原题的问题即转化为求阶乘数中质因数 5 的个数，首先可以试着分析下 100 以内的数，再试试 100 以上的数，聪明的你一定想到了可以使用求余求模等方法 :)

### Python

```
class Solution:
    # @param {integer} n
    # @return {integer}
    def trailingZeroes(self, n):
        if n < 0:
            return -1

        count = 0
        while n > 0:
            n /= 5
            count += n

        return count 
```

### C++

```
class Solution {
public:
    int trailingZeroes(int n) {
        if (n < 0) {
            return -1;
        }

        int count = 0;
        for (; n > 0; n /= 5) {
            count += (n / 5);
        }

        return count;
    }
}; 
```

### Java

```
public class Solution {
    public int trailingZeroes(int n) {
        if (n < 0) {
            return -1;
        }

        int count = 0;
        for (; n > 0; n /= 5) {
            count += (n / 5);
        }

        return count;
    }
} 
```

### 源码分析

1.  异常处理，小于 0 的数返回-1.
2.  先计算 5 的正整数幂都有哪些，不断使用 n / 5 即可知质因数 5 的个数。
3.  在循环时使用 `n /= 5` 而不是 `i *= 5`, 可有效防止溢出。

> **Warning** lintcode 和 leetcode 上的方法名不一样，在两个 OJ 上分别提交的时候稍微注意下。

### 复杂度分析

关键在于`n /= 5`执行的次数，时间复杂度 $$\log_5 n$$，使用了`count`作为返回值，空间复杂度 $$O(1)$$.

## 题解 2 - Recursive

可以使用迭代处理的程序往往用递归，而且往往更为优雅。递归的终止条件为`n <= 0`.

### Python

```
class Solution:
    # @param {integer} n
    # @return {integer}
    def trailingZeroes(self, n):
        if n == 0:
            return 0
        elif n < 0:
            return -1
        else:
            return n / 5 + self.trailingZeroes(n / 5) 
```

### C++

```
class Solution {
public:
    int trailingZeroes(int n) {
        if (n == 0) {
            return 0;
        } else if (n < 0) {
            return -1;
        } else {
            return n / 5 + trailingZeroes(n / 5);
        }
    }
}; 
```

### Java

```
public class Solution {
    public int trailingZeroes(int n) {
        if (n == 0) {
            return 0;
        } else if (n < 0) {
            return -1;
        } else {
            return n / 5 + trailingZeroes(n / 5);
        }
    }
} 
```

### 源码分析

这里将负数输入视为异常，返回-1 而不是 0\. 注意使用递归时务必注意收敛和终止条件的返回值。这里递归层数最多不超过 $$\log_5 n$$, 因此效率还是比较高的。

### 复杂度分析

递归层数最大为 $$\log_5 n$$, 返回值均在栈上，可以认为没有使用辅助的堆空间。

## Reference

*   [^wikipedia]: [Prime factor - Wikipedia, the free encyclopedia](http://en.wikipedia.org/wiki/Prime_factor)
*   [Count trailing zeroes in factorial of a number - GeeksforGeeks](http://www.geeksforgeeks.org/count-trailing-zeroes-factorial-number/)

## Unique Binary Search Trees

## Question

*   leetcode: [Unique Binary Search Trees | LeetCode OJ](https://leetcode.com/problems/unique-binary-search-trees/)
*   lintcode: [(163) Unique Binary Search Trees](http://www.lintcode.com/en/problem/unique-binary-search-trees/)

```
Given n, how many structurally unique BSTs (binary search trees)
that store values 1...n?

Example
Given n = 3, there are a total of 5 unique BST's.

1           3    3       2      1
 \         /    /       / \      \
  3      2     1       1   3      2
 /      /       \                  \
2     1          2                  3 
```

## 题解 1 - 两重循环

挺有意思的一道题，与数据结构和动态规划都有点关系。这两天在骑车路上和睡前都一直在想，始终未能找到非常明朗的突破口，直到看到这么一句话——『以 i 为根节点的树，其左子树由[0, i-1]构成， 其右子树由[i+1, n]构成。』这不就是 BST 的定义嘛！灵活运用下就能找到递推关系了。

容易想到这道题的动态规划状态为 count[n], count[n] 表示到正整数 i 为止的二叉搜索树个数。容易得到 count[1] = 1, 根节点为 1，count[2] = 2, 根节点可为 1 或者 2。那么 count[3] 的根节点自然可为 1，2，3\. 如果以 1 为根节点，那么根据 BST 的定义，2 和 3 只可能位于根节点 1 的右边；如果以 2 为根节点，则 1 位于左子树，3 位于右子树；如果以 3 为根节点，则 1 和 2 必位于 3 的左子树。

抽象一下，如果以 i 作为根节点，由基本的排列组合知识可知，其唯一 BST 个数为左子树的 BST 个数乘上右子树的 BST 个数。故对于 i 来说，其左子树由[0, i - 1]构成，唯一的 BST 个数为 count[i - 1], 右子树由[i + 1, n] 构成，其唯一的 BST 个数没有左子树直观，但是也有迹可循。对于两组有序数列「1, 2, 3] 和 [4, 5, 6]来说，**这两个有序数列分别组成的 BST 个数必然是一样的，因为 BST 的个数只与有序序列的大小有关，而与具体值没有关系。**所以右子树的 BST 个数为 count[n - i]，于是乎就得到了如下递推关系： $$count[i] = \sum _{j = 0} ^{i - 1} (count[j] \cdot count[i - j - 1])$$

网上有很多用 count[3] 的例子来得到递推关系，恕本人愚笨，在没有从 BST 的定义和有序序列个数与 BST 关系分析的基础上，我是不敢轻易说就能得到如上状态转移关系的。

### Python

```
class Solution:
    # @paramn n: An integer
    # @return: An integer
    def numTrees(self, n):
        if n < 0:
            return -1

        count = [0] * (n + 1)
        count[0] = 1
        for i in xrange(1, n + 1):
            for j in xrange(i):
                count[i] += count[j] * count[i - j - 1]

        return count[n] 
```

### C++

```
class Solution {
public:
    /**
     * @paramn n: An integer
     * @return: An integer
     */
    int numTrees(int n) {
        if (n < 0) {
            return -1;
        }

        vector<int> count(n + 1);
        count[0] = 1;
        for (int i = 1; i != n + 1; ++i) {
            for (int j = 0; j != i; ++j) {
                count[i] += count[j] * count[i - j - 1];
            }
        }

        return count[n];
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @paramn n: An integer
     * @return: An integer
     */
    public int numTrees(int n) {
        if (n < 0) {
            return -1;
        }

        int[] count = new int[n + 1];
        count[0] = 1;
        for (int i = 1; i < n + 1; ++i) {
            for (int j = 0; j < i; ++j) {
                count[i] += count[j] * count[i - j - 1];
            }
        }

        return count[n];
    }
} 
```

### 源码分析

1.  对 n 小于 0 特殊处理。
2.  初始化大小为 n + 1 的数组，初始值为 0，但对 count[0] 赋值为 1.
3.  两重 for 循环递推求得 count[i] 的值。
4.  返回 count[n] 的值。

由于需要处理空节点的子树，故初始化 count[0] 为 1 便于乘法处理。其他值必须初始化为 0，因为涉及到累加操作。

### 复杂度分析

一维数组大小为 n + 1, 空间复杂度为 $$O(n + 1)$$. 两重 for 循环等差数列求和累计约 $$n² / 2$$, 故时间复杂度为 $$O(n²)$$. 此题为 Catalan number 的一种，除了平方时间复杂度的解法外还存在 $$O(n)$$ 的解法，欲练此功，先戳 [Wikipedia](http://en.wikipedia.org/wiki/Catalan_number) 的链接。

## Reference

*   [^fisherlei]: [水中的鱼: [LeetCode] Unique Binary Search Trees, Solution](http://fisherlei.blogspot.com/2013/03/leetcode-unique-binary-search-trees.html)
*   [Unique Binary Search Trees | 九章算法](http://www.jiuzhang.com/solutions/unique-binary-search-trees/)
*   [Catalan number - Wikipedia, the free encyclopedia](http://en.wikipedia.org/wiki/Catalan_number)

## Update Bits

## Question

*   CTCI: [(179) Update Bits](http://www.lintcode.com/en/problem/update-bits/)

```
Given two 32-bit numbers, N and M, and two bit positions, i and j.
Write a method to set all bits between i and j in N equal to M
(e g , M becomes a substring of N located at i and starting at j)

Example
Given N=(10000000000)2, M=(10101)2, i=2, j=6

return N=(10001010100)2

Note
In the function, the numbers N and M will given in decimal,
you should also return a decimal number.

Challenge
Minimum number of operations?

Clarification
You can assume that the bits j through i have enough space to fit all of M.
That is, if M=10011，
you can assume that there are at least 5 bits between j and i.
You would not, for example, have j=3 and i=2,
because M could not fully fit between bit 3 and bit 2. 
```

## 题解

Cracking The Coding Interview 上的题，题意简单来讲就是使用 M 代替 N 中的第`i`位到第`j`位。很显然，我们需要借用掩码操作。大致步骤如下：

1.  得到第`i`位到第`j`位的比特位为 0，而其他位均为 1 的掩码`mask`。
2.  使用`mask`与 N 进行按位与，清零 N 的第`i`位到第`j`位。
3.  对 M 右移`i`位，将 M 放到 N 中指定的位置。
4.  返回 N | M 按位或的结果。

获得掩码`mask`的过程可参考 CTCI 书中的方法，先获得掩码(1111...000...111)的左边部分，然后获得掩码的右半部分，最后左右按位或即为最终结果。

### C++ <i class="fa fa-bug"></i>

```
class Solution {
public:
    /**
     *@param n, m: Two integer
     *@param i, j: Two bit positions
     *return: An integer
     */
    int updateBits(int n, int m, int i, int j) {
        int ones = ~0;
        int left = ones << (j + 1);
        int right = ((1 << i) - 1);
        int mask = left | right;

        return (n & mask) | (m << i);
    }
}; 
```

### 源码分析

在给定测试数据`[-521,0,31,31]`时出现了 WA, 也就意味着目前这段程序是存在 bug 的，此时`m = 0, i = 31, j = 31`，仔细瞅瞅到底是哪几行代码有问题？本地调试后发现问题出在`left`那一行，`left`移位后仍然为`ones`, 这是为什么呢？在`j`为 31 时`j + 1`为 32，也就是说此时对`left`位移的操作已经超出了此时`int`的最大位宽！

### C++

```
class Solution {
public:
    /**
     *@param n, m: Two integer
     *@param i, j: Two bit positions
     *return: An integer
     */
    int updateBits(int n, int m, int i, int j) {
        int ones = ~0;
        int mask = 0;
        if (j < 31) {
            int left = ones << (j + 1);
            int right = ((1 << i) - 1);
            mask = left | right;
        } else {
            mask = (1 << i) - 1;
        }

        return (n & mask) | (m << i);
    }
}; 
```

### 源码分析

使用`~0`获得全 1 比特位，在`j == 31`时做特殊处理，即不必求`left`。求掩码的右侧 1 时使用了`(1 << i) - 1`, 题中有保证第`i`位到第`j`位足以容纳 M, 故不必做溢出处理。

### 复杂度分析

时间复杂度和空间复杂度均为 $$O(1)$$.

### C++

```
class Solution {
public:
    /**
     *@param n, m: Two integer
     *@param i, j: Two bit positions
     *return: An integer
     */
    int updateBits(int n, int m, int i, int j) {
        // get the bit width of input integer
        int bitwidth = 8 * sizeof(n);
        int ones = ~0;
        // use unsigned for logical shift
        unsigned int mask = ones << (bitwidth - (j - i + 1));
        mask = mask >> (bitwidth - 1 - j);

        return (n & (~mask)) | (m << i);
    }
}; 
```

### 源码分析

之前的实现需要使用`if`判断，但实际上还有更好的做法，即先获得`mask`的反码，最后取反即可。但这种方法需要提防有符号数，因为 C/C++ 中对有符号数的移位操作为算术移位，也就是说对负数右移时会在前面补零。解决办法可以使用无符号数定义`mask`.

按题意 int 的位数为 32，但考虑到通用性，可以使用`sizeof`获得其真实位宽。

### 复杂度分析

时间复杂度和空间复杂度均为 $$O(1)$$.

## Reference

*   [c++ - logical shift right on signed data - Stack Overflow](http://stackoverflow.com/questions/13221369/logical-shift-right-on-signed-data)
*   [Update Bits | 九章算法](http://www.jiuzhang.com/solutions/update-bits/)
*   *CTCI 5th Chapter 9.5 中文版* p163