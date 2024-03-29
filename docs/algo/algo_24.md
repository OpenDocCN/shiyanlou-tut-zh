# 第 24 节 Exhaustive Search - 穷竭搜索（三）

## Palindrome Partitioning

*   tags: [palindrome]

## Question

*   leetcode: [Palindrome Partitioning | LeetCode OJ](https://leetcode.com/problems/palindrome-partitioning/)
*   lintcode: [(136) Palindrome Partitioning](http://www.lintcode.com/en/problem/palindrome-partitioning/)

```
Given a string s, partition s such that every substring of the partition is a palindrome.

Return all possible palindrome partitioning of s.

For example, given s = "aab",
Return

  [
    ["aa","b"],
    ["a","a","b"]
  ] 
```

## 题解 1 - DFS

罗列所有可能，典型的 DFS. 此题要求所有可能的回文子串，即需要找出所有可能的分割，使得分割后的子串都为回文。凭借高中的排列组合知识可知这可以用『隔板法』来解决，具体就是在字符串的每个间隙为一个隔板，对于长度为 n 的字符串，共有 n-1 个隔板可用，每个隔板位置可以选择放或者不放，总共有 $$O(2^{n-1})$$ 种可能。由于需要满足『回文』条件，故实际上需要穷举的状态数小于 $$O(2^{n-1})$$.

回溯法看似不难，但是要活学活用起来还是不容易的，核心抓住两点：**深搜的递归建立和剪枝函数的处理。**

根据『隔板法』的思想，我们首先从第一个隔板开始挨个往后取，若取到的子串不是回文则立即取下一个隔板，直到取到最后一个隔板。若取到的子串是回文，则将当前子串加入临时列表中，接着从当前隔板处字符开始递归调用回溯函数，直至取到最后一个隔板，最后将临时列表中的子串加入到最终返回结果中。接下来则将临时列表中的结果一一移除，这个过程和 subsets 模板很像，代码比这个文字描述更为清晰。

### Python

```
class Solution:
    # @param s, a string
    # @return a list of lists of string
    def partition(self, s):
        result = []
        if not s:
            return result

        palindromes = []
        self.dfs(s, 0, palindromes, result)
        return result

    def dfs(self, s, pos, palindromes, ret):
        if pos == len(s):
            ret.append([] + palindromes)
            return

        for i in xrange(pos + 1, len(s) + 1):
            if not self.isPalindrome(s[pos:i]):
                continue

            palindromes.append(s[pos:i])
            self.dfs(s, i, palindromes, ret)
            palindromes.pop()

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
     * @param s: A string
     * @return: A list of lists of string
     */
    vector<vector<string>> partition(string s) {
        vector<vector<string> > result;
        if (s.empty()) return result;

        vector<string> palindromes;
        dfs(s, 0, palindromes, result);

        return result;
    }

private:
    void dfs(string s, int pos, vector<string> &palindromes, 
             vector<vector<string> > &ret) {

        if (pos == s.size()) {
            ret.push_back(palindromes);
            return;
        }

        for (int i = pos + 1; i <= s.size(); ++i) {
            string substr = s.substr(pos, i - pos);
            if (!isPalindrome(substr)) {
                continue;
            }

            palindromes.push_back(substr);
            dfs(s, i, palindromes, ret);
            palindromes.pop_back();
        }
    }

    bool isPalindrome(string s) {
        if (s.empty()) return false;

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
     * @param s: A string
     * @return: A list of lists of string
     */
    public List<List<String>> partition(String s) {
        List<List<String>> result = new ArrayList<List<String>>();
        if (s == null || s.isEmpty()) return result;

        List<String> palindromes = new ArrayList<String>();
        dfs(s, 0, palindromes, result);

        return result;
    }

    private void dfs(String s, int pos, List<String> palindromes, 
                     List<List<String>> ret) {

        if (pos == s.length()) {
            ret.add(new ArrayList<String>(palindromes));
            return;
        }

        for (int i = pos + 1; i <= s.length(); i++) {
            String substr = s.substring(pos, i);
            if (!isPalindrome(substr)) {
                continue;
            }

            palindromes.add(substr);
            dfs(s, i, palindromes, ret);
            palindromes.remove(palindromes.size() - 1);
        }
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

回文的判断采用了简化的版本，没有考虑空格等非字母数字字符要求。Java 中 ArrayList 和 List 的实例化需要注意下。Python 中 result 的初始化为[], 不需要初始化为 [[]] 画蛇添足。C++ 中的`.substr(pos, n)` 含义为从索引为 pos 的位置往后取 n 个(含) 字符，注意与 Java 中区别开来。

### 复杂度分析

DFS，状态数最多 $$O(2^{n-1})$$, 故时间复杂度为 $$O(2^n)$$, 使用了临时列表，空间复杂度为 $$O(n)$$.

## Reference

*   [Palindrome Partitioning 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/palindrome-partitioning/)
*   soulmachine 的 Palindrome Partitioning

## Combinations

## Question

*   leetcode: [Combinations | LeetCode OJ](https://leetcode.com/problems/combinations/)
*   lintcode: [(152) Combinations](http://www.lintcode.com/en/problem/combinations/)

### Problem Statement

Given two integers n and k, return all possible combinations of k numbers out of 1 ... n.

#### Example

For example, If n = 4 and k = 2, a solution is: `[[2,4],[3,4],[2,3],[1,2],[1,3],[1,4]]`

## 题解

套用 [Permutations](http://algorithm.yuanbin.me/zh-hans/exhaustive_search/permutations.html) 模板。

### Java

```
public class Solution {
    public List<List<Integer>> combine(int n, int k) {
        assert(n >= 1 && n >= k && k >= 1);

        List<List<Integer>> result = new ArrayList<List<Integer>>();
        List<Integer> list = new ArrayList<Integer>();
        dfs(n, k, 1, list, result);

        return result;
    }

    private void dfs(int n, int k, int pos, List<Integer> list,
                     List<List<Integer>> result) {

        if (list.size() == k) {
            result.add(new ArrayList<Integer>(list));
            return;
        }
        for (int i = pos; i <= n; i++) {
            list.add(i);
            dfs(n, k, i + 1, list, result);
            list.remove(list.size() - 1);
        }
    }
} 
```

### 源码分析

注意递归`helper(n, k, i + 1, list, result);`中的`i + 1`，不是`pos + 1`。

### 复杂度分析

状态数 $$C_n²$$, 每组解有两个元素，故时间复杂度应为 $$O(n²)$$. list 只保留最多两个元素，空间复杂度 $$O(1)$$.

## Combination Sum

## Question

*   leetcode: [Combination Sum | LeetCode OJ](https://leetcode.com/problems/combination-sum/)
*   lintcode: [(135) Combination Sum](http://www.lintcode.com/en/problem/combination-sum/)

```
Given a set of candidate numbers (C) and a target number (T),
find all unique combinations in C where the candidate numbers sums to T.
The same repeated number may be chosen from C unlimited number of times.

For example, given candidate set 2,3,6,7 and target 7,
A solution set is:
[7]
[2, 2, 3]

Have you met this question in a real interview? Yes
Example
given candidate set 2,3,6,7 and target 7,
A solution set is:
[7]
[2, 2, 3]

Note
- All numbers (including target) will be positive integers.
- Elements in a combination (a1, a2, … , ak) must be in non-descending order.
(ie, a1 ≤ a2 ≤ … ≤ ak).
- The solution set must not contain duplicate combinations. 
```

## 题解

和 [Permutations](http://algorithm.yuanbin.me/zh-hans/exhaustive_search/permutations.html) 十分类似，区别在于剪枝函数不同。这里允许一个元素被多次使用，故递归时传入的索引值不自增，而是由 for 循环改变。

### Java

```
public class Solution {
    /**
     * @param candidates: A list of integers
     * @param target:An integer
     * @return: A list of lists of integers
     */
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        List<Integer> list = new ArrayList<Integer>();
        if (candidates == null) return result;

        Arrays.sort(candidates);
        helper(candidates, 0, target, list, result);

        return result;
    }

    private void helper(int[] candidates, int pos, int gap,
                        List<Integer> list, List<List<Integer>> result) {

        if (gap == 0) {
            // add new object for result
            result.add(new ArrayList<Integer>(list));
            return;
        }

        for (int i = pos; i < candidates.length; i++) {
            // cut invalid candidate
            if (gap < candidates[i]) {
                return;
            }
            list.add(candidates[i]);
            helper(candidates, i, gap - candidates[i], list, result);
            list.remove(list.size() - 1);
        }
    }
} 
```

### 源码分析

对数组首先进行排序是必须的，递归函数中本应该传入 target 作为入口参数，这里借用了 Soulmachine 的实现，使用 gap 更容易理解。注意在将临时 list 添加至 result 中时需要 new 一个新的对象。

### 复杂度分析

按状态数进行分析，时间复杂度 $$O(n!)$$, 使用了 list 保存中间结果，空间复杂度 $$O(n)$$.

## Reference

*   Soulmachine 的 leetcode 题解

## Combination Sum II

## Question

*   leetcode: [Combination Sum II | LeetCode OJ](https://leetcode.com/problems/combination-sum-ii/)
*   lintcode: [(153) Combination Sum II](http://www.lintcode.com/en/problem/combination-sum-ii/)

```
Given a collection of candidate numbers (C) and a target number (T),
find all unique combinations in C where the candidate numbers sums to T.
Each number in C may only be used once in the combination.

Have you met this question in a real interview? Yes
Example
For example, given candidate set 10,1,6,7,2,1,5 and target 8,

A solution set is:

[1,7]

[1,2,5]

[2,6]

[1,1,6]

Note
All numbers (including target) will be positive integers.
Elements in a combination (a1, a2, … , ak) must be in non-descending order.
(ie, a1 ≤ a2 ≤ … ≤ ak).
The solution set must not contain duplicate combinations. 
```

## 题解

和 [Unique Subsets](http://algorithm.yuanbin.me/zh-hans/exhaustive_search/unique_subsets.html) 非常类似。在 [Combination Sum](http://algorithm.yuanbin.me/zh-hans/exhaustive_search/combination_sum.html) 的基础上改改就好了。

### Java

```
public class Solution {
    /**
     * @param num: Given the candidate numbers
     * @param target: Given the target number
     * @return: All the combinations that sum to target
     */
    public List<List<Integer>> combinationSum2(int[] num, int target) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        List<Integer> list = new ArrayList<Integer>();
        if (num == null) return result;

        Arrays.sort(num);
        helper(num, 0, target, list, result);

        return result;
    }

    private void helper(int[] nums, int pos, int gap,
                        List<Integer> list, List<List<Integer>> result) {

        if (gap == 0) {
            result.add(new ArrayList<Integer>(list));
            return;
        }

        for (int i = pos; i < nums.length; i++) {
            // ensure only the first same num is chosen, remove duplicate list
            if (i != pos && nums[i] == nums[i - 1]) {
                continue;
            }
            // cut invalid num
            if (gap < nums[i]) {
                return;
            }
            list.add(nums[i]);
            // i + 1 ==> only be used once
            helper(nums, i + 1, gap - nums[i], list, result);
            list.remove(list.size() - 1);
        }
    }
} 
```

### 源码分析

这里去重的方法继承了 Unique Subsets 中的做法，当然也可以新建一变量 `prev`，由于这里每个数最多只能使用一次，故递归时索引变量传`i + 1`.

### 复杂度分析

时间复杂度 $$O(n)$$, 空间复杂度 $$O(n)$$.

## Minimum Depth of Binary Tree

## Question

*   leetcode: [Minimum Depth of Binary Tree | LeetCode OJ](https://leetcode.com/problems/minimum-depth-of-binary-tree/)
*   lintcode: [(155) Minimum Depth of Binary Tree](http://www.lintcode.com/en/problem/minimum-depth-of-binary-tree/)

```
Given a binary tree, find its minimum depth.

The minimum depth is the number of nodes along the shortest path
from the root node down to the nearest leaf node.

Example
Given a binary tree as follow:

        1

     /     \

   2       3

          /    \

        4      5
The minimum depth is 2 
```

## 题解

注意审题，题中的最小深度指的是从根节点到**最近的叶子节点（因为题中的最小深度是 the number of nodes，故该叶子节点不能是空节点）**，所以需要单独处理叶子节点为空的情况。此题使用 DFS 递归实现比较简单。

### Java

```
/**
 * Definition of TreeNode:
 * public class TreeNode {
 *     public int val;
 *     public TreeNode left, right;
 *     public TreeNode(int val) {
 *         this.val = val;
 *         this.left = this.right = null;
 *     }
 * }
 */
public class Solution {
    /**
     * @param root: The root of binary tree.
     * @return: An integer.
     */
    public int minDepth(TreeNode root) {
        if (root == null) return 0;

        int leftDepth = minDepth(root.left);
        int rightDepth = minDepth(root.right);

        // current node is not leaf node
        if (root.left == null) {
            return 1 + rightDepth;
        } else if (root.right == null) {
            return 1 + leftDepth;
        }

        return 1 + Math.min(leftDepth, rightDepth);
    }
} 
```

### 源码分析

建立好递归模型即可，左右子节点为空时需要单独处理下。

### 复杂度分析

每个节点遍历一次，时间复杂度 $$O(n)$$. 不计栈空间的话空间复杂度 $$O(1)$$.

## Word Search

## Question

*   leetcode: [Word Search | LeetCode OJ](https://leetcode.com/problems/word-search/)
*   lintcode: [(123) Word Search](http://www.lintcode.com/en/problem/word-search/)

### Problem Statement

Given a 2D board and a word, find if the word exists in the grid. The word can be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once.

#### Example

Given board =

```
[
  "ABCE",
  "SFCS",
  "ADEE"
] 
```

*   word = `"ABCCED"`, -> returns `true`,
*   word = `"SEE"`, -> returns `true`,
*   word = `"ABCB"`, -> returns `false`.

## 题解

典型的 DFS 实现，这里有上下左右四个方向，往四个方向递归之前需要记录坐标处是否被访问过，并且在不满足条件时要重置该标记变量。该题的一大难点是如何处理起始点和字符串的第一个字符不等的情况，我最开始尝试在一个 DFS 中解决，发现很难 bug-free, 而且程序逻辑支离破碎。后来看了下其他题解发现简单粗暴的方法就是双重循环嵌套 DFS...

### Java

```
public class Solution {
    /**
     * @param board: A list of lists of character
     * @param word: A string
     * @return: A boolean
     */
    public boolean exist(char[][] board, String word) {
        if (board == null || board.length == 0 || board[0].length == 0) return false;
        if (word == null || word.length() == 0) return false;

        boolean[][] visited = new boolean[board.length][board[0].length];
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if (dfs(board, word, visited, i, j, 0)) {
                    return true;
                }
            }
        }

        return false;
    }

    public boolean dfs(char[][] board, String word,
                               boolean[][] visited,
                               int row, int col,
                               int wi) {
        // out of index
        if (row < 0 || row > board.length - 1 ||
            col < 0 || col > board[0].length - 1) {
            return false;
        }

        if (!visited[row][col] && board[row][col] == word.charAt(wi)) {
            // return instantly
            if (wi == word.length() - 1) return true;
            // traverse unvisited row and col
            visited[row][col] = true;
            boolean down = dfs(board, word, visited, row + 1, col, wi + 1);
            boolean right = dfs(board, word, visited, row, col + 1, wi + 1);
            boolean up = dfs(board, word, visited, row - 1, col, wi + 1);
            boolean left = dfs(board, word, visited, row, col - 1, wi + 1);
            // reset with false if none of above is true
            visited[row][col] = up || down || left || right;
            return up || down || left || right;
        }

        return false;
    }
} 
```

### 源码分析

注意处理好边界退出条件及`visited`在上下左右四个方向均为`false`时需要重置。判断字符串字符和`board`中字符是否相等前需要去掉已访问坐标。如果不引入`visited`二维矩阵，也可以使用特殊字符替换的方法，这样的话空间复杂度就大大降低了，细节见下面参考链接。

### 复杂度分析

DFS 最坏情况下遍历所有坐标点，二重 for 循环最坏情况下也全部执行完，故时间复杂度最差情况下为 $$O(m²n²)$$, 使用了`visited`矩阵，空间复杂度为 $$O(mn)$$, 当然这个可以优化到 $$O(1)$$.(原地更改原 board 数组字符内容)。

## Reference

*   [LeetCode – Word Search (Java)](http://www.programcreek.com/2014/06/leetcode-word-search-java/)