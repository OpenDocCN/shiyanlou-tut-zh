# 第 2 节 String - 字符串（二）

## Reverse Words in a String

## Question

*   lintcode: [(53) Reverse Words in a String](http://www.lintcode.com/en/problem/reverse-words-in-a-string/)

```
Given an input string, reverse the string word by word.

For example,
Given s = "the sky is blue",
return "blue is sky the".

Example
Clarification

- What constitutes a word?
A sequence of non-space characters constitutes a word.

- Could the input string contain leading or trailing spaces?
Yes. However, your reversed string should not contain leading or trailing spaces.

- How about multiple spaces between two words?
Reduce them to a single space in the reversed string. 
```

## 题解

1.  由第一个提问可知：题中只有空格字符和非空格字符之分，因此空格字符应为其一关键突破口。
2.  由第二个提问可知：输入的前导空格或者尾随空格在反转后应去掉。
3.  由第三个提问可知：两个单词间的多个空格字符应合并为一个或删除掉。

首先找到各个单词(以空格隔开)，根据题目要求，单词应从后往前依次放入。正向取出比较麻烦，因此可尝试采用逆向思维——先将输入字符串数组中的单词从后往前逆序取出，取出单词后即翻转并 append 至新字符串数组。在 append 之前加入空格即可。

### C++

```
class Solution {
public:
    /**
     * @param s : A string
     * @return : A string
     */
    string reverseWords(string s) {
        if (s.empty()) {
            return s;
        }

        string s_ret, s_temp;
        string::size_type ix = s.size();
        while (ix != 0) {
            s_temp.clear();
            while (!isspace(s[--ix])) {
                s_temp.push_back(s[ix]);
                if (ix == 0) {
                    break;
                }
            }
            if (!s_temp.empty()) {
                if (!s_ret.empty()) {
                    s_ret.push_back(' ');
                }
                std::reverse(s_temp.begin(), s_temp.end());
                s_ret.append(s_temp);
            }
        }

        return s_ret;
    }
}; 
```

### 源码分析

1.  首先处理异常，s 为空时直接返回空。
2.  索引初始值`ix = s.size()`，而不是`ix = s.size() - 1`，便于处理`ix == 0`时的特殊情况。
3.  使用额外空间`s_ret, s_temp`，空间复杂度为 O(n)，`s_temp`用于缓存临时的单词以 append 入`s_ret`。
4.  最后返回`s_ret`。

空间复杂度为 O(1)的解法？

1.  处理异常及特殊情况
2.  处理多个空格及首尾空格
3.  记住单词的头尾指针，翻转之
4.  整体翻转

## Valid Palindrome

*   tags: [palindrome]

## Question

*   leetcode: [Valid Palindrome | LeetCode OJ](https://leetcode.com/problems/valid-palindrome/)
*   lintcode: [(415) Valid Palindrome](http://www.lintcode.com/en/problem/valid-palindrome/)

```
Given a string, determine if it is a palindrome,
considering only alphanumeric characters and ignoring cases.

Example
"A man, a plan, a canal: Panama" is a palindrome.

"race a car" is not a palindrome.
Note
Have you consider that the string might be empty?
This is a good question to ask during an interview.
For the purpose of this problem,
we define empty string as valid palindrome.

Challenge
O(n) time without extra memory. 
```

## 题解

字符串的回文判断问题，由于字符串可随机访问，故逐个比较首尾字符是否相等最为便利，即常见的『两根指针』技法。此题忽略大小写，并只考虑字母和数字字符。链表的回文判断总结见 [Check if a singly linked list is palindrome](http://algorithm.yuanbin.me/zh-hans/linked_list/check_if_a_singly_linked_list_is_palindrome.html).

### Python

```
class Solution:
    # @param {string} s A string
    # @return {boolean} Whether the string is a valid palindrome
    def isPalindrome(self, s):
        if not s:
            return True

        l, r = 0, len(s) - 1

        while l < r:
            # find left alphanumeric character
            if not s[l].isalnum():
                l += 1
                continue
            # find right alphanumeric character
            if not s[r].isalnum():
                r -= 1
                continue
            # case insensitive compare
            if s[l].lower() == s[r].lower():
                l += 1
                r -= 1
            else:
                return False
        #
        return True 
```

### C++

```
class Solution {
public:
    /**
     * @param s A string
     * @return Whether the string is a valid palindrome
     */
    bool isPalindrome(string& s) {
        if (s.empty()) return true;

        int l = 0, r = s.size() - 1;
        while (l < r) {
            // find left alphanumeric character
            if (!isalnum(s[l])) {
                ++l;
                continue;
            }
            // find right alphanumeric character
            if (!isalnum(s[r])) {
                --r;
                continue;
            }
            // case insensitive compare
            if (tolower(s[l]) == tolower(s[r])) {
                ++l;
                --r;
            } else {
                return false;
            }
        }

        return true;
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param s A string
     * @return Whether the string is a valid palindrome
     */
    public boolean isPalindrome(String s) {
        if (s == null || s.isEmpty()) return true;

        int l = 0, r = s.length() - 1;
        while (l < r) {
            // find left alphanumeric character
            if (!Character.isLetterOrDigit(s.charAt(l))) {
                l++;
                continue;
            }
            // find right alphanumeric character
            if (!Character.isLetterOrDigit(s.charAt(r))) {
                r--;
                continue;
            }
            // case insensitive compare
            if (Character.toLowerCase(s.charAt(l)) == Character.toLowerCase(s.charAt(r))) {
                l++;
                r--;
            } else {
                return false;
            }
        }

        return true;
    }
} 
```

### 源码分析

两步走：

1.  找到最左边和最右边的第一个合法字符(字母或者字符)
2.  一致转换为小写进行比较

字符的判断尽量使用语言提供的 API

### 复杂度分析

两根指针遍历一次，时间复杂度 $$O(n)$$, 空间复杂度 $$O(1)$$.

## Longest Palindromic Substring

*   tags: [palindrome]

## Question

*   leetcode: [Longest Palindromic Substring | LeetCode OJ](https://leetcode.com/problems/longest-palindromic-substring/)
*   lintcode: [(200) Longest Palindromic Substring](http://www.lintcode.com/en/problem/longest-palindromic-substring/)

```
Given a string S, find the longest palindromic substring in S.
You may assume that the maximum length of S is 1000,
and there exists one unique longest palindromic substring.

Example
Given the string = "abcdzdcab", return "cdzdc".
Challenge
O(n2) time is acceptable. Can you do it in O(n) time. 
```

## 题解 1 - 穷竭搜索

最简单的方案，穷举所有可能的子串，判断子串是否为回文，使用一变量记录最大回文长度，若新的回文超过之前的最大回文长度则更新标记变量并记录当前回文的起止索引，最后返回最长回文子串。

### Python

```
class Solution:
    # @param {string} s input string
    # @return {string} the longest palindromic substring
    def longestPalindrome(self, s):
        if not s:
            return ""

        n = len(s)
        longest, left, right = 0, 0, 0
        for i in xrange(0, n):
            for j in xrange(i + 1, n + 1):
                substr = s[i:j]
                if self.isPalindrome(substr) and len(substr) > longest:
                    longest = len(substr)
                    left, right = i, j
        # construct longest substr
        result = s[left:right]
        return result

    def isPalindrome(self, s):
        if not s:
            return False
        # reverse compare
        return s == s[::-1] 
```

### C++

```
class Solution {
public:
    /**
     * @param s input string
     * @return the longest palindromic substring
     */
    string longestPalindrome(string& s) {
        string result;
        if (s.empty()) return s;

        int n = s.size();
        int longest = 0, left = 0, right = 0;
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j <= n; ++j) {
                string substr = s.substr(i, j - i);
                if (isPalindrome(substr) && substr.size() > longest) {
                    longest = j - i;
                    left = i;
                    right = j;
                }
            }
        }

        result = s.substr(left, right - left);
        return result;
    }

private:
    bool isPalindrome(string &s) {
        int n = s.size();
        for (int i = 0; i < n; ++i) {
            if (s[i] != s[n - i - 1]) return false;
        }
        return true;
    }
}; 
```

### Java

```
public class Solution {
    /**
     * @param s input string
     * @return the longest palindromic substring
     */
    public String longestPalindrome(String s) {
        String result = new String();
        if (s == null || s.isEmpty()) return result;

        int n = s.length();
        int longest = 0, left = 0, right = 0;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j <= n; j++) {
                String substr = s.substring(i, j);
                if (isPalindrome(substr) && substr.length() > longest) {
                    longest = substr.length();
                    left = i;
                    right = j;
                }
            }
        }

        result = s.substring(left, right);
        return result;
    }

    private boolean isPalindrome(String s) {
        if (s == null || s.isEmpty()) return false;

        int n = s.length();
        for (int i = 0; i < n; i++) {
            if (s.charAt(i) != s.charAt(n - i - 1)) return false;
        }

        return true;
    }
} 
```

### 源码分析

使用`left`, `right`作为子串的起止索引，用于最后构造返回结果，避免中间构造字符串以减少开销。

### 复杂度分析

穷举所有的子串，$$O(C_n²) = O(n²)$$, 每次判断字符串是否为回文，复杂度为 $$O(n)$$, 故总的时间复杂度为 $$O(n³)$$. 故大数据集下可能 TLE. 使用了`substr`作为临时子串，空间复杂度为 $$O(n)$$.

## 题解 2

### C++

```
string palindrome(string& s, int l, int r) {
    while (l>=0 && r<s.size() && s[l]==s[r]) l--, r++;
    return s.substr(l+1, r-l-1);
}

string longestPalindrome(string s) {
    if (s.empty()) return s;

    string res;
    for (int i=0; i<s.size(); i++) {
        string t;
        t = palindrome(s, i, i);
        if (t.size() > res.size()) res = t;

        t = palindrome(s, i, i+1);
        if (t.size() > res.size()) res = t;   
    }
    return res;
} 
```

### 源码分析

假定扫描的每个字母是回文的中间位置（需要处理奇偶两种情况），从该位置向两头搜索寻找最大回文长度

### 复杂度分析

时间复杂度降到 O(n²)了

## 题解 3

另外还有一个 O（n）的解法，具体参考下面的链接 http://articles.leetcode.com/2011/11/longest-palindromic-substring-part-ii.html

## Reference

*   [Longest Palindromic Substring Part I | LeetCode](http://articles.leetcode.com/2011/11/longest-palindromic-substring-part-i.html)
*   [Longest Palindromic Substring Part II | LeetCode](http://articles.leetcode.com/2011/11/longest-palindromic-substring-part-ii.html)

## Space Replacement

## Question

*   lintcode: [(212) Space Replacement](http://www.lintcode.com/en/problem/space-replacement/)

```
Write a method to replace all spaces in a string with %20\. 
The string is given in a characters array, you can assume it has enough space 
for replacement and you are given the true length of the string.

Example
Given "Mr John Smith", length = 13.

The string after replacement should be "Mr%20John%20Smith".

Note
If you are using Java or Python，please use characters array instead of string.

Challenge
Do it in-place. 
```

## 题解

根据题意，给定的输入数组长度足够长，将空格替换为`%20` 后也不会溢出。通常的思维为从前向后遍历，遇到空格即将`%20` 插入到新数组中，这种方法在生成新数组时很直观，但要求原地替换时就不方便了，这时可联想到插入排序的做法——从后往前遍历，空格处标记下就好了。由于不知道新数组的长度，故首先需要遍历一次原数组，字符串类题中常用方法。

需要注意的是这个题并未说明多个空格如何处理，如果多个连续空格也当做一个空格时稍有不同。

### C++

```
int replaceBlank(char string[], int length) {
    int n = 0;
    for (int i=0; i<length; i++)
        if (string[i] == ' ') n++;

    int new_len = length + n*2;
    for (int i=length-1; i>=0; i--) {
        if (string[i] != ' ') {
            string[--new_len] = string[i];
        } else {
            string[--new_len] = '0';
            string[--new_len] = '2';
            string[--new_len] = '%';
        }
    }
    return length + n*2;
} 
```

### Java

```
public class Solution {
    /**
     * @param string: An array of Char
     * @param length: The true length of the string
     * @return: The true length of new string
     */
    public int replaceBlank(char[] string, int length) {
        if (string == null) return 0;

        int space = 0;
        for (char c : string) {
            if (c == ' ') space++;
        }

        int r = length + 2 * space - 1;
        for (int i = length - 1; i >= 0; i--) {
            if (string[i] != ' ') {
                string[r] = string[i];
                r--;
            } else {
                string[r--] = '0';
                string[r--] = '2';
                string[r--] = '%';
            }
        }

        return length + 2 * space;
    }
} 
```

### 源码分析

先遍历一遍求得空格数，得到『新数组』的实际长度，从后往前遍历。

### 复杂度分析

遍历两次原数组，时间复杂度近似为 $$O(n)$$, 使用了`r` 作为标记，空间复杂度 $$O(1)$$.

## Wildcard Matching

## Question

*   leetcode: [Wildcard Matching | LeetCode OJ](https://leetcode.com/problems/wildcard-matching/)
*   lintcode: [(192) Wildcard Matching](http://www.lintcode.com/en/problem/wildcard-matching/)

```
Implement wildcard pattern matching with support for '?' and '*'.

'?' Matches any single character.
'*' Matches any sequence of characters (including the empty sequence).
The matching should cover the entire input string (not partial).

Example
isMatch("aa","a") → false
isMatch("aa","aa") → true
isMatch("aaa","aa") → false
isMatch("aa", "*") → true
isMatch("aa", "a*") → true
isMatch("ab", "?*") → true
isMatch("aab", "c*a*b") → false 
```

## 题解 1 - DFS

字符串的通配实现。'`?`'表示匹配单一字符，'`*`'可匹配任意多字符串(包含零个)。要匹配的字符串设为`s`, 模式匹配用的字符串设为`p`，那么如果是普通字符，两个字符串索引向前推进一位即可，如果`p`中的字符是`?`也好办，同上处理，向前推进一位。所以现在的关键就在于如何处理'`*`', 因为`*`可匹配 0, 1, 2...个字符，所以遇到`*`时，`s`应该尽可能的向前推进，注意到`p`中`*`后面可能跟有其他普通字符，故`s`向前推进多少位直接与`p`中`*`后面的字符相关。同时此时两个字符串的索引处即成为回溯点，如果后面的字符串匹配不成功，则`s`中的索引向前推进，向前推进的字符串即表示和`p`中`*`匹配的字符个数。

### Java

```
public class Solution {
    /**
     * @param s: A string
     * @param p: A string includes "?" and "*"
     * @return: A boolean
     */
    public boolean isMatch(String s, String p) {
        if (s == null || p == null) return false;
        if (s.length() == 0|| p.length() == 0) return false;

        return helper(s, 0, p, 0);
    }

    private boolean helper(String s, int si, String p, int pj) {
        // index out of range check
        if (si == s.length() || pj == p.length()) {
            if (si == s.length() && pj == p.length()) {
                return true;
            } else {
                return false;
            }
        }

        if (p.charAt(pj) == '*') {
            // remove coninuous *
            while (p.charAt(pj) == '*') {
                pj++;
                // index out of range check
                if (pj == p.length()) return true;
            }

            // compare remaining part of p after * with s
            while (si < s.length() && !helper(s, si, p, pj)) {
                si++;
            }
            // substring of p equals to s
            return si != s.length();
        } else if (s.charAt(si) == p.charAt(pj) || p.charAt(pj) == '?') {
            return helper(s, si + 1, p, pj + 1);
        } else {
            return false;
        }
    }
} 
```

### 源码分析

其中对`*`的处理和递归回溯是这段代码的精华。

### 复杂度分析

最坏情况下需要不断回溯，时间复杂度 $$O(n!) \times O(m!)$$, 空间复杂度 $$O(1)$$(不含栈空间)。

## 题解 2

### C++

```
bool isMatch(string s, string p) {
    int star = 0, ss = 0, i = 0, j = 0;
    while (s[i]) {
        if (p[j] == '?' || p[j] == s[i]) {j++; i++; continue;}
        if (p[j] == '*') {star = ++j; ss = i; continue;}
        if (star) {j = star; i = ++ss; continue;}
        return false;
    }
    while (p[j] == '*') j++;
    return !p[j];
} 
```

## Reference

*   Soulmachine 的 leetcode 题解
*   [题解 2 的出处](http://yucoding.blogspot.com/2013/02/leetcode-question-123-wildcard-matching.html)

## Length of Last Word

## Question

*   leetcode: [Length of Last Word | LeetCode OJ](https://leetcode.com/problems/length-of-last-word/)
*   lintcode: [(422) Length of Last Word](http://www.lintcode.com/en/problem/length-of-last-word/)

```
Given a string s consists of upper/lower-case alphabets and empty space characters ' ',
return the length of last word in the string.

If the last word does not exist, return 0.

Have you met this question in a real interview? Yes
Example
Given s = "Hello World", return 5.

Note
A word is defined as a character sequence consists of non-space characters only. 
```

## 题解 1

关键点在于确定最后一个字符串之前的空格，此外还需要考虑末尾空格这一特殊情况，故首先除掉右边的空白字符比较好。

### Java

```
public class Solution {
    /**
     * @param s A string
     * @return the length of last word
     */
    public int lengthOfLastWord(String s) {
        if (s == null | s.isEmpty()) return 0;

        // trim right space
        int begin = 0, end = s.length();
        while (end > 0 && s.charAt(end - 1) == ' ') {
            end--;
        }
        // find the last space
        for (int i = 0; i < end; i++) {
            if (s.charAt(i) == ' ') {
                begin = i + 1;
            }
        }

        return end - begin;
    }
} 
```

### 源码分析

两根指针。

### 复杂度分析

遍历一次，时间复杂度 $$O(n)$$.

## 题解 2

直接从后向前扫描

### C++

```
 int lengthOfLastWord(string s) {
        if (s.size() == 0) return 0;

        int count = 0;
        for (int i=s.size()-1; i>=0; i--)
            if (s[i] == ' ') {
                if (count) break;
            } else count++;

        return count;
    } 
```

## Count and Say

## Question

*   leetcode: [Count and Say | LeetCode OJ](https://leetcode.com/problems/count-and-say/)
*   lintcode: [(420) Count and Say](http://www.lintcode.com/en/problem/count-and-say/)

```
The count-and-say sequence is the sequence of integers beginning as follows:

1, 11, 21, 1211, 111221, ...

1 is read off as "one 1" or 11.

11 is read off as "two 1s" or 21.

21 is read off as "one 2, then one 1" or 1211.

Given an integer n, generate the nth sequence.

Example
Given n = 5, return "111221".

Note
The sequence of integers will be represented as a string. 
```

## 题解

题目大意是找第 n 个数(字符串表示)，规则则是对于连续字符串，表示为重复次数+数本身。

### C++

```
string countAndSay(int n) {
    if (n == 0) return "";
    string res = "1";
    while (--n) {
        string cur = "";
        for (int i = 0; i < res.size(); i++) {
            int count = 1;
             while ((i + 1 < res.size()) && (res[i] == res[i + 1])){
                count++;   
                i++;
            }
            cur += to_string(count) + res[i];
        }
        res = cur;
    }
    return res;
} 
```

### Java

```
public class Solution {
    /**
     * @param n the nth
     * @return the nth sequence
     */
    public String countAndSay(int n) {
        if (n <= 0) return null;

        String s = "1";
        for (int i = 1; i < n; i++) {
            int count = 1;
            StringBuilder sb = new StringBuilder();
            int sLen = s.length();
            for (int j = 0; j < sLen; j++) {
                if (j < sLen - 1 && s.charAt(j) == s.charAt(j + 1)) {
                    count++;
                } else {
                    sb.append(count + "" + s.charAt(j));
                    // reset
                    count = 1;
                }
            }
            s = sb.toString();
        }

        return s;
    }
} 
```

### 源码分析

字符串是动态生成的，故使用 StringBuilder 更为合适。注意 s 初始化为"1", 第一重 for 循环中注意循环的次数为 n-1.

### 复杂度分析

略

### 题解 2 - 递归

### C++

```
class Solution {
public:
    string countAndSay(int n) {
        if (n == 1) return "1";             // base case
        string res, tmp = countAndSay(n - 1);  // recursion
        char c = tmp[0];
        int count = 1;
        for (int i = 1; i < tmp.size(); i++)
            if (tmp[i] == c)
                count++;
            else {
                res += to_string(count);
                res.push_back(c);
                c = tmp[i];
                count = 1;
            }
        res += to_string(count);
        res.push_back(c);
        return res;
    }
}; 
```

## Reference

*   [[leetcode]Count and Say - 喵星人与汪星人](http://huntfor.iteye.com/blog/2059877)