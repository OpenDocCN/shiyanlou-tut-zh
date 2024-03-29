# 第 10 节 Math and Bit Manipulation - 数学和位运算（二）

## Hash Function

## Question

*   lintcode: [(128) Hash Function](http://www.lintcode.com/en/problem/hash-function/)

### Problem Statement

In data structure Hash, hash function is used to convert a string(or any other type) into an integer smaller than hash size and bigger or equal to zero. The objective of designing a hash function is to "hash" the key as unreasonable as possible. A good hash function can avoid collision as less as possible. A widely used hash function algorithm is using a magic number 33, consider any string as a 33 based big integer like follow:

hashcode("abcd") = (ascii(a) * $$33³$$ + ascii(b) * $$33²$$ + ascii(c) *33 + ascii(d)) % HASH_SIZE

= (97* $$33³$$ + 98 * $$33²$$ + 99 * 33 +100) % HASH_SIZE

= 3595978 % HASH_SIZE

here HASH*SIZE is the capacity of the hash table (you can assume a hash table is like an array with index 0 ~ HASH*SIZE-1).

Given a string as a key and the size of hash table, return the hash value of this key.f

#### Example

For key="abcd" and size=100, return 78

#### Clarification

For this problem, you are not necessary to design your own hash algorithm or consider any collision issue, you just need to implement the algorithm as described.

## 题解 1

基本实现题，大多数人看到题目的直觉是按照定义来递推不就得了嘛，但其实这里面大有玄机，因为在字符串较长时使用 long 型来计算 33 的幂会溢出！所以这道题的关键在于如何处理**大整数溢出**。对于整数求模，`(a * b) % m = a % m * b % m` 这个基本公式务必牢记。根据这个公式我们可以大大降低时间复杂度和规避溢出。

### Java

```
class Solution {
    /**
     * @param key: A String you should hash
     * @param HASH_SIZE: An integer
     * @return an integer
     */
    public int hashCode(char[] key,int HASH_SIZE) {
        if (key == null || key.length == 0) return -1;

        long hashSum = 0;
        for (int i = 0; i < key.length; i++) {
            hashSum += key[i] * modPow(33, key.length - i - 1, HASH_SIZE);
            hashSum %= HASH_SIZE;
        }

        return (int)hashSum;
    }

    private long modPow(int base, int n, int mod) {
        if (n == 0) {
            return 1;
        } else if (n == 1) {
            return base % mod;
        } else if (n % 2 == 0) {
            long temp = modPow(base, n / 2, mod);
            return (temp % mod) * (temp % mod) % mod;
        } else {
            return (base % mod) * modPow(base, n - 1, mod) % mod;
        }
    }
} 
```

### 源码分析

题解 1 属于较为直观的解法，只不过在计算 33 的幂时使用了私有方法`modPow`, 这个方法使用了对数级别复杂度的算法，可防止 TLE 的产生。注意两个 int 型数据在相乘时可能会溢出，故对中间结果的存储需要使用 long.

### 复杂度分析

遍历加求`modPow`，时间复杂度 $$O(n \log n)$$, 空间复杂度 $$O(1)$$. 当然也可以使用哈希表的方法将幂求模的结果保存起来，这样一来空间复杂度就是 $$O(n)$$, 不过时间复杂度为 $$O(n)$$.

## 题解 2 - 巧用求模公式

从题解 1 中我们可以看到其时间复杂度还是比较高的，作为基本库来使用是比较低效的。我们从范例`hashcode("abc")`为例进行说明。

$$ \begin{array}{cl} hashcode(abc) & = & (a \times 33^{2} + b \times 33 + c)\% M\ & = & (33(33\times a+b)+c)\% M\ & = & (33(33(33\times0+a)+b)+c)\% M \end{array} $$

再根据 $$(a \times b) \% M = (a \% M) \times (b \% M)$$

从中可以看出使用迭代的方法较容易实现。

### Java

```
class Solution {
    /**
     * @param key: A String you should hash
     * @param HASH_SIZE: An integer
     * @return an integer
     */
    public int hashCode(char[] key,int HASH_SIZE) {
        if (key == null || key.length == 0) return -1;

        long hashSum = 0;
        for (int i = 0; i < key.length; i++) {
            hashSum = 33 * hashSum + key[i];
            hashSum %= HASH_SIZE;
        }

        return (int)hashSum;
    }
} 
```

### 源码分析

精华在`hashSum = 33 * hashSum + key[i];`

### 复杂度分析

时间复杂度 $$O(n)$$, 空间复杂度 $$O(1)$$.

## Count 1 in Binary

## Question

*   lintcode: [(365) Count 1 in Binary](http://www.lintcode.com/en/problem/count-1-in-binary/)

```
Count how many 1 in binary representation of a 32-bit integer.

Example
Given 32, return 1

Given 5, return 2

Given 1023, return 9

Challenge
If the integer is n bits with m 1 bits. Can you do it in O(m) time? 
```

## 题解

题 [O1 Check Power of 2](http://algorithm.yuanbin.me/zh-hans/math_and_bit_manipulation/o1_check_power_of_2.html) 的进阶版，`x & (x - 1)` 的含义为去掉二进制数中 1 的最后一位，无论 x 是正数还是负数都成立。

### C++

```
class Solution {
public:
    /**
     * @param num: an integer
     * @return: an integer, the number of ones in num
     */
    int countOnes(int num) {
        int count=0;
        while (num) {
            num &= num-1;
            count++;
        }
        return count;
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param num: an integer
     * @return: an integer, the number of ones in num
     */
    public int countOnes(int num) {
        int count = 0;
        while (num != 0) {
            num = num & (num - 1);
            count++;
        }

        return count;
    }
} 
```

### 源码分析

累加计数器即可。

### 复杂度分析

这种算法依赖于数中 1 的个数，时间复杂度为 $$O(m)$$. 空间复杂度 $$O(1)$$.

## Reference

*   [Number of 1 bits | LeetCode](http://articles.leetcode.com/2010/09/number-of-1-bits.html) - 评论中有关于不同算法性能的讨论

## Fibonacci

## Question

*   lintcode: [(366) Fibonacci](http://www.lintcode.com/en/problem/fibonacci/)

### Problem Statement

Find the *N*th number in Fibonacci sequence.

A Fibonacci sequence is defined as follow:

*   The first two numbers are 0 and 1.
*   The *i* th number is the sum of *i*-1 th number and *i*-2 th number.

The first ten numbers in Fibonacci sequence is:

`0, 1, 1, 2, 3, 5, 8, 13, 21, 34 ...`

#### Example

Given `1`, return `0`

Given `2`, return `1`

Given `10`, return `34`

#### Note

The *N*th fibonacci number won't exceed the max value of signed 32-bit integer in the test cases.

## 题解

斐波那契数列使用递归极其容易实现，其实使用非递归的方法也很容易，不断向前滚动即可。

### C++

```
class Solution{
public:
    /**
     * @param n: an integer
     * @return an integer f(n)
     */
    int fibonacci(int n) {
        if (n <= 0) return -1;
        if (n == 1) return 0;
        if (n == 2) return 1;

        int fn = 0, fn1 = 0, fn2 = 1;
        for (int i = 3; i <= n; i++) {
            fn = fn1 + fn2;
            fn1 = fn2;
            fn2 = fn;
        }
        return fn;
    }
}; 
```

### Java

```
class Solution {
    /**
     * @param n: an integer
     * @return an integer f(n)
     */
    public int fibonacci(int n) {
        if (n <= 0) return -1;
        if (n == 1) return 0;
        if (n == 2) return 1;

        int fn = 0, fn1 = 1, fn2 = 0;
        for (int i = 3; i <= n; i++) {
            fn = fn1 + fn2;
            fn2 = fn1;
            fn1 = fn;
        }

        return fn;
    }
} 
```

### 源码分析

1.  corner cases
2.  初始化 fn, fn1, fn2, 建立地推关系。
3.  注意 fn, fn2, fn1 的递推顺序。

### 复杂度分析

遍历一次，时间复杂度为 $$O(n)$$, 使用了两个额外变量，空间复杂度为 $$O(1)$$.

## A plus B Problem

## Question

*   lintcode: [(1) A + B Problem](http://www.lintcode.com/en/problem/a-b-problem/)

```
Write a function that add two numbers A and B.
You should not use + or any arithmetic operators.

Example
Given a=1 and b=2 return 3

Note
There is no need to read data from standard input stream.
Both parameters are given in function aplusb,
you job is to calculate the sum and return it.
Challenge
Of course you can just return a + b to get accepted.
But Can you challenge not do it like that?
Clarification
Are a and b both 32-bit integers?
Yes.
Can I use bit operation?

Sure you can. 
```

## 题解

不用加减法实现加法，类似数字电路中的全加器，异或求得部分和，相与求得进位，最后将进位作为加法器的输入，典型的递归实现思路。

### Java

```
class Solution {
    /*
     * param a: The first integer
     * param b: The second integer
     * return: The sum of a and b
     */
    public int aplusb(int a, int b) {
        int result = a ^ b;
        int carry = a & b;
        carry <<= 1;
        if (carry != 0) {
            result = aplusb(result, carry);
        }

        return result;
    }
} 
```

### 源码分析

递归步为进位是否为 0，为 0 时返回。

### 复杂度分析

取决于进位，近似为 $$O(1)$$. 使用了部分额外变量，空间复杂度为 $$O(1)$$.

## Print Numbers by Recursion

## Question

*   lintcode: [(371) Print Numbers by Recursion](http://www.lintcode.com/en/problem/print-numbers-by-recursion/)

```
Print numbers from 1 to the largest number with N digits by recursion.

Example
Given N = 1, return [1,2,3,4,5,6,7,8,9].

Given N = 2, return [1,2,3,4,5,6,7,8,9,10,11,12,...,99].

Note
It's pretty easy to do recursion like:

recursion(i) {
    if i > largest number:
        return
    results.add(i)
    recursion(i + 1)
}
however this cost a lot of recursion memory as the recursion depth maybe very large.
Can you do it in another way to recursive with at most N depth?

Challenge
Do it in recursion, not for-loop. 
```

## 题解

从小至大打印 N 位的数列，正如题目中所提供的 `recursion(i)`, 解法简单粗暴，但问题在于 N 稍微大一点时栈就溢出了，因为递归深度太深了。能联想到的方法大概有两种，一种是用排列组合的思想去解释，把 0~9 当成十个不同的数(字符串表示)，塞到 N 个坑位中，这个用 DFS 来解应该是可行的；另一个则是使用数学方法，依次递归递推，比如 N=2 可由 N=1 递归而来，具体方法则是乘 10 进位加法。题中明确要求递归深度最大不超过 N, 故 DFS 方法比较危险。

### Java

```
public class Solution {
    /**
     * @param n: An integer.
     * return : An array storing 1 to the largest number with n digits.
     */
    public List<Integer> numbersByRecursion(int n) {
        List<Integer> result = new ArrayList<Integer>();
        if (n <= 0) {
            return result;
        }
        helper(n, result);
        return result;
    }

    private void helper(int n, List<Integer> ret) {
        if (n == 0) return;
        helper(n - 1, ret);
        // current base such as 10, 20, 30...
        int base = (int)Math.pow(10, n - 1);
        // get List size before for loop
        int size = ret.size();
        for (int i = 1; i < 10; i++) {
            // add 10, 100, 1000...
            ret.add(i * base);
            for (int j = 0; j < size; j++) {
                // add 11, 12, 13...
                ret.add(ret.get(j) + base * i);
            }
        }
    }
} 
```

### 源码分析

递归步的截止条件`n == 0`, 由于需要根据之前 N-1 位的数字递推，`base` 每次递归一层都需要乘 10，`size`需要在`for`循环之前就确定。

### 复杂度分析

添加 $$10^n$$ 个元素，时间复杂度 $$O(10^n)$$, 空间复杂度 $$O(1)$$.

## Reference

*   [Lintcode: Print Numbers by Recursion | codesolutiony](https://codesolutiony.wordpress.com/2015/05/21/lintcode-print-numbers-by-recursion/)

## Majority Number

## Question

*   leetcode: [Majority Element | LeetCode OJ](https://leetcode.com/problems/majority-element/)
*   lintcode: [(46) Majority Number](http://www.lintcode.com/en/problem/majority-number/)

```
Given an array of integers, the majority number is
the number that occurs more than half of the size of the array. Find it.

Example
Given [1, 1, 1, 1, 2, 2, 2], return 1

Challenge
O(n) time and O(1) extra space 
```

## 题解

找出现次数超过一半的数，使用哈希表统计不同数字出现的次数，超过二分之一即返回当前数字。这种方法非常简单且容易实现，但会占据过多空间，注意到题中明确表明要找的数会超过二分之一，这里的隐含条件不是那么容易应用。既然某个数超过二分之一，那么用这个数和其他数进行 PK，不同的计数器都减一**（核心在于两两抵消）**，相同的则加 1，最后返回计数器大于 0 的即可。综上，我们需要一辅助数据结构如`pair<int, int>`.

### C++

```
int majorityNumber(vector<int> nums) {
    if (nums.empty()) return -1;

    int k = -1, count = 0;
    for (auto n : nums) {
        if (!count) k = n;
        if (k == n) count++;
        else count--;
    }
    return k;
} 
```

### Java

```
public class Solution {
    /**
     * @param nums: a list of integers
     * @return: find a  majority number
     */
    public int majorityNumber(ArrayList<Integer> nums) {
        if (nums == null || nums.isEmpty()) return -1;

        // pair<key, count>
        int key = -1, count = 0;
        for (int num : nums) {
            // re-initialize
            if (count == 0) {
                key = num;
                count = 1;
                continue;
            }
            // increment/decrement count
            if (key == num) {
                count++;
            } else {
                count--;
            }
        }

        return key;
    }
} 
```

### 源码分析

初始化`count = 0`, 遍历数组时需要先判断`count == 0`以重新初始化。

### 复杂度分析

时间复杂度 $$O(n)$$, 空间复杂度 $$O(1)$$.

## Majority Number II

## Question

*   leetcode: [Majority Element II | LeetCode OJ](https://leetcode.com/problems/majority-element-ii/)
*   lintcode: [(47) Majority Number II](http://www.lintcode.com/en/problem/majority-number-ii/)

```
Given an array of integers,
the majority number is the number that occurs more than 1/3 of the size of the array.

Find it.

Example
Given [1, 2, 1, 2, 1, 3, 3], return 1.

Note
There is only one majority number in the array.

Challenge
O(n) time and O(1) extra space. 
```

## 题解

题 [Majority Number](http://algorithm.yuanbin.me/zh-hans/math_and_bit_manipulation/majority_number.html) 的升级版，之前那道题是『两两抵消』，这道题自然则需要『三三抵消』，不过『三三抵消』需要注意不少细节，比如两个不同数的添加顺序和添加条件。

### C++

```
class Solution {
public:
    /**
     * @param nums: A list of integers
     * @return: The majority number occurs more than 1/3.
     */
    int majorityNumber(vector<int> nums) {
        if (nums.empty()) return -1;

        int k1 = 0, k2 = 0, c1 = 0, c2 = 0;
        for (auto n : nums) {
            if (!c1 || k1 == n) {
                k1 = n;
                c1++;
            } else if (!c2 || k2 == n) {
                k2 = n;
                c2++;
            } else {
                c1--;
                c2--;
            }
        }

        c1 = 0; 
        c2 = 0;
        for (auto n : nums) {
            if (n == k1) c1++;
            if (n == k2) c2++;
        }
        return c1 > c2 ? k1 : k2;
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param nums: A list of integers
     * @return: The majority number that occurs more than 1/3
     */
    public int majorityNumber(ArrayList<Integer> nums) {
        if (nums == null || nums.isEmpty()) return -1;

        // pair
        int key1 = -1, key2 = -1;
        int count1 = 0, count2 = 0;
        for (int num : nums) {
            if (count1 == 0) {
                key1 = num;
                count1 = 1;
                continue;
            } else if (count2 == 0 && key1 != num) {
                key2 = num;
                count2 = 1;
                continue;
            }
            if (key1 == num) {
                count1++;
            } else if (key2 == num) {
                count2++;
            } else {
                count1--;
                count2--;
            }
        }

        count1 = 0;
        count2 = 0;
        for (int num : nums) {
            if (key1 == num) {
                count1++;
            } else if (key2 == num) {
                count2++;
            }
        }
        return count1 > count2 ? key1 : key2;
    }
} 
```

### 源码分析

首先处理`count == 0`的情况，这里需要注意的是`count2 == 0 && key1 = num`, 不重不漏。最后再次遍历原数组也必不可少，因为由于添加顺序的区别，count1 和 count2 的大小只具有相对意义，还需要最后再次比较其真实计数器值。

### 复杂度分析

时间复杂度 $$O(n)$$, 空间复杂度 $$O(2 \times 2) = O(1)$$.

## Reference

*   [Majority Number II 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/majority-number-ii/)

## Majority Number III

## Question

*   lintcode: [(48) Majority Number III](http://www.lintcode.com/en/problem/majority-number-iii/)

```
Given an array of integers and a number k,
the majority number is the number that occurs more than 1/k of the size of the array.

Find it.

Example
Given [3,1,2,3,2,3,3,4,4,4] and k=3, return 3.

Note
There is only one majority number in the array.

Challenge
O(n) time and O(k) extra space 
```

## 题解

[Majority Number II](http://algorithm.yuanbin.me/zh-hans/math_and_bit_manipulation/majority_number_ii.html) 的升级版，有了前两道题的铺垫，此题的思路已十分明了，对 K-1 个数进行相互抵消，这里不太可能使用 key1, key2...等变量，用数组使用上不太方便，且增删效率不高，故使用哈希表较为合适，当哈希表的键值数等于 K 时即进行清理，当然更准备地来讲应该是等于 K-1 时清理。故此题的逻辑即为：1\. 更新哈希表，若遇哈希表 size == K 时则执行删除操作，最后遍历哈希表取真实计数器值，返回最大的 key.

### C++

```
class Solution {
public:
    /**
     * @param nums: A list of integers
     * @param k: As described
     * @return: The majority number
     */
    int majorityNumber(vector<int> nums, int k) {
        unordered_map<int, int> map;

        for (auto n : nums) {
           if (map.size() < k) map[n]++;
           else {
                if (map.count(n)) map[n]++;
                else {
                    map[n] = 1;
                    vector<int> keys;
                    for (auto &it : map) {
                        it.second--;
                        if (!it.second) keys.push_back(it.first);
                    }
                    for (int i : keys) map.erase(i);
                }
            }   
        }

        int mx = 0;
        int ret = 0;
        for (auto &it : map) {
            if (it.second > mx) {
                ret = it.first;
                mx = it.second;
            }
        }
        return ret;
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param nums: A list of integers
     * @param k: As described
     * @return: The majority number
     */
    public int majorityNumber(ArrayList<Integer> nums, int k) {
        HashMap<Integer, Integer> hash = new HashMap<Integer, Integer>();
        if (nums == null || nums.isEmpty()) return -1;

        // update HashMap
        for (int num : nums) {
            if (!hash.containsKey(num)) {
                hash.put(num, 1);
                if (hash.size() >= k) {
                    removeZeroCount(hash);
                }
            } else {
                hash.put(num, hash.get(num) + 1);
            }
        }

        // reset
        for (int key : hash.keySet()) {
            hash.put(key, 0);
        }
        for (int key : nums) {
            if (hash.containsKey(key)) {
                hash.put(key, hash.get(key) + 1);
            }
        }

        // find max
        int maxKey = -1, maxCount = 0;
        for (int key : hash.keySet()) {
            if (hash.get(key) > maxCount) {
                maxKey = key;
                maxCount = hash.get(key);
            }
        }

        return maxKey;
    }

    private void removeZeroCount(HashMap<Integer, Integer> hash) {
        Set<Integer> keySet = hash.keySet();
        for (int key : keySet) {
            hash.put(key, hash.get(key) - 1);
        }

        /* solution 1 */
        Iterator<Map.Entry<Integer, Integer>> it = hash.entrySet().iterator();
        while (it.hasNext()) {
            Map.Entry<Integer, Integer> entry = it.next();
            if(entry.getValue() == 0) {
                it.remove();
            }
        }

        /* solution 2 */
        // List<Integer> removeList = new ArrayList<>();
        // for (int key : keySet) {
        //     hash.put(key, hash.get(key) - 1);
        //     if (hash.get(key) == 0) {
        //         removeList.add(key);
        //     }
        // }
        // for (Integer key : removeList) {
        //     hash.remove(key);
        // }

        /* solution3 lambda expression for Java8 */
    }
} 
```

### 源码分析

此题的思路不算很难，但是实现起来还是有点难度的，**Java 中删除哈希表时需要考虑线程安全。**

### 复杂度分析

时间复杂度 $$O(n)$$, 使用了哈希表，空间复杂度 $$O(k)$$.

## Reference

*   [Majority Number III 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/majority-number-iii/)