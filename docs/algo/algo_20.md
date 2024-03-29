# 第 20 节 Binary Tree - 二叉树（四）

## Construct Binary Tree from Preorder and Inorder Traversal

## Question

*   leetcode: [Construct Binary Tree from Preorder and Inorder Traversal | LeetCode OJ](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
*   lintcode: [(73) Construct Binary Tree from Preorder and Inorder Traversal](http://www.lintcode.com/en/problem/construct-binary-tree-from-preorder-and-inorder-traversal/)

```
Given preorder and inorder traversal of a tree, construct the binary tree.

Example
Given in-order [1,2,3] and pre-order [2,1,3], return a tree:
  2
 / \
1   3
Note
You may assume that duplicates do not exist in the tree. 
```

## 题解

二叉树的重建，典型题。核心有两点：

1.  preorder 先序遍历的第一个节点即为根节点。
2.  确定 inorder 数组中的根节点后其左子树和右子树也是 preorder 的左子树和右子树。

其中第二点是隐含条件，数组中没有重复元素，故可以根据先序遍历中第一个元素（根节点）得到根节点的值，然后在 inorder 中序遍历的数组中搜索得到根节点的索引值，即为左子树，右边为右子树。根据中序遍历中左子树的索引确定先序遍历数组中左子树的起止索引。递归直至处理完所有数组元素。

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
     *@param preorder : A list of integers that preorder traversal of a tree
     *@param inorder : A list of integers that inorder traversal of a tree
     *@return : Root of a tree
     */
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if (preorder == null || inorder == null) return null;
        if (preorder.length == 0 || inorder.length == 0) return null;
        if (preorder.length != inorder.length) return null;

        TreeNode root = helper(preorder, 0, preorder.length - 1,
                               inorder, 0, inorder.length - 1);
        return root;
    }

    private TreeNode helper(int[] preorder, int prestart, int preend,
                            int[] inorder, int instart, int inend) {
        // corner cases
        if (prestart > preend || instart > inend) return null;
        // build root TreeNode
        int root_val = preorder[prestart];
        TreeNode root = new TreeNode(root_val);
        // find index of root_val in inorder[]
        int index = findIndex(inorder, instart, inend, root_val);
        // build left subtree
        root.left = helper(preorder, prestart + 1, prestart + index - instart,
               inorder, instart, index - 1);
        // build right subtree
        root.right = helper(preorder, prestart + index - instart + 1, preend,
               inorder, index + 1, inend);
        return root;
    }

    private int findIndex(int[] nums, int start, int end, int target) {
        for (int i = start; i <= end; i++) {
            if (nums[i] == target) return i;
        }
        return -1;
    }
} 
```

### 源码分析

由于需要知道左右子树在数组中的索引，故需要引入辅助方法。找根节点这个大家都能很容易地想到，但是最关键的一步——找出左右子树的起止索引，这一点就不那么直接了，老实说想了很久忽略了这个突破点。

### 复杂度分析

`findIndex` 时间复杂度近似 $$O(n)$$, `helper` 递归调用，每次调用都需要找中序遍历数组中的根节点，故总的时间复杂度为 $$O(n²)$$. 原地生成最终二叉树，空间复杂度为 $$O(1)$$.

## Reference

*   [Construct Binary Tree from Preorder and Inorder Traversal 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/construct-binary-tree-from-preorder-and-inorder-traversal/)

## Construct Binary Tree from Inorder and Postorder Traversal

## Question

*   lintcode: [(72) Construct Binary Tree from Inorder and Postorder Traversal](http://www.lintcode.com/en/problem/construct-binary-tree-from-inorder-and-postorder-traversal/)

```
Given inorder and postorder traversal of a tree, construct the binary tree.

Example
Given inorder [1,2,3] and postorder [1,3,2], return a tree:
  2
   / \
   1   3
   Note
   You may assume that duplicates do not exist in the tree. 
```

## 题解

和题 [Construct Binary Tree from Preorder and Inorder Traversal](http://algorithm.yuanbin.me/zh-hans/binary_tree/construct_binary_tree_from_preorder_and_inorder_traversal.html) 几乎一致，关键在于找到中序遍历中的根节点和左右子树，递归解决。

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
     *@param inorder : A list of integers that inorder traversal of a tree
     *@param postorder : A list of integers that postorder traversal of a tree
     *@return : Root of a tree
     */
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        if (inorder == null || postorder == null) return null;
        if (inorder.length == 0 || postorder.length == 0) return null;
        if (inorder.length != postorder.length) return null;

        TreeNode root = helper(inorder, 0, inorder.length - 1,
               postorder, 0, postorder.length - 1);
        return root;
    }

    private TreeNode helper(int[] inorder, int instart, int inend,
                            int[] postorder, int poststart, int postend) {
        // corner cases
        if (instart > inend || poststart > postend) return null;

        // build root TreeNode
        int root_val = postorder[postend];
        TreeNode root = new TreeNode(root_val);
        // find index of root_val in inorder[]
        int index = findIndex(inorder, instart, inend, root_val);
        // build left subtree
        root.left = helper(inorder, instart, index - 1,
                           postorder, poststart, poststart + index - instart - 1);
        // build right subtree
        root.right = helper(inorder, index + 1, inend,
                           postorder, poststart + index - instart, postend - 1);
        return root;
    }

    private int findIndex(int[] nums, int start, int end, int target) {
        for (int i = start; i <= end; i++) {
            if (nums[i] == target) return i;
        }
        return -1;
    }
} 
```

### 源码分析

找根节点的方法作为私有方法，辅助函数需要注意索引范围。

### 复杂度分析

找根节点近似 $$O(n)$$, 递归遍历整个数组，嵌套找根节点的方法，故总的时间复杂度为 $$O(n²)$$.

## Subtree

## Question

*   lintcode: [(245) Subtree](http://www.lintcode.com/en/problem/subtree/#)

```
You have two every large binary trees: T1,
with millions of nodes, and T2, with hundreds of nodes.
Create an algorithm to decide if T2 is a subtree of T1.

Example
T2 is a subtree of T1 in the following case:
       1                3
      / \              /
T1 = 2   3      T2 =  4
        /
       4
T2 isn't a subtree of T1 in the following case:
       1               3
      / \               \
T1 = 2   3       T2 =    4
        /
       4
Note
A tree T2 is a subtree of T1 if there exists a node n in T1 such that
the subtree of n is identical to T2.
That is, if you cut off the tree at node n,
the two trees would be identical. 
```

## 题解

判断 T2 是否是 T1 的子树，首先应该在 T1 中找到 T2 的根节点，找到根节点后两棵子树必须完全相同。所以整个思路分为两步走：找根节点，判断两棵树是否全等。咋看起来极其简单，但实际实现中还是比较精妙的，尤其是递归的先后顺序及条件与条件或的处理。

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
     * @param T1, T2: The roots of binary tree.
     * @return: True if T2 is a subtree of T1, or false.
     */
    public boolean isSubtree(TreeNode T1, TreeNode T2) {
        if (T2 == null) return true;
        if (T1 == null) return false;
        return identical(T1, T2) || isSubtree(T1.left, T2) || isSubtree(T1.right, T2);
    }

    private boolean identical(TreeNode T1, TreeNode T2) {
        if (T1 == null && T2 == null) return true;
        if (T1 == null || T2 == null) return false;
        if (T1.val != T2.val) return false;
        return identical(T1.left, T2.left) && identical(T1.right, T2.right);
    }
} 
```

### 源码分析

这道题的异常处理相对 trick 一点，需要理解 null 对子树的含义。另外需要先调用`identical`再递归调用`isSubtree`判断左右子树的情况。方法`identical`中调用`.val`前需要判断是否为 null, 而后递归调用判断左右子树是否 identical。

### 复杂度分析

identical 的调用，时间复杂度近似 $$O(n)$$, 查根节点的时间复杂度随机，平均为 $$O(m)$$, 故总的时间复杂度可近似为 $$O(mn)$$.

## Reference

*   [LintCode: Subtree](http://cherylintcode.blogspot.com/2015/06/subtree.html)

## Binary Tree Zigzag Level Order Traversal

## Question

*   leetcode: [Binary Tree Zigzag Level Order Traversal | LeetCode OJ](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)
*   lintcode: [(71) Binary Tree Zigzag Level Order Traversal](http://www.lintcode.com/en/problem/binary-tree-zigzag-level-order-traversal/)

```
Given a binary tree, return the zigzag level order traversal of its nodes' values.
(ie, from left to right, then right to left for the next level and alternate between).

Example
Given binary tree {3,9,20,#,#,15,7},

    3
   / \
  9  20
    /  \
   15   7

return its zigzag level order traversal as:

[
  [3],
  [20,9],
  [15,7]
] 
```

## 题解 1 - 队列

二叉树的广度优先遍历使用队列非常容易实现，这道题要求的是蛇形遍历，我们可以发现奇数行的遍历仍然可以按照广度优先遍历的方式实现，而对于偶数行，只要翻转一下就好了。

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
     * @return: A list of lists of integer include
     *          the zigzag level order traversal of its nodes' values
     */
    public ArrayList<ArrayList<Integer>> zigzagLevelOrder(TreeNode root) {
        ArrayList<ArrayList<Integer>> result = new ArrayList<ArrayList<Integer>>();
        if (root == null) return result;

        boolean odd = true;
        Queue<TreeNode> q = new LinkedList<TreeNode>();
        q.offer(root);
        while (!q.isEmpty()) {
            // level traversal
            int qLen = q.size();
            ArrayList<Integer> level = new ArrayList<Integer>();
            for (int i = 0; i < qLen; i++) {
                TreeNode node = q.poll();
                level.add(node.val);
                if (node.left != null) q.offer(node.left);
                if (node.right != null) q.offer(node.right);
            }
            // add level order reverse for even
            if (odd) {
                result.add(level);
            } else {
                Collections.reverse(level);
                result.add(level);
            }
            // flip odd and even
            odd = !odd;
        }

        return result;
    }
} 
```

### 源码分析

区分奇数偶数行使用额外变量。

### 复杂度分析

需要 reverse 的节点数目近似为 n/2, 故时间复杂度 $$O(n)$$. 最下层节点数目最多 n/2, 故 reverse 操作的空间复杂度可近似为 $$O(n/2)$$.

总的时间复杂度为 $$O(n)$$, 空间复杂度也为 $$O(n)$$.

## Reference

*   [Binary Tree Zigzag Level Order Traversal 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/binary-tree-zigzag-level-order-traversal/)
*   [Printing a Binary Tree in Zig Zag Level-Order | LeetCode](http://articles.leetcode.com/2010/09/printing-binary-tree-in-zig-zag-level_18.html)

## Binary Tree Serialization

## Question

*   lintcode: [(7) Binary Tree Serialization](http://www.lintcode.com/en/problem/binary-tree-serialization/)

```
Design an algorithm and write code to serialize and deserialize a binary tree.
Writing the tree to a file is called 'serialization'
and reading back from the file to reconstruct
the exact same binary tree is 'deserialization'.
There is no limit of how you deserialize or serialize a binary tree,
you only need to make sure you can serialize a binary tree to a string
and deserialize this string to the original structure.
Have you met this question in a real interview? Yes
Example
An example of testdata: Binary tree {3,9,20,#,#,15,7},
denote the following structure:
  3
 / \
9  20
  /  \
 15   7
Our data serialization use bfs traversal.
This is just for when you got wrong answer and want to debug the input.

You can use other method to do serializaiton and deserialization. 
```

## 题解

根据之前由前序，中序，后序遍历恢复二叉树的经验，确定根节点的位置十分重要（但是这里可能有重复元素，故和之前的题目不太一样）。能直接确定根节点的有前序遍历和广度优先搜索，其中较为简洁的为前序遍历。序列化较为简单，但是反序列化的实现不太容易。需要借助字符串解析工具。

### Python

```
"""
Definition of TreeNode:
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left, self.right = None, None
"""
"""
Definition of TreeNode:
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left, self.right = None, None
"""
class Solution0:
    '''
    @param root: An object of TreeNode, denote the root of the binary tree.
    This method will be invoked first, you should design your own algorithm
    to serialize a binary tree which denote by a root node to a string which
    can be easily deserialized by your own "deserialize" method later.
    '''
    def serialize(self, root):
        if not root:
            return ''

        def post_order(root):
            if root:
                post_order(root.left)
                post_order(root.right)
                ret[0] += str(root.val) + ','
            else:
                ret[0] += '#,'

        ret = ['']
        post_order(root)

        return ret[0][:-1]  # remove last ,

    '''
    @param data: A string serialized by your serialize method.
    This method will be invoked second, the argument data is what exactly
    you serialized at method "serialize", that means the data is not given by
    system, it's given by your own serialize method. So the format of data is
    designed by yourself, and deserialize it here as you serialize it in
    "serialize" method.
    '''
    def deserialize(self, data):
        if not data:
            return

        nodes = data.split(',')
        def post_order(nodes):
            if nodes[-1] == '#':
                nodes.pop()
                return None
            root = TreeNode(int(nodes.pop()))
            root.right = post_order(nodes)
            root.left = post_order(nodes)
            return root
        return post_order(nodes)

class Solution1:
    '''
    @param root: An object of TreeNode, denote the root of the binary tree.
    This method will be invoked first, you should design your own algorithm
    to serialize a binary tree which denote by a root node to a string which
    can be easily deserialized by your own "deserialize" method later.
    '''
    def serialize(self, root):
        if not root:
            return ''

        def pre_order(root):
            if root:
                ret[0] += str(root.val) + ','
                pre_order(root.left)
                pre_order(root.right)
            else:
                ret[0] += '#,'

        ret = ['']
        pre_order(root)

        return ret[0][:-1]  # remove last ,

    '''
    @param data: A string serialized by your serialize method.
    This method will be invoked second, the argument data is what exactly
    you serialized at method "serialize", that means the data is not given by
    system, it's given by your own serialize method. So the format of data is
    designed by yourself, and deserialize it here as you serialize it in
    "serialize" method.
    '''
    def deserialize(self, data):
        if not data:
            return

        nodes = data.split(',')
        self.i = 0
        def pre_order(nodes):
            if nodes[self.i] == '#':
                return None
            root = TreeNode(int(nodes[self.i]))
            self.i += 1
            root.left = pre_order(nodes)
            self.i += 1
            root.right = pre_order(nodes)
            return root
        return pre_order(nodes)

import collections
class Solution2:

    '''
    @param root: An object of TreeNode, denote the root of the binary tree.
    This method will be invoked first, you should design your own algorithm
    to serialize a binary tree which denote by a root node to a string which
    can be easily deserialized by your own "deserialize" method later.
    '''
    def serialize(self, root):
        if not root:
            return

        ret = []
        queue = collections.deque()
        queue.append(root)
        while queue:
            node = queue.popleft()
            if node:
                queue.append(node.left)
                queue.append(node.right)
                ret.append(str(node.val))
            else:
                ret.append('#')
        return ','.join(ret)

    '''
    @param data: A string serialized by your serialize method.
    This method will be invoked second, the argument data is what exactly
    you serialized at method "serialize", that means the data is not given by
    system, it's given by your own serialize method. So the format of data is
    designed by yourself, and deserialize it here as you serialize it in
    "serialize" method.
    '''
    def deserialize(self, data):
        if not data:
            return
        nodes = data.split(',')
        root = TreeNode(int(nodes[0]))
        i = 1
        queue = collections.deque()
        queue.append(root)
        while queue:
            node = queue.popleft()
            if nodes[i] == '#':
                node.left = None
            else:
                t = TreeNode(int(nodes[i]))
                node.left = t
                queue.append(t)
            i += 1
            if nodes[i] == '#':
                node.right = None
            else:
                t = TreeNode(int(nodes[i]))
                node.right = t
                queue.append(t)
            i += 1
        return root 
```

### 源码分析

第零种解法是后序遍历（推荐）， 在`serialize`的时候， 需要先左->右->中。 在`deserialize`的时候，因为是从最后一个值开始 pop， 构成 tree 的时候， 就应该先中->右->左。

第一种解法是前序遍历， 其中巧妙的利用了 python 的 closure， 在`serialize`中， 利用了 list mutable 的特性， 修改了 ret 中的值。 `deserialize`中， 利用了`self.i`来储存`instance variable`。

第二种解法是广度遍历。 在`deserialize`的时候， 保持一个`index i`，记录用过的 node。

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
class Solution {
    /**
     * This method will be invoked first, you should design your own algorithm
     * to serialize a binary tree which denote by a root node to a string which
     * can be easily deserialized by your own "deserialize" method later.
     */
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        if (root == null) return sb.toString();

        seriaHelper(root, sb);

        return sb.substring(0, sb.length() - 1);
    }

    private void seriaHelper(TreeNode root, StringBuilder sb) {
        if (root == null) {
            sb.append("#,");
        } else {
            sb.append(root.val).append(",");
            seriaHelper(root.left, sb);
            seriaHelper(root.right, sb);
        }
    }

    /**
     * This method will be invoked second, the argument data is what exactly
     * you serialized at method "serialize", that means the data is not given by
     * system, it's given by your own serialize method. So the format of data is
     * designed by yourself, and deserialize it here as you serialize it in
     * "serialize" method.
     */
    public TreeNode deserialize(String data) {
        if (data == null || data.length() == 0) return null;

        StringTokenizer st = new StringTokenizer(data, ",");
        return deseriaHelper(st);
    }

    private TreeNode deseriaHelper(StringTokenizer st) {
        if (!st.hasMoreTokens()) return null;

        String val = st.nextToken();
        if (val.equals("#")) {
            return null;
        }

        TreeNode root = new TreeNode(Integer.parseInt(val));
        root.left = deseriaHelper(st);
        root.right = deseriaHelper(st);

        return root;
    }
} 
```

### 源码分析

由二叉树序列化的过程不难，难就难在根据字符串进行反序列化，这里引入了 Java 中的 StringTokenizer 字符串分割工具，非常方便，使得递归得以顺利实现。其中`deseriaHelper`的实现较为巧妙。

### 复杂度分析

略

## Reference

*   [Serialize and Deserialize a Binary Tree (pre order).](https://gist.github.com/bittib/5620951)
*   [Serialization/Deserialization of a Binary Tree | LeetCode](http://articles.leetcode.com/2010/09/serializationdeserialization-of-binary.html)