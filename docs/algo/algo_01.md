# 第 1 节 课程说明与学习方法

## 一、课程说明

本实验课程通过在线编程学习 leetcode 等题目的实战经验。

我们希望这个教程能给你在学习算法的过程提供思路和源码方面的参考，但绝不鼓励死记硬背！请按照教程在线完成代码并理解原理。

### 课程来源

本课程来自[Bill Ryan](http://www.yuanbin.me/)的[《数据结构与算法/leetcode/lintcode 题解》](https://github.com/billryan/algorithm-exercise)项目中的 Part II。

本文参考了很多教材和博客，凡参考过的几乎都给出明确链接，如果不小心忘记了，欢迎在课程评论和问答中指出。

#### 相关链接

*   作者主页：[`www.yuanbin.me/`](http://www.yuanbin.me/)
*   项目 Github 地址：[`github.com/billryan/algorithm-exercise`](https://github.com/billryan/algorithm-exercise)

### 适用用户

我们假设本课程的用户已经掌握：

*   计算机基础知识
*   一门编程语言（Python，Java，C++等）
*   实验楼 Linux 环境的基本用法

### 许可证

本作品采用 **知识共享署名-相同方式共享 4.0 国际许可协议** 进行许可。** 传播此文档时请注意遵循以上许可协议 **。 关于本许可证的更多详情可参考 http://creativecommons.org/licenses/by-sa/4.0/

### 致谢

*   感谢作者[Bill Ryan](http://yuanbin.me)授权实验楼制作本实验课程。
*   感谢所有本项目的贡献者。

## 二、学习方法

### 在线实验

实验楼的理念是理论与实践相结合，点击开始实验进入实验环境后，按照左边的教程在环境中操作，需要注意：

*   课程提供 Java，C++，Python，C 的实现代码，请仔细阅读并在环境中实现自己的版本。
*   实验环境中已内置课程所需的开发工具，例如 Eclipse，Vim，g++，gcc，gdb 等。
*   如果对实验环境不熟悉，请先观看[实验入门视频教程](https://www.shiyanlou.com/questions/2544)。
*   如果对编程语言不熟悉，请先学习相关实验课程。
*   如果要保存实验代码，可以使用[我的代码库](https://www.shiyanlou.com/questions/360)功能。

### 课程问答

实验中遇到任何问题，可以在[实验楼问答](https://www.shiyanlou.com/questions)中提问。我们鼓励大家多多提问，也希望能回复同学提出的问题。交流与分享才能获得更快的成长。

如果发现文档中有任何错误，也可以在[实验楼问答](https://www.shiyanlou.com/questions)提出，我们会及时修复。

### 实验报告

实验报告是每次实验的数据汇总，可以在实验报告中使用 Markdown 记录实验心得和收获，使用方法见[实验报告的使用方法](https://www.shiyanlou.com/questions/670)。

**现在开始我们第一节字符串相关题目的学习和练习吧！**

## String - 字符串（一）

本章主要介绍字符串相关题目。

> 处理字符串操作相关问题时，常见的做法是从字符串尾部开始编辑，从后往前逆向操作。这么做的原因是因为字符串的尾部往往有足够空间，可以直接修改而不用担心覆盖字符串前面的数据。

摘自《程序员面试金典》

## strStr

## Question

*   leetcode: [Implement strStr() | LeetCode OJ](https://leetcode.com/problems/implement-strstr/)
*   lintcode: [lintcode - (13) strstr](http://www.lintcode.com/en/problem/strstr/)

### Problem Statement

For a given source string and a target string, you should output the **first** index(from 0) of target string in source string.

If target does not exist in source, just return `-1`.

#### Example

If source = `"source"` and target = `"target"`, return `-1`.

If source = `"abcdabcdefg"` and target = `"bcd"`, return `1`.

#### Challenge

O(n2) is acceptable. Can you implement an O(n) algorithm? (hint: *KMP*)

#### Clarification

Do I need to implement KMP Algorithm in a real interview?

*   Not necessary. When you meet this problem in a real interview, the interviewer may just want to test your basic implementation ability. But make sure your confirm with the interviewer first.

## 题解

对于字符串查找问题，可使用双重 for 循环解决，效率更高的则为 KMP 算法。双重 for 循环的使用较有讲究，因为这里需要考虑目标字符串比源字符串短的可能。对目标字符串的循环肯定是必要的，所以可以优化的地方就在于如何访问源字符串了。简单直观的解法是利用源字符串的长度作为 for 循环的截止索引，这种方法需要处理源字符串中剩余长度不足以匹配目标字符串的情况，而更为高效的方案则为仅遍历源字符串中有可能和目标字符串匹配的部分索引。

### Python

```
class Solution:
    def strStr(self, source, target):
        if source is None or target is None:
            return -1

        for i in range(len(source) - len(target) + 1):
            for j in range(len(target)):
                if source[i + j] != target[j]:
                    break
            else:  # no break
                return i
        return -1 
```

### C

```
int strStr(char* haystack, char* needle) {
    if (haystack == NULL || needle == NULL) return -1;

    const int len_h = strlen(haystack);
    const int len_n = strlen(needle);
    for (int i = 0; i < len_h - len_n + 1; i++) {
        int j = 0;
        for (; j < len_n; j++) {
            if (haystack[i+j] != needle[j]) {
                break;
            }
        }
        if (j == len_n) return i;
    }

    return -1;
} 
```

### C++

```
class Solution {
public:
    int strStr(string haystack, string needle) {
        if (haystack.empty() && needle.empty()) return 0;
        if (haystack.empty()) return -1;
        if (needle.empty()) return 0;
        // in case of overflow for negative
        if (haystack.size() < needle.size()) return -1;

        for (int i = 0; i < haystack.size() - needle.size() + 1; i++) {
            string::size_type j = 0;
            for (; j < needle.size(); j++) {
                if (haystack[i + j] != needle[j]) break;
            }
            if (j == needle.size()) return i;
        }
        return -1;
    }
}; 
```

### Java

```
public class Solution {
    public int strStr(String haystack, String needle) {
        if (haystack == null && needle == null) return 0;
        if (haystack == null) return -1;
        if (needle == null) return 0;

        for (int i = 0; i < haystack.length() - needle.length() + 1; i++) {
            int j = 0;
            for (; j < needle.length(); j++) {
                if (haystack.charAt(i+j) != needle.charAt(j)) break;
            }
            if (j == needle.length()) return i;
        }

        return -1;
    }
} 
```

### 源码分析

1.  边界检查：`haystack(source)`和`needle(target)`有可能是空串。
2.  边界检查之下标溢出：注意变量`i`的循环判断条件，如果用的是`i < source.length()`则在后面的`source.charAt(i + j)`时有可能溢出。
3.  代码风格：
    *   运算符`==`两边应加空格
    *   变量名不要起`s1`s2</code>这类，要有意义，如<code>target`source`
    *   Java 代码的大括号一般在同一行右边，C++ 代码的大括号一般另起一行
    *   int i, j;`声明前有一行空格，是好的代码风格
4.  是否在 for 的条件中声明`i`,`j`，这个视情况而定，如果需要在循环外再使用时，则须在外部初始化，否则没有这个必要。

需要注意的是有些题目要求并不是返回索引，而是返回字符串，此时还需要调用相应语言的`substring`方法。Python3 中用`range`替换了`xrange`，Python2 中使用`xrange`效率略高一些。 另外需要注意的是 Python 代码中的`else`接的是`for` 而不是`if`, 其含义为`no break`, 属于比较 Pythonic 的用法，有兴趣的可以参考 [4\. More Control Flow Tools](https://docs.python.org/3/tutorial/controlflow.html) 的 4.4 节和 [if statement - Why does python use 'else' after for and while loops?](http://stackoverflow.com/questions/9979970/why-does-python-use-else-after-for-and-while-loops)

### 复杂度分析

双重 for 循环，时间复杂度最坏情况下为 $$O((n-m)*m)$$.

## Two Strings Are Anagrams

## Question

*   lintcode: [(158) Two Strings Are Anagrams](http://www.lintcode.com/en/problem/two-strings-are-anagrams/)

```
Write a method anagram(s,t) to decide if two strings are anagrams or not.

Example
Given s="abcd", t="dcab", return true.

Challenge
O(n) time, O(1) extra space 
```

## 题解 1 - hashmap 统计字频

判断两个字符串是否互为变位词，若区分大小写，考虑空白字符时，直接来理解可以认为两个字符串的拥有各不同字符的数量相同。对于比较字符数量的问题常用的方法为遍历两个字符串，统计其中各字符出现的频次，若不等则返回`false`. 有很多简单字符串类面试题都是此题的变形题。

### Python

```
class Solution:
    """
    @param s: The first string
    @param b: The second string
    @return true or false
    """
    def anagram(self, s, t):
        return collections.Counter(s) == collections.Counter(t) 
```

### C++

```
class Solution {
public:
    /**
     * @param s: The first string
     * @param b: The second string
     * @return true or false
     */
    bool anagram(string s, string t) {
        if (s.empty() || t.empty()) {
            return false;
        }
        if (s.size() != t.size()) {
            return false;
        }

        int letterCount[256] = {0};

        for (int i = 0; i != s.size(); ++i) {
            ++letterCount[s[i]];
            --letterCount[t[i]];
        }
        for (int i = 0; i != t.size(); ++i) {
            if (letterCount[t[i]] != 0) {
                return false;
            }
        }

        return true;
    }
}; 
```

### 源码分析

1.  两个字符串长度不等时必不可能为变位词(需要注意题目条件灵活处理)。
2.  初始化含有 256 个字符的计数器数组。
3.  对字符串 s 自增，字符串 t 递减，再次遍历判断`letterCount`数组的值，小于 0 时返回`false`.

在字符串长度较长(大于所有可能的字符数)时，还可对第二个`for`循环做进一步优化，即`t.size() > 256`时，使用 256 替代`t.size()`, 使用`i`替代`t[i]`.

### 复杂度分析

两次遍历字符串，时间复杂度最坏情况下为 $$O(n)$$, 使用了额外的数组，空间复杂度 $$O(1)$$.

## 题解 2 - 排序字符串

另一直接的解法是对字符串先排序，若排序后的字符串内容相同，则其互为变位词。题解 1 中使用 hashmap 的方法对于比较两个字符串是否互为变位词十分有效，但是在比较多个字符串时，使用 hashmap 的方法复杂度则较高。

### Python

```
class Solution:
    """
    @param s: The first string
    @param b: The second string
    @return true or false
    """
    def anagram(self, s, t):
        return sorted(s) == sorted(t) 
```

### C++

```
class Solution {
public:
    /**
     * @param s: The first string
     * @param b: The second string
     * @return true or false
     */
    bool anagram(string s, string t) {
        if (s.empty() || t.empty()) {
            return false;
        }
        if (s.size() != t.size()) {
            return false;
        }

        sort(s.begin(), s.end());
        sort(t.begin(), t.end());

        if (s == t) {
            return true;
        } else {
            return false;
        }
    }
}; 
```

### 源码分析

对字符串 s 和 t 分别排序，而后比较是否含相同内容。对字符串排序时可以采用先统计字频再组装成排序后的字符串，效率更高一点。

### 复杂度分析

C++的 STL 中 sort 的时间复杂度介于 $$O(n)$$ 和 $$O(n²)$$之间，判断`s == t`时间复杂度最坏为 $$O(n)$$.

## Reference

*   *CC150 Chapter 9.1* 中文版 p109

## Compare Strings

## Question

*   lintcode: [(55) Compare Strings](http://www.lintcode.com/en/problem/compare-strings/)

```
Compare two strings A and B, determine whether A contains all of the characters in B.

The characters in string A and B are all Upper Case letters.

Example
For A = "ABCD", B = "ABC", return true.

For A = "ABCD" B = "AABC", return false. 
```

## 题解

题 [Two Strings Are Anagrams | Data Structure and Algorithm](http://algorithm.yuanbin.me/zh-hans/string/two_strings_are_anagrams.html) 的变形题。题目意思是问 B 中的所有字符是否都在 A 中，而不是单个字符。比如 B="AABC"包含两个「A」，而 A="ABCD"只包含一个「A」，故返回 false. 做题时注意题意，必要时可向面试官确认。

既然不是类似 strstr 那样的匹配，直接使用两重循环就不太合适了。题目中另外给的条件则是 A 和 B 都是全大写单词，理解题意后容易想到的方案就是先遍历 A 和 B 统计各字符出现的频次，然后比较频次大小即可。嗯，祭出万能的哈希表。

### Python

Python 的`dict`就是 hash， 所以 python 在处理需要用到 hash 的地方非常方便。

```
import collections
class Solution:
    def compare_strings(self, A, B):
        # return a dict with default value set to 0
        letters = collections.defaultdict(int)
        for a in A:
            letters[a] += 1

        for b in B:
            if b not in letters:
                return False
            elif letters[b] <= 0:
                return False
            else:
                letters[b] -= 1
        return True 
```

### 源码解析

1.  异常处理，B 的长度大于 A 时必定返回`false`, 包含了空串的特殊情况。
2.  使用额外的辅助空间，统计各字符的频次。

### 复杂度分析

遍历一次 A 字符串，遍历一次 B 字符串，时间复杂度最坏 $$O(2n)$$, 空间复杂度为 $$O(26)$$.

### C++

```
class Solution {
public:
    /**
     * @param A: A string includes Upper Case letters
     * @param B: A string includes Upper Case letter
     * @return:  if string A contains all of the characters in B return true
     *           else return false
     */
    bool compareStrings(string A, string B) {
        if (A.size() < B.size()) {
            return false;
        }

        const int AlphabetNum = 26;
        int letterCount[AlphabetNum] = {0};
        for (int i = 0; i != A.size(); ++i) {
            ++letterCount[A[i] - 'A'];
        }
        for (int i = 0; i != B.size(); ++i) {
            --letterCount[B[i] - 'A'];
            if (letterCount[B[i] - 'A'] < 0) {
                return false;
            }
        }

        return true;
    }
}; 
```

### 源码解析

1.  异常处理，B 的长度大于 A 时必定返回`false`, 包含了空串的特殊情况。
2.  使用额外的辅助空间，统计各字符的频次。

### 复杂度分析

遍历一次 A 字符串，遍历一次 B 字符串，时间复杂度最坏 $$O(2n)$$, 空间复杂度为 $$O(26)$$.

## Anagrams

## Question

*   leetcode: [Anagrams | LeetCode OJ](https://leetcode.com/problems/anagrams/)
*   lintcode: [(171) Anagrams](http://www.lintcode.com/en/problem/anagrams/)

```
Given an array of strings, return all groups of strings that are anagrams.

Example
Given ["lint", "intl", "inlt", "code"], return ["lint", "inlt", "intl"].

Given ["ab", "ba", "cd", "dc", "e"], return ["ab", "ba", "cd", "dc"].
Note
All inputs will be in lower-case 
```

## 题解 1 - 双重`for`循环(TLE)

题 [Two Strings Are Anagrams](http://algorithm.yuanbin.me/zh-hans/string/two_strings_are_anagrams.html) 的升级版，容易想到的方法为使用双重`for`循环两两判断字符串数组是否互为变位字符串。但显然此法的时间复杂度较高。还需要 $$O(n)$$ 的数组来记录字符串是否被加入到最终结果中。

### Python

```
class Solution:
    # @param strs: A list of strings
    # @return: A list of strings
    # @return: A list of strings
    def anagrams(self, strs):

        if len(strs) < 2 :
            return strs
        result=[]
        visited=[False]*len(strs)
        for index1,s1 in enumerate(strs):
            hasAnagrams = False
            for index2,s2 in enumerate(strs):
                if index2 > index1 and not visited[index2] and self.isAnagrams(s1,s2):
                    result.append(s2)
                    visited[index2]=True
                    hasAnagrams = True
            if not visited[index1] and hasAnagrams:
                result.append(s1)
        return result

    def isAnagrams(self, str1, str2):
        if  sorted (str1) == sorted(str2):
                return True
        return False 
```

### C++

```
class Solution {
public:
    /**
     * @param strs: A list of strings
     * @return: A list of strings
     */
    vector<string> anagrams(vector<string> &strs) {
        if (strs.size() < 2) {
            return strs;
        }

        vector<string> result;
        vector<bool> visited(strs.size(), false);
        for (int s1 = 0; s1 != strs.size(); ++s1) {
            bool has_anagrams = false;
            for (int s2 = s1 + 1; s2 < strs.size(); ++s2) {
                if ((!visited[s2]) && isAnagrams(strs[s1], strs[s2])) {
                    result.push_back(strs[s2]);
                    visited[s2] = true;
                    has_anagrams = true;
                }
            }
            if ((!visited[s1]) && has_anagrams) result.push_back(strs[s1]);
        }

        return result;
    }

private:
    bool isAnagrams(string &s, string &t) {
        if (s.size() != t.size()) {
            return false;
        }

        const int AlphabetNum = 26;
        int letterCount[AlphabetNum] = {0};
        for (int i = 0; i != s.size(); ++i) {
            ++letterCount[s[i] - 'a'];
            --letterCount[t[i] - 'a'];
        }
        for (int i = 0; i != t.size(); ++i) {
            if (letterCount[t[i] - 'a'] < 0) {
                return false;
            }
        }

        return true;
    }
}; 
```

### 源码分析

1.  strs 长度小于等于 1 时直接返回。
2.  使用与 strs 等长的布尔数组表示其中的字符串是否被添加到最终的返回结果中。
3.  双重循环遍历字符串数组，注意去重即可。
4.  私有方法`isAnagrams`用于判断两个字符串是否互为变位词。

### 复杂度分析

私有方法`isAnagrams`最坏的时间复杂度为 $$O(2L)$$, 其中 $$L$$ 为字符串长度。双重`for`循环时间复杂度近似为 $$\frac {1}{2} O(n²)$$, $$n$$ 为给定字符串数组数目。总的时间复杂度近似为 $$O(n² L)$$. 使用了 Vector String "visited"，空间复杂度可认为是 $$O(n)$$.

## 题解 2 - 排序 + hashmap

在题 [Two Strings Are Anagrams](http://algorithm.yuanbin.me/zh-hans/string/two_strings_are_anagrams.html) 中曾介绍过使用排序和 hashmap 两种方法判断变位词。这里我们将这两种方法同时引入！只不过此时的 hashmap 的 key 为字符串，value 为该字符串在 vector 中出现的次数。两次遍历字符串数组，第一次遍历求得排序后的字符串数量，第二次遍历将排序后相同的字符串取出放入最终结果中。

**leetcode 上此题的 signature 已经更新，需要将 anagrams 按组输出，稍微麻烦一点点。**

### Python lintcode

```
class Solution:
    # @param strs: A list of strings
    # @return: A list of strings
    # @return: A list of strings
    def anagrams(self, strs):
        strDict={}
        result=[]
        for string in strs:
            if  "".join(sorted(string)) not in strDict.keys():
                strDict["".join(sorted(string))] = 1
            else: 
                strDict["".join(sorted(string))] += 1
        for string in strs:
            if strDict["".join(sorted(string))] >1:
                result.append(string)
        return result 
```

### C++ - lintcode

```
class Solution {
public:
    /**
     * @param strs: A list of strings
     * @return: A list of strings
     */
    vector<string> anagrams(vector<string> &strs) {
        unordered_map<string, int> hash;

        for (int i = 0; i < strs.size(); i++) {
            string str = strs[i];
            sort(str.begin(), str.end());
            ++hash[str];
        }

        vector<string> result;
        for (int i = 0; i < strs.size(); i++) {
            string str = strs[i];
            sort(str.begin(), str.end());
            if (hash[str] > 1) {
                result.push_back(strs[i]);
            }
        }

        return result;
    }
}; 
```

### Java - leetcode

```
public class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        List<List<String>> result = new ArrayList<List<String>>();
        if (strs == null) return result;

        // one key to multiple value multiMap
        Map<String, ArrayList<String>> multiMap = new HashMap<String, ArrayList<String>>();
        for (String str : strs) {
            char[] strChar = str.toCharArray();
            Arrays.sort(strChar);
            String strSorted = String.valueOf(strChar);
            if (multiMap.containsKey(strSorted)) {
                ArrayList<String> aList = multiMap.get(strSorted);
                aList.add(str);
                multiMap.put(strSorted, aList);
            } else {
                ArrayList<String> aList = new ArrayList<String>();
                aList.add(str);
                multiMap.put(strSorted, aList);
            }
        }

        // add List group to result
        Set<String> keySet = multiMap.keySet();
        for (String key : keySet) {
            ArrayList<String> aList = multiMap.get(key);
            Collections.sort(aList);
            result.add(aList);
        }

        return result;
    }
} 
```

### 源码分析

建立 key 为字符串，value 为相应计数器的 hashmap, `unordered_map`为 C++ 11 中引入的哈希表数据结构[^unordered*map], 这种新的数据结构和之前的 map 有所区别，详见[^map-unordered*map]。

第一次遍历字符串数组获得排序后的字符串计数器信息，第二次遍历字符串数组将哈希表中计数器值大于 1 的字符串取出。

leetcode 中题目 signature 已经有所变化，这里使用一对多的 HashMap 较为合适，使用 ArrayList<String> 作为 value. Java 中对 String 排序可先将其转换为 char[], 排序后再转换为新的 String.

### 复杂度分析

遍历一次字符串数组，复杂度为 $$O(n)$$, 对单个字符串排序复杂度近似为 $$O(L \log L)$$. 两次遍历字符串数组，故总的时间复杂度近似为 $$O(nL \log L)$$. 使用了哈希表，空间复杂度为 $$O(K)$$, 其中 K 为排序后不同的字符串个数。

## Reference

*   [^unordered*map]: [unordered](http://www.cplusplus.com/reference/unordered_map/unordered_map/)*map - C++ Reference
*   [^map-unordered*map]: [c++ - Choosing between std::map and std::unordered](http://stackoverflow.com/questions/3902644/choosing-between-stdmap-and-stdunordered-map)*map - Stack Overflow
*   [Anagrams | 九章算法](http://www.jiuzhang.com/solutions/anagrams/)

## Longest Common Substring

## Question

*   lintcode: [(79) Longest Common Substring](http://www.lintcode.com/en/problem/longest-common-substring/)

```
Given two strings, find the longest common substring.
Return the length of it.

Example
Given A="ABCD", B="CBCE", return 2.
Note
The characters in substring should occur continuously in original string.
This is different with subsequence. 
```

## 题解

求最长公共子串，注意「子串」和「子序列」的区别！简单考虑可以使用两根指针索引分别指向两个字符串的当前遍历位置，若遇到相等的字符时则同时向后移动一位。

### C++

```
class Solution {
public:
    /**
     * @param A, B: Two string.
     * @return: the length of the longest common substring.
     */
    int longestCommonSubstring(string &A, string &B) {
        if (A.empty() || B.empty()) {
            return 0;
        }

        int lcs = 0, lcs_temp = 0;
        for (int i = 0; i < A.size(); ++i) {
            for (int j = 0; j < B.size(); ++j) {
                lcs_temp = 0;
                while ((i + lcs_temp < A.size()) &&\
                       (j + lcs_temp < B.size()) &&\
                       (A[i + lcs_temp] == B[j + lcs_temp]))
                {
                    ++lcs_temp;
                }

                // update lcs
                if (lcs_temp > lcs) {
                    lcs = lcs_temp;
                }
            }
        }

        return lcs;
    }
}; 
```

### 源码分析

1.  异常处理，空串时返回 0.
2.  分别使用`i`和`j`表示当前遍历的索引处。若当前字符相同时则共同往后移动一位。
3.  没有相同字符时比较此次遍历的`lcs_temp`和`lcs`大小，更新`lcs`.
4.  返回`lcs`.

注意在`while`循环中不可直接使用`++i`或者`++j`，因为有可能会漏解！

### 复杂度分析

双重 for 循环，最坏时间复杂度约为 $$O(mn \cdot lcs)$$.

## Reference

*   [Longest Common Substring | 九章算法](http://www.jiuzhang.com/solutions/longest-common-substring/)

## Rotate String

## Question

*   lintcode: [(8) Rotate String](http://www.lintcode.com/en/problem/rotate-string/)

### Problem Statement

Given a string and an offset, rotate string by offset. (rotate from left to right)

#### Example

Given `"abcdefg"`.

```
offset=0 => "abcdefg"
offset=1 => "gabcdef"
offset=2 => "fgabcde"
offset=3 => "efgabcd" 
```

#### Challenge

Rotate in-place with O(1) extra memory.

## 题解

常见的翻转法应用题，仔细观察规律可知翻转的分割点在从数组末尾数起的 offset 位置。先翻转前半部分，随后翻转后半部分，最后整体翻转。

### Python - immutable string

```
class Solution:
    """
    param A: A string
    param offset: Rotate string with offset.
    return: Rotated string.
    """
    def rotateString(self, A, offset):
        if A is None or len(A) == 0:
            return A

        offset %= len(A)
        before = A[:len(A) - offset]
        after = A[len(A) - offset:]
        # [::-1] means reverse in Python
        A = before[::-1] + after[::-1]
        A = A[::-1]

        return A 
```

### Python - mutable list

```
class Solution:
    # @param A: a list of char
    # @param offset: an integer
    # @return: nothing
    def rotateString(self, A, offset):
        if A is None or len(A) == 0:
            return

        offset %= len(A)
        self.reverse(A, 0, len(A)-offset-1)
        self.reverse(A, len(A)-offset, len(A)-1)
        self.reverse(A, 0, len(A)-1)

    def reverse(self, str_l, start, end):
        while start < end:
            str_l[start], str_l[end] = str_l[end], str_l[start]
            start += 1
            end -= 1 
```

### C++

```
class Solution {
public:
  /**
     * param A: A string
     * param offset: Rotate string with offset.
     * return: Rotated string.
     */
    string rotateString(string A, int offset) {
        if (A.empty() || A.size() == 0) {
            return A;
        }

        int len = A.size();
        offset %= len;
        reverse(A, 0, len - offset - 1);
        reverse(A, len - offset, len - 1);
        reverse(A, 0, len - 1);
        return A;
    }

private:
    void reverse(string &str, int start, int end) {
        while (start < end) {
            char temp = str[start];
            str[start] = str[end];
            str[end] = temp;
            start++;
            end--;
        }
    }
}; 
```

### Java

```
public class Solution {
    /*
     * param A: A string
     * param offset: Rotate string with offset.
     * return: Rotated string.
     */
    public char[] rotateString(char[] A, int offset) {
        if (A == null || A.length == 0) {
            return A;
        }

        int len = A.length;
        offset %= len;
        reverse(A, 0, len - offset - 1);
        reverse(A, len - offset, len - 1);
        reverse(A, 0, len - 1);

        return A;
    }

    private void reverse(char[] str, int start, int end) {
        while (start < end) {
            char temp = str[start];
            str[start] = str[end];
            str[end] = temp;
            start++;
            end--;
        }
    }
}; 
```

### 源码分析

1.  异常处理，A 为空或者其长度为 0
2.  `offset`可能超出 A 的大小，应模`len`后再用
3.  三步翻转法

Python 虽没有提供字符串的翻转，但用 slice 非常容易实现，非常 Pythonic!

**通常来说，字符串在各种编程语言中的实现一般为 immutable 的，对字符串做改变时往往会生成新的字符串，所以如果要达到空间复杂度为 O(1) 的效果，需要用可变数据结构来实现。**

### 复杂度分析

翻转一次时间复杂度近似为 $$O(n)$$, 原地交换的空间复杂度为 $$O(1)$$, 非原地交换的空间复杂度为 $$O(n)$$. 总共翻转 3 次，所以总的时间复杂度为 $$O(n)$$, 空间复杂度为 $$O(1)$$ 或者 $$O(n)$$.

## Reference

*   [Reverse a string in Python](http://stackoverflow.com/questions/931092/reverse-a-string-in-python)
*   [What’s New in Python 2.3 — Extended Slices](https://docs.python.org/2/whatsnew/2.3.html#extended-slices)