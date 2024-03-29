# 第 11 节 Math and Bit Manipulation - 数学和位运算（三）

## Digit Counts

## Question

*   leetcode: [Number of Digit One | LeetCode OJ](https://leetcode.com/problems/number-of-digit-one/)
*   lintcode: [(3) Digit Counts](http://www.lintcode.com/en/problem/digit-counts/)

```
Count the number of k's between 0 and n. k can be 0 - 9.

Example
if n=12, k=1 in [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12],
we have FIVE 1's (1, 10, 11, 12) 
```

## 题解

leetcode 上的有点简单，这里以 Lintcode 上的为例进行说明。找出从 0 至整数 n 中出现数位 k 的个数，与整数有关的题大家可能比较容易想到求模求余等方法，但其实很多与整数有关的题使用字符串的解法更为便利。将整数 i 分解为字符串，然后遍历之，自增 k 出现的次数即可。

### C++

```
class Solution {
public:
    /*
     * param k : As description.
     * param n : As description.
     * return: How many k's between 0 and n.
     */
    int digitCounts(int k, int n) {
        char c = k + '0';
        int count = 0;
        for (int i = k; i <= n; i++) {
            for (auto s : to_string(i)) {
                if (s == c) count++;
            }
        }
        return count;
    }
}; 
```

### Java

```
class Solution {
    /*
     * param k : As description.
     * param n : As description.
     * return: An integer denote the count of digit k in 1..n
     */
    public int digitCounts(int k, int n) {
        int count = 0;
        char kChar = (char)(k + '0');
        for (int i = k; i <= n; i++) {
            char[] iChars = Integer.toString(i).toCharArray();
            for (char iChar : iChars) {
                if (kChar == iChar) count++;
            }
        }

        return count;
    }
} 
```

### 源码分析

太简单了，略

### 复杂度分析

时间复杂度 $$O(n \times L)$$, L 为 n 的最大长度，拆成字符数组，空间复杂度 $$O(L)$$.

## Ugly Number

## Question

*   leetcode: [Ugly Number | LeetCode OJ](https://leetcode.com/problems/ugly-number/)
*   lintcode: [(4) Ugly Number](http://www.lintcode.com/en/problem/ugly-number/)

```
Ugly number is a number that only have factors 3, 5 and 7.

Design an algorithm to find the Kth ugly number.
The first 5 ugly numbers are 3, 5, 7, 9, 15 ...

Example
If K=4, return 9.
Challenge
O(K log K) or O(K) time. 
```

## 题解 1 - TLE

Lintcode 和 Leetcode 中质数稍微有点区别，这里以 Lintcode 上的版本为例进行说明。寻找第 K 个丑数，丑数在这里的定义是仅能被 3，5，7 整除。简单粗暴的方法就是挨个检查正整数，数到第 K 个丑数时即停止。

### Java

```
class Solution {
    /**
     * @param k: The number k.
     * @return: The kth prime number as description.
     */
    public long kthPrimeNumber(int k) {
        if (k <= 0) return -1;

        int count = 0;
        long num = 1;
        while (count < k) {
            num++;
            if (isUgly(num)) {
                count++;
            }
        }

        return num;
    }

    private boolean isUgly(long num) {
        while (num % 3 == 0) {
            num /= 3;
        }
        while (num % 5 == 0) {
            num /= 5;
        }
        while (num % 7 == 0) {
            num /= 7;
        }

        return num == 1;
    }
} 
```

### 源码分析

判断丑数时依次约掉质因数 3，5，7，若处理完所有质因数 3,5,7 后不为 1 则不是丑数。自增 num 时应在判断是否为丑数之前。

### 复杂度分析

无法准确分析，但是估计在 $$O(n³)$$ 以上。

## 题解 2 - 二分查找

根据丑数的定义，它的质因数只含有 3, 5, 7, 那么根据这一点其实可以知道后面的丑数一定可以从前面的丑数乘 3,5,7 得到。那么可不可以将其递推关系用数学公式表示出来呢？

我大概做了下尝试，首先根据丑数的定义可以写成 $$U*k = 3^{x*3} \cdot 5^{x*5} \cdot 7^{x*7}$$, 那么 $$U*{k+1}$$ 和 $$U*k$$ 的不同则在于 $$x*3, x*5, x*7$$ 的不同，递推关系为 $$U*{k+1} = U*k \cdot \frac{3^{y*3} \cdot 5^{y*5} \cdot 7^{y*7}}{3^{z*3} \cdot 5^{z*5} \cdot 7^{z_7}}$$,将这些分数按照从小到大的顺序排列可在 $$O(K)$$ 的时间内解决，但是问题来了，得到这些具体的 $$y, z$$ 就需要费不少时间，且人工操作极易漏解。:( 所以这种解法只具有数学意义，没有想到好的实现方法。

除了这种找相邻递推关系的方法我们还可以尝试对前面的丑数依次乘 3, 5, 7，直至找到比当前最大的丑数大的一个数，对乘积后的三种不同结果取最小值即可得下一个最大的丑数。这种方法需要保存之前的 N 个丑数，由于已按顺序排好，天然的二分法。

### C++

```
class Solution {
public:
    /*
     * @param k: The number k.
     * @return: The kth prime number as description.
     */
    long long kthPrimeNumber(int k) {
        if (k <= 0) return -1;

        vector<long long> ugly(k + 1);
        ugly[0] = 1;
        int index = 0, index3 = 0, index5 = 0, index7 = 0;
        while (index <= k) {
            long long val = ugly[index3]*3 < ugly[index5]*5 ? ugly[index3]*3 : ugly[index5]*5;
            val = val < ugly[index7]*7 ? val : ugly[index7]*7;
            if (val == ugly[index3]*3) ++index3;
            if (val == ugly[index5]*5) ++index5;
            if (val == ugly[index7]*7) ++index7;
            ugly[++index] = val;
        }
        return ugly[k];
    }
}; 
```

### Java

```
class Solution {
    /**
     * @param k: The number k.
     * @return: The kth prime number as description.
     */
    public long kthPrimeNumber(int k) {
        if (k <= 0) return -1;

        ArrayList<Long> nums = new ArrayList<Long>();
        nums.add(1L);
        for (int i = 0; i < k; i++) {
            long minNextUgly = Math.min(nextUgly(nums, 3), nextUgly(nums, 5));
            minNextUgly = Math.min(minNextUgly, nextUgly(nums, 7));
            nums.add(minNextUgly);
        }

        return nums.get(nums.size() - 1);
    }

    private long nextUgly(ArrayList<Long> nums, int factor) {
        int size = nums.size();
        int start = 0, end = size - 1;
        while (start + 1 < end) {
            int mid = start + (end - start) / 2;
            if (nums.get(mid) * factor > nums.get(size - 1)) {
                end = mid;
            } else {
                start = mid;
            }
        }
        if (nums.get(start) * factor > nums.get(size - 1)) {
            return nums.get(start) * factor;
        }

        return nums.get(end) * factor;
    }
} 
```

### 源码分析

`nextUgly`根据输入的丑数数组和 factor 决定下一个丑数，`nums.add(1L)`中 1 后面需要加 L 表示 Long, 否则编译错误。

### 复杂度分析

找下一个丑数 $$O(\log K)$$, 循环 K 次，故总的时间复杂度近似 $$O(K \log K)$$, 使用了数组保存丑数，空间复杂度 $$O(K)$$.

## 题解 3 - 动态规划

TBD

## Reference

*   《剑指 Offer》第五章
*   [Ugly Numbers - GeeksforGeeks](http://www.geeksforgeeks.org/ugly-numbers/)

## Plus One

## Question

*   leetcode: [Plus One | LeetCode OJ](https://leetcode.com/problems/plus-one/)
*   lintcode: [(407) Plus One](http://www.lintcode.com/en/problem/plus-one/)

### Problem Statement

Given a non-negative number represented as an array of digits, plus one to the number.

The digits are stored such that the most significant digit is at the head of the list.

#### Example

Given [1,2,3] which represents 123, return [1,2,4].

Given [9,9,9] which represents 999, return [1,0,0,0].

## 题解

又是一道两个整数按数位相加的题，自后往前累加，处理下进位即可。这道题中是加 1，其实还可以扩展至加 2，加 3 等。

### C++

```
class Solution {
public:
    /**
     * @param digits a number represented as an array of digits
     * @return the result
     */
    vector<int> plusOne(vector<int>& digits) {
        return plusN(digits, 1);
    }

    vector<int> plusN(vector<int>& digits, int n) {
        vector<int> result;
        int carry = n;
        for (int i = digits.size() - 1; i >= 0; i--) {
            result.insert(result.begin(), (digits[i] + carry) % 10);
            carry = (digits[i] + carry) / 10;
        }
        if (carry) result.insert(result.begin(), carry);
        return result;
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param digits a number represented as an array of digits
     * @return the result
     */
    public int[] plusOne(int[] digits) {
        return plusDigit(digits, 1);
    }

    private int[] plusDigit(int[] digits, int digit) {
        if (digits == null || digits.length == 0) return null;

        // regard digit(0~9) as carry
        int carry = digit;
        int[] result = new int[digits.length];
        for (int i = digits.length - 1; i >= 0; i--) {
            result[i] = (digits[i] + carry) % 10;
            carry = (digits[i] + carry) / 10;
        }

        // carry == 1
        if (carry == 1) {
            int[] finalResult = new int[result.length + 1];
            finalResult[0] = 1;
            return finalResult;
        }

        return result;
    }
} 
```

### 源码分析

源码中单独实现了加任何数(0~9)的私有方法，更为通用，对于末尾第一个数，可以将要加的数当做进位处理，这样就不必单独区分最后一位了，十分优雅！

### 复杂度分析

Java 中需要返回数组，而这个数组在处理之前是不知道大小的，故需要对最后一个进位单独处理。时间复杂度 $$O(n)$$, 空间复杂度在最后一位有进位时恶化为 $$O(n)$$, 当然也可以通过两次循环使得空间复杂度为 $$O(1)$$.

## Reference

*   Soulmachine 的 leetcode 题解，将要加的数当做进位处理就是从这学到的。