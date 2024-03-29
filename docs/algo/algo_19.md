# 第 19 节 Binary Tree - 二叉树（三）

## Binary Tree Maximum Path Sum

## Question

*   leetcode: [Binary Tree Maximum Path Sum | LeetCode OJ](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
*   lintcode: [(94) Binary Tree Maximum Path Sum](http://www.lintcode.com/en/problem/binary-tree-maximum-path-sum/)

### Problem Statement

Given a binary tree, find the maximum path sum.

The path may start and end at any node in the tree.

#### Example

Given the below binary tree:

```
 1
 / \
2   3 
```

return `6`.

## 题解 1 - 递归中仅返回子树路径长度

如题目右侧的四颗半五角星所示，本题属于中等偏难的那一类题。题目很短，要求返回最大路径和。咋看一下感觉使用递归应该很快就能搞定，实则不然，**因为从题目来看路径和中不一定包含根节点！也就是说可以起止于树中任一连通节点。**

弄懂题意后我们就来剖析剖析，本着由简入难的原则，我们先来分析若路径和包含根节点，如何才能使其路径和达到最大呢？选定根节点后，路径和中必然包含有根节点的值，剩下的部分则为左子树和右子树，要使路径和最大，则必然要使左子树和右子树中的路径长度都取最大。

> **Warning** 注意区分包含根节点的路径和(题目要的答案)和左子树/右子树部分的路径长度(答案的一个组成部分)。路径和=根节点+左子树路径长度+右子树路径长度

```
 -10
       /  \
      2    -3
     / \   / \
    3   4 5   7 
```

如上所示，包含根节点`-10`的路径和组成的节点应为`4 -> 2 -> -10 <- -3 <- 7`, 对于左子树而言，其可能的路径组成节点为`3 -> 2`或`4 -> 2`, 而不是像根节点的路径和那样为`3 -> 2 <- 4`. 这种差异也就造成了我们不能很愉快地使用递归来求得最大路径和。

我们使用分治的思想来分析路径和/左子树/右子树，设 $$f(root)$$ 为`root`的子树到`root`节点(含)路径长度的最大值，那么我们有 $$f(root) = root->val + \max (f(root->left), ~f(root->right))$$

递归模型已初步建立起来，接下来就是分析如何将左右子树的路径长度和最终题目要求的「路径和」挂钩。设 $$g(root)$$ 为当「路径和」中根节点为`root`时的值，那么我们有 $$g(root) = root->val + f(root->left) + f(root->right)$$

顺着这个思路，我们可以遍历树中的每一个节点求得 $$g(node)$$ 的值，输出最大值即可。如果不采取任何记忆化存储的措施，其时间复杂度必然是指数级别的。嗯，先来看看这个思路的具体实现，后续再对其进行优化。遍历节点我们使用递归版的前序遍历，求单边路径长度采用递归。

### C++ Recursion + Iteration(Not Recommended) <i class="fa fa-hand-o-down"></i>

**Time Limit Exceeded**

```
/**
 * Definition of TreeNode:
 * class TreeNode {
 * public:
 *     int val;
 *     TreeNode *left, *right;
 *     TreeNode(int val) {
 *         this->val = val;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     * @param root: The root of binary tree.
     * @return: An integer
     */
    int maxPathSum(TreeNode *root) {
        if (NULL == root) {
            return 0;
        }

        int result = INT_MIN;
        stack<TreeNode *> s;
        s.push(root);
        while (!s.empty()) {
            TreeNode *node = s.top();
            s.pop();

            int temp_path_sum = node->val + singlePathSum(node->left) \
                                          + singlePathSum(node->right);

            if (temp_path_sum > result) {
                result = temp_path_sum;
            }

            if (NULL != node->right) s.push(node->right);
            if (NULL != node->left) s.push(node->left);
        }

        return result;
    }

private:
    int singlePathSum(TreeNode *root) {
        if (NULL == root) {
            return 0;
        }

        int path_sum = max(singlePathSum(root->left), singlePathSum(root->right));
        return max(0, (root->val + path_sum));
    }
}; 
```

### 源码分析

注意`singlePathSum`中最后的返回值，如果其路径长度`path_sum`比 0 还小，那么取这一段路径反而会减少最终的路径和，故不应取这一段，我们使用 0 表示这一隐含意义。

## 题解 2 - 递归中同时返回子树路径长度和路径和

### C++ using std::pair

上题求路径和和左右子树路径长度是分开求得的，因此导致了时间复杂度剧增的恶劣情况，从题解的递推关系我们可以看出其实是可以在一次递归调用过程中同时求得路径和和左右子树的路径长度的，只不过此时递归程序需要返回的不再是一个值，而是路径长度和路径和这一组值！C++中我们可以使用`pair`或者自定义新的数据类型来相对优雅的解决。

```
/**
 * Definition of TreeNode:
 * class TreeNode {
 * public:
 *     int val;
 *     TreeNode *left, *right;
 *     TreeNode(int val) {
 *         this->val = val;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
class Solution {
private:
    pair<int, int> helper(TreeNode *root) {
        if (NULL == root) {
            return make_pair(0, INT_MIN);
        }

        pair<int, int> leftTree = helper(root->left);
        pair<int, int> rightTree = helper(root->right);

        int single_path_sum = max(leftTree.first, rightTree.first) + root->val;
        single_path_sum = max(0, single_path_sum);

        int max_sub_sum = max(leftTree.second, rightTree.second);
        int max_path_sum = root->val + leftTree.first + rightTree.first;
        max_path_sum = max(max_sub_sum, max_path_sum);

        return make_pair(single_path_sum, max_path_sum);
    }

public:
    /**
     * @param root: The root of binary tree.
     * @return: An integer
     */
    int maxPathSum(TreeNode *root) {
        if (NULL == root) {
            return 0;
        }

        return helper(root).second;
    }
}; 
```

### 源码分析

除了使用`pair`对其进行封装，也可使用嵌套类新建一包含单路径长度和全局路径和两个变量的类，不过我用 C++写的没编译过... 老是提示`...private`，遂用`pair`改写之。建议使用`class`而不是`pair`封装`single_path_sum`和`max_path_sum`[^pair*is*harmful].

这种方法难以理解的地方在于这种实现方式的正确性，较为关键的语句为`max_path_sum = max(max_sub_sum, max_path_sum)`, 这行代码是如何体现题目中以下的这句话的呢？ > The path may start and end at any node in the tree.

简单来讲，题解 2 从两个方面予以保证：

1.  采用「冒泡」法返回不经过根节点的路径和的较大值。
2.  递推子树路径长度(不变值)而不是到该节点的路径和(有可能变化)，从而保证了这种解法的正确性。

如果还不理解的建议就以上面那个根节点为-10 的例子画一画。

### C++

```
/**
 * Definition of TreeNode:
 * class TreeNode {
 * public:
 *     int val;
 *     TreeNode *left, *right;
 *     TreeNode(int val) {
 *         this->val = val;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
class Solution {
    class ResultType {
    public:
        int singlePath, maxPath;
        ResultType(int s, int m):singlePath(s), maxPath(m) {}
    };

private:
    ResultType helper(TreeNode *root) {
        if (root == NULL) {
            ResultType *nullResult = new ResultType(0, INT_MIN);
            return *nullResult;
        }
        // Divide
        ResultType left = helper(root->left);
        ResultType right = helper(root->right);

        // Conquer
        int singlePath = max(left.singlePath, right.singlePath) + root->val;
        singlePath = max(singlePath, 0);

        int maxPath = max(left.maxPath, right.maxPath);
        maxPath = max(maxPath, left.singlePath + right.singlePath + root->val);

        ResultType *result = new ResultType(singlePath, maxPath);
        return *result;
    }

public:
    int maxPathSum(TreeNode *root) {
        ResultType result = helper(root);
        return result.maxPath;
    }
}; 
```

### Java

```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Result {
    int singlePath, maxPath;
    Result(int singlePath, int maxPath) {
        this.singlePath = singlePath;
        this.maxPath = maxPath;
    }
}

public class Solution {
    public int maxPathSum(TreeNode root) {
        return helper(root).maxPath;
    }

    private Result helper(TreeNode root) {
        if (root == null) {
            // maxPath should be MIN_VALUE to avoid negtive
            return new Result(0, Integer.MIN_VALUE);
        }

        Result left = helper(root.left);
        Result right = helper(root.right);

        int singlePath = Math.max(left.singlePath, right.singlePath) + root.val;
        singlePath = Math.max(0, singlePath); // drop negtive

        int maxPath = Math.max(left.maxPath, right.maxPath);
        maxPath = Math.max(maxPath, root.val + left.singlePath + right.singlePath);

        return new Result(singlePath, maxPath);
    }
} 
```

### 源码分析

1.  如果不用 `ResultType *XXX = new ResultType ...` 再 `return *XXX` 的方式，则需要在自定义 class 中重载 `new` operator。
2.  如果遇到 `...private` 的编译错误，则是因为自定义 class 中需要把成员声明为 public，否则需要把访问这个成员的函数也做 class 内部处理。

## Reference

*   [^pair*is*harmful]: [std::pair considered harmful! « Modern Maintainable Code](http://maintainablecode.logdown.com/posts/158531-stdpair-considered-harmful) - 作者指出了`pair`不能滥用的原因，如不可维护，信息量小。
*   [Binary Tree Maximum Path Sum | 九章算法](http://www.jiuzhang.com/solutions/binary-tree-maximum-path-sum/)

## Lowest Common Ancestor

## Question

*   lintcode: [(88) Lowest Common Ancestor](http://www.lintcode.com/en/problem/lowest-common-ancestor/)

### Problem Statement

Given the root and two nodes in a Binary Tree. Find the lowest common ancestor(LCA) of the two nodes.

The lowest common ancestor is the node with largest depth which is the ancestor of both nodes.

#### Example

For the following binary tree:

```
 4
 / \
3   7
   / \
  5   6 
```

LCA(3, 5) = `4`

LCA(5, 6) = `7`

LCA(6, 7) = `7`

## 题解 1 - 自底向上

初次接触这种题可能会没有什么思路，在没有思路的情况下我们就从简单例子开始分析！首先看看`3`和`5`，这两个节点分居根节点`4`的两侧，如果可以从子节点往父节点递推，那么他们将在根节点`4`处第一次重合；再来看看`5`和`6`，这两个都在根节点`4`的右侧，沿着父节点往上递推，他们将在节点`7`处第一次重合；最后来看看`6`和`7`，此时由于`7`是`6`的父节点，故`7`即为所求。从这三个基本例子我们可以总结出两种思路——自顶向下(从前往后递推)和自底向上(从后往前递推)。

顺着上述实例的分析，我们首先看看自底向上的思路，自底向上的实现用一句话来总结就是——如果遍历到的当前节点是 A/B 中的任意一个，那么我们就向父节点汇报此节点，否则递归到节点为空时返回空值。具体来说会有如下几种情况：

1.  当前节点不是两个节点中的任意一个，此时应判断左右子树的返回结果。
    *   若左右子树均返回非空节点，那么当前节点一定是所求的根节点，将当前节点逐层向前汇报。// 两个节点分居树的两侧
    *   若左右子树仅有一个子树返回非空节点，则将此非空节点向父节点汇报。// 节点仅存在于树的一侧
    *   若左右子树均返回`NULL`, 则向父节点返回`NULL`. // 节点不在这棵树中
2.  当前节点即为两个节点中的一个，此时向父节点返回当前节点。

根据此递归模型容易看出应该使用先序/后序遍历来实现。

### C++ Recursion From Bottom to Top

```
/**
 * Definition of TreeNode:
 * class TreeNode {
 * public:
 *     int val;
 *     TreeNode *left, *right;
 *     TreeNode(int val) {
 *         this->val = val;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     * @param root: The root of the binary search tree.
     * @param A and B: two nodes in a Binary.
     * @return: Return the least common ancestor(LCA) of the two nodes.
     */
    TreeNode *lowestCommonAncestor(TreeNode *root, TreeNode *A, TreeNode *B) {
        // return either A or B or NULL
        if (NULL == root || root == A || root == B) return root;

        TreeNode *left = lowestCommonAncestor(root->left, A, B);
        TreeNode *right = lowestCommonAncestor(root->right, A, B);

        // A and B are on both sides
        if ((NULL != left) && (NULL != right)) return root;

        // either left or right or NULL
        return (NULL != left) ? left : right;
    }
}; 
```

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
     * @param root: The root of the binary search tree.
     * @param A and B: two nodes in a Binary.
     * @return: Return the least common ancestor(LCA) of the two nodes.
     */
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode A, TreeNode B) {
        if (root == null) return null;

        TreeNode lNode = lowestCommonAncestor(root.left, A, B);
        TreeNode rNode = lowestCommonAncestor(root.right, A, B);
        // root is the LCA of A and B
        if (lNode != null && rNode != null) return root;
        // root node is A/B(including the case below)
        if (root == A || root == B) return root;
        // return lNode/rNode if root is not LCA
        return (lNode != null) ? lNode : rNode;
    }
} 
```

### 源码分析

结合例子和递归的整体思想去理解代码，在`root == A || root == B`后即层层上浮(自底向上)，直至找到最终的最小公共祖先节点。

最后一行`return (NULL != left) ? left : right;`将非空的左右子树节点和空值都包含在内了，十分精炼！[^leetcode]

> **fixme** 细心的你也许会发现，其实题解的分析漏掉了一种情况，即树中可能只含有 A/B 中的一个节点！这种情况应该返回空值，但上述实现均返回非空节点。

关于重复节点：由于这里比较的是元素地址，因此可以认为树中不存在重复元素，否则不符合树的数据结构。

## 题解 - 自底向上(计数器)

为了解决上述方法可能导致误判的情况，我们可以对返回结果添加计数器来解决。**由于此计数器的值只能由子树向上递推，故应该用后序遍历。**在类中添加私有变量较为方便, C++中的写法较为复杂，后续再优化。

定义`pair<TreeNode *, int> result(node, counter)`表示遍历到某节点时的返回结果，返回的`node`表示 LCA 路径中的可能的最小节点，相应的计数器`counter`则表示目前和`A`或者`B`匹配的节点数，若计数器为 2，则表示已匹配过两次，该节点即为所求，若只匹配过一次，还需进一步向上递推。表述地可能比较模糊，还是看看代码吧。

### C++

```
/**
 * Definition of TreeNode:
 * class TreeNode {
 * public:
 *     int val;
 *     TreeNode *left, *right;
 *     TreeNode(int val) {
 *         this->val = val;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     * @param root: The root of the binary search tree.
     * @param A and B: two nodes in a Binary.
     * @return: Return the least common ancestor(LCA) of the two nodes.
     */
    TreeNode *lowestCommonAncestor(TreeNode *root, TreeNode *A, TreeNode *B) {
        if ((NULL == A) || (NULL == B)) return NULL;

        pair<TreeNode *, int> result = helper(root, A, B);

        if (A != B) {
            return (2 == result.second) ? result.first : NULL;
        } else {
            return (1 == result.second) ? result.first : NULL;
        }
    }

private:
    pair<TreeNode *, int> helper(TreeNode *root, TreeNode *A, TreeNode *B) {
        TreeNode * node = NULL;
        if (NULL == root) return make_pair(node, 0);

        pair<TreeNode *, int> left = helper(root->left, A, B);
        pair<TreeNode *, int> right = helper(root->right, A, B);

        // return either A or B
        int count = max(left.second, right.second);
        if (A == root || B == root)  return make_pair(root, ++count);

        // A and B are on both sides
        if (NULL != left.first && NULL != right.first) return make_pair(root, 2);

        // return either left or right or NULL
        return (NULL != left.first) ? left : right;
    }
}; 
```

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
    private int count = 0;
    /**
     * @param root: The root of the binary search tree.
     * @param A and B: two nodes in a Binary.
     * @return: Return the least common ancestor(LCA) of the two nodes.
     */
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode A, TreeNode B) {
        TreeNode result = helper(root, A, B);
        if (A == B) {
            return result;
        } else {
            return (count == 2) ? result : null;
        }
    }

    private TreeNode helper(TreeNode root, TreeNode A, TreeNode B) {
        if (root == null) return null;

        TreeNode lNode = helper(root.left, A, B);
        TreeNode rNode = helper(root.right, A, B);
        // root is the LCA of A and B
        if (lNode != null && rNode != null) return root;
        // root node is A/B(including the case below)
        if (root == A || root == B) {
            count++;
            return root;
        }
        // return lNode/rNode if root is not LCA
        return (lNode != null) ? lNode : rNode;
    }
} 
```

### 源码分析

在`A == B`时，计数器返回 1 的节点即为我们需要的节点，否则只取返回 2 的节点，如此便保证了该方法的正确性。对这种实现还有问题的在下面评论吧。

## Reference

*   [^leetcode]: [Lowest Common Ancestor of a Binary Tree Part I | LeetCode](http://articles.leetcode.com/2011/07/lowest-common-ancestor-of-a-binary-tree-part-i.html) - 清晰易懂的题解和实现。
*   [Lowest Common Ancestor of a Binary Tree Part II | LeetCode](http://articles.leetcode.com/2011/07/lowest-common-ancestor-of-a-binary-tree-part-ii.html) - 如果存在指向父节点的指针，我们能否有更好的解决方案？
*   [Lowest Common Ancestor of a Binary Search Tree (BST) | LeetCode](http://articles.leetcode.com/2011/07/lowest-common-ancestor-of-a-binary-search-tree.html) - 二叉搜索树中求最小公共祖先。
*   [Lowest Common Ancestor | 九章算法](http://www.jiuzhang.com/solutions/lowest-common-ancestor/) - 第一种和第二种方法可以在知道父节点时使用，但第二种 Divide and Conquer 才是本题需要的思想（第二种解法可以轻易改成不需要 parent 的指针的）。

## Invert Binary Tree

## Question

*   leetcode: [Invert Binary Tree | LeetCode OJ](https://leetcode.com/problems/invert-binary-tree/)
*   lintcode: [(175) Invert Binary Tree](http://www.lintcode.com/en/problem/invert-binary-tree/)

```
Invert a binary tree.

Example
  1         1
 / \       / \
2   3  => 3   2
   /       \
  4         4
Challenge
Do it in recursion is acceptable, can you do it without recursion? 
```

## 题解 1 - Recursive

二叉树的题用递归的思想求解自然是最容易的，此题要求为交换左右子节点，故递归交换之即可。具体实现可分返回值为空或者二叉树节点两种情况，返回值为节点的情况理解起来相对不那么直观一些。

### C++ - return void

```
/**
 * Definition of TreeNode:
 * class TreeNode {
 * public:
 *     int val;
 *     TreeNode *left, *right;
 *     TreeNode(int val) {
 *         this->val = val;
 *         this->left = this->right = NULL;
 *     }
 * };
 */
class Solution {
public:
    /**
     * @param root: a TreeNode, the root of the binary tree
     * @return: nothing
     */
    void invertBinaryTree(TreeNode *root) {
        if (root == NULL) return;

        TreeNode *temp = root->left;
        root->left = root->right;
        root->right = temp;

        invertBinaryTree(root->left);
        invertBinaryTree(root->right);
    }
}; 
```

### C++ - return TreeNode *

```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (root == NULL) return NULL;

        TreeNode *temp = root->left;
        root->left = invertTree(root->right);
        root->right = invertTree(temp);

        return root;
    }
}; 
```

### 源码分析

分三块实现，首先是节点为空的情况，然后使用临时变量交换左右节点，最后递归调用，递归调用的正确性可通过画图理解。

### 复杂度分析

每个节点遍历一次，时间复杂度为 $$O(n)$$, 使用了临时变量，空间复杂度为 $$O(1)$$.

## 题解 2 - Iterative

递归的实现非常简单，那么非递归的如何实现呢？如果将递归改写成栈的实现，那么简单来讲就需要两个栈了，稍显复杂。其实仔细观察此题可发现使用 level-order 的遍历次序也可实现。即从根节点开始入队，交换左右节点，并将非空的左右子节点入队，从队列中取出节点，交换之，直至队列为空。

### C++

```
/**
 * Definition of TreeNode:
 * class TreeNode {
 * public:
 *     int val;
 *     TreeNode *left, *right;
 *     TreeNode(int val) {
 *         this->val = val;
 *         this->left = this->right = NULL;
 *     }
 * };
 */
class Solution {
public:
    /**
     * @param root: a TreeNode, the root of the binary tree
     * @return: nothing
     */
    void invertBinaryTree(TreeNode *root) {
        if (root == NULL) return;

        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()) {
            // pop out the front node
            TreeNode *node = q.front();
            q.pop();
            // swap between left and right pointer
            swap(node->left, node->right);
            // push non-NULL node
            if (node->left != NULL) q.push(node->left);
            if (node->right != NULL) q.push(node->right);
        }
    }
}; 
```

### 源码分析

交换左右指针后需要判断子节点是否非空，仅入队非空子节点。

### 复杂度分析

遍历每一个节点，时间复杂度为 $$O(n)$$, 使用了队列，最多存储最下一层子节点数目，最多只有总节点数的一半，故最坏情况下 $$O(n)$$.

## Reference

*   [0ms C++ Recursive/Iterative Solutions with Explanations - Leetcode Discuss](https://leetcode.com/discuss/42613/0ms-c-recursive-iterative-solutions-with-explanations)

## Diameter of a Binary Tree

## Question

*   [Diameter of a Binary Tree - GeeksforGeeks](http://www.geeksforgeeks.org/diameter-of-a-binary-tree/)

```
The diameter of a tree (sometimes called the width) is the number of nodes
on the longest path between two leaves in the tree.
The diagram below shows two trees each with diameter nine,
the leaves that form the ends of a longest path are shaded
(note that there is more than one path in each tree of length nine,
but no path longer than nine nodes). 
```

![Diameter of a Binary Tree](img/0c3179892dd4850dcebc803c1c7288c2.jpg)

## 题解

和题 [Lowest Common Ancestor](http://algorithm.yuanbin.me/zh-hans/binary_tree/lowest_common_ancestor.html) 分析思路特别接近。

### Java

```
class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int val) {
        this.val = val;
        this.left = null;
        this.right = null;
    }
}

public class Solution {
    public int diameter(TreeNode root) {
        if (root == null) return 0;

        // left, right height
        int leftHight = getHeight(root.left);
        int rightHight = getHeight(root.right);

        // left, right subtree diameter
        int leftDia = diameter(root.left);
        int rightDia = diameter(root.right);

        int maxSubDia = Math.max(leftDia, rightDia);
        return Math.max(maxSubDia, leftHight + 1 + rightHight);
    }

    private int getHeight(TreeNode root) {
        if (root == null) return 0;

        return 1 + Math.max(getHeight(root.left), getHeight(root.right));
    }

    public static void main(String[] args) {
        TreeNode root = new TreeNode(1);
        root.left = new TreeNode(2);
        root.right = new TreeNode(3);
        root.left.left = new TreeNode(4);
        root.left.right = new TreeNode(5);
        root.left.right.left = new TreeNode(6);
        root.left.right.left.right = new TreeNode(7);
        root.left.left.left = new TreeNode(8);

        Solution sol = new Solution();
        int maxDistance = sol.diameter(root);
        System.out.println("Max Distance: " + maxDistance);
    }
} 
```

## Reference

*   [Diameter of a Binary Tree - GeeksforGeeks](http://www.geeksforgeeks.org/diameter-of-a-binary-tree/)
*   [Diameter of a Binary Tree | Algorithms](http://algorithms.tutorialhorizon.com/diameter-of-a-binary-tree/)