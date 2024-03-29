# 第 17 节 Binary Tree - 二叉树（一）

二叉树的基本概念在 [Binary Tree | Algorithm](http://algorithm.yuanbin.me/zh-hans/basics_data_structure/binary_tree.html) 中有简要的介绍，这里就二叉树的一些应用做一些实战演练。

二叉树的遍历大致可分为前序、中序、后序三种方法。

下图是把本章中所有出现的题目归类总结了一下，便于记忆

![二叉树](https://raw.githubusercontent.com/billryan/algorithm-exercise/master/images/binary_tree_summary.png)

## Binary Tree Preorder Traversal

## Question

*   leetcode: [Binary Tree Preorder Traversal | LeetCode OJ](https://leetcode.com/problems/binary-tree-preorder-traversal/)
*   lintcode: [(66) Binary Tree Preorder Traversal](http://www.lintcode.com/en/problem/binary-tree-preorder-traversal/)

### Problem Statement

Given a binary tree, return the preorder traversal of its nodes' values.

#### Example

Given binary tree `{1,#,2,3}`:

```
1
 \
  2
 /
3 
```

return `[1,2,3]`.

#### Challenge

Can you do it without recursion?

## 题解 1 - 递归

**面试时不推荐递归这种做法。**

递归版很好理解，首先判断当前节点(根节点)是否为`null`，是则返回空 vector，否则先返回当前节点的值，然后对当前节点的左节点递归，最后对当前节点的右节点递归。递归时对返回结果的处理方式不同可进一步细分为遍历和分治两种方法。

### Python - Divide and Conquer

```
"""
Definition of TreeNode:
class TreeNode:
    def __init__(self, val):
        this.val = val
        this.left, this.right = None, None
"""

class Solution:
    """
    @param root: The root of binary tree.
    @return: Preorder in ArrayList which contains node values.
    """
    def preorderTraversal(self, root):
        if root == None:
            return []
        return [root.val] + self.preorderTraversal(root.left) \
                          + self.preorderTraversal(root.right) 
```

### C++ - Divide and Conquer

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
     * @return: Preorder in vector which contains node values.
     */
    vector<int> preorderTraversal(TreeNode *root) {
        vector<int> result;
        if (root != NULL) {
            // Divide (分)
            vector<int> left = preorderTraversal(root->left);
            vector<int> right = preorderTraversal(root->right);
            // Merge
            result.push_back(root->val);
            result.insert(result.end(), left.begin(), left.end());
            result.insert(result.end(), right.begin(), right.end());
        }

        return result;
    }
}; 
```

### C++ - Traversal

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
     * @return: Preorder in vector which contains node values.
     */
    vector<int> preorderTraversal(TreeNode *root) {
        vector<int> result;
        traverse(root, result);

        return result;
    }

private:
    void traverse(TreeNode *root, vector<int> &ret) {
        if (root != NULL) {
            ret.push_back(root->val);
            traverse(root->left, ret);
            traverse(root->right, ret);
        }
    }
}; 
```

### Java - Divide and Conquer

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
public class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();
        if (root != null) {
            // Divide
            List<Integer> left = preorderTraversal(root.left);
            List<Integer> right = preorderTraversal(root.right);
            // Merge
            result.add(root.val);
            result.addAll(left);
            result.addAll(right);
        }

        return result;
    }
} 
```

### 源码分析

使用遍历的方法保存递归返回结果需要使用辅助递归函数`traverse`，将结果作为参数传入递归函数中，传值时注意应使用`vector`的引用。 分治方法首先分开计算各结果，最后合并到最终结果中。 C++ 中由于是使用 vector, 将新的 vector 插入另一 vector 不能再使用 push_back, 而应该使用 insert。 Java 中使用`addAll`方法.

### 复杂度分析

遍历树中节点，时间复杂度 $$O(n)$$, 未使用额外空间。

## 题解 2 - 迭代

迭代时需要利用栈来保存遍历到的节点，纸上画图分析后发现应首先进行出栈抛出当前节点，保存当前节点的值，随后将右、左节点分别入栈(注意入栈顺序，先右后左)，迭代到其为叶子节点(NULL)为止。

### Python

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    # @param {TreeNode} root
    # @return {integer[]}
    def preorderTraversal(self, root):
        if root is None:
            return []

        result = []
        s = []
        s.append(root)
        while s:
            root = s.pop()
            result.append(root.val)
            if root.right is not None:
                s.append(root.right)
            if root.left is not None:
                s.append(root.left)

        return result 
```

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
     * @param root: The root of binary tree.
     * @return: Preorder in vector which contains node values.
     */
    vector<int> preorderTraversal(TreeNode *root) {
        vector<int> result;
        if (root == NULL) return result;

        stack<TreeNode *> s;
        s.push(root);
        while (!s.empty()) {
            TreeNode *node = s.top();
            s.pop();
            result.push_back(node->val);
            if (node->right != NULL) {
                s.push(node->right);
            }
            if (node->left != NULL) {
                s.push(node->left);
            }
        }

        return result;
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
public class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();
        if (root == null) return result;

        Deque<TreeNode> stack = new ArrayDeque<TreeNode>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            result.add(node.val);

            if (node.right != null) stack.push(node.right);
            if (node.left != null) stack.push(node.left);
        }

        return result;
    }
} 
```

### 源码分析

1.  对 root 进行异常处理
2.  将 root 压入栈
3.  循环终止条件为栈 s 为空，所有元素均已处理完
4.  访问当前栈顶元素(首先取出栈顶元素，随后 pop 掉栈顶元素)并存入最终结果
5.  将右、左节点分别压入栈内，以便取元素时为先左后右。
6.  返回最终结果

其中步骤 4,5,6 为迭代的核心，对应前序遍历「根左右」。

所以说到底，**使用迭代，只不过是另外一种形式的递归。**使用递归的思想去理解遍历问题会容易理解许多。

### 复杂度分析

使用辅助栈，最坏情况下栈空间与节点数相等，空间复杂度近似为 $$O(n)$$, 对每个节点遍历一次，时间复杂度近似为 $$O(n)$$.

## Binary Tree Inorder Traversal

## Question

*   leetcode: [Binary Tree Inorder Traversal | LeetCode OJ](https://leetcode.com/problems/binary-tree-inorder-traversal/)
*   lintcode: [(67) Binary Tree Inorder Traversal](http://www.lintcode.com/en/problem/binary-tree-inorder-traversal/)

### Problem Statement

Given a binary tree, return the *inorder* traversal of its nodes' values.

#### Example

Given binary tree `{1,#,2,3}`,

```
 1
    \
     2
    /
   3 
```

return `[1,3,2]`.

#### Challenge

Can you do it without recursion?

## 题解 1 - 递归版

中序遍历的访问顺序为『先左再根后右』，递归版最好理解，递归调用时注意返回值和递归左右子树的顺序即可。

### Python

```
"""
Definition of TreeNode:
class TreeNode:
    def __init__(self, val):
        this.val = val
        this.left, this.right = None, None
"""

class Solution:
    """
    @param root: The root of binary tree.
    @return: Inorder in ArrayList which contains node values.
    """
    def inorderTraversal(self, root):
        if root is None:
            return []
        else:
            return [root.val] + self.inorderTraversal(root.left) \
                              + self.inorderTraversal(root.right) 
```

### Python - with helper

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    # @param {TreeNode} root
    # @return {integer[]}
    def inorderTraversal(self, root):
        result = []
        self.helper(root, result)
        return result

    def helper(self, root, ret):
        if root is not None:
            self.helper(root.left, ret)
            ret.append(root.val)
            self.helper(root.right, ret) 
```

### C++

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
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        helper(root, result);
        return result;
    }

private:
    void helper(TreeNode *root, vector<int> &ret) {
        if (root != NULL) {
            helper(root->left, ret);
            ret.push_back(root->val);
            helper(root->right, ret);
        }
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
public class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();
        helper(root, result);
        return result;
    }

    private void helper(TreeNode root, List<Integer> ret) {
        if (root != null) {
            helper(root.left, ret);
            ret.add(root.val);
            helper(root.right, ret);
        }
    }
} 
```

### 源码分析

Python 这种动态语言在写递归时返回结果好处理点，无需声明类型。通用的方法为在递归函数入口参数中传入返回结果， 也可使用分治的方法替代辅助函数。

### 复杂度分析

树中每个节点都需要被访问常数次，时间复杂度近似为 $$O(n)$$. 未使用额外辅助空间。

## 题解 2 - 迭代版

使用辅助栈改写递归程序，中序遍历没有前序遍历好写，其中之一就在于入栈出栈的顺序和限制规则。我们采用「左根右」的访问顺序可知主要由如下四步构成。

1.  首先需要一直对左子树迭代并将非空节点入栈
2.  节点指针为空后不再入栈
3.  当前节点为空时进行出栈操作，并访问栈顶节点
4.  将当前指针 p 用其右子节点替代

步骤 2,3,4 对应「左根右」的遍历结构，只是此时的步骤 2 取的左值为空。

### Python

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    # @param {TreeNode} root
    # @return {integer[]}
    def inorderTraversal(self, root):
        result = []
        s = []
        while root is not None or s:
            if root is not None:
                s.append(root)
                root = root.left
            else:
                root = s.pop()
                result.append(root.val)
                root = root.right

        return result 
```

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
    /**
     * @param root: The root of binary tree.
     * @return: Inorder in vector which contains node values.
     */
public:
    vector<int> inorderTraversal(TreeNode *root) {
        vector<int> result;
        stack<TreeNode *> s;

        while (!s.empty() || NULL != root) {
            if (root != NULL) {
                s.push(root);
                root = root->left;
            } else {
                root = s.top();
                s.pop();
                result.push_back(root->val);
                root = root->right;
            }
        }

        return result;
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
public class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();
        if (root == null) return result;

        Deque<TreeNode> stack = new ArrayDeque<TreeNode>();
        while (root != null || (!stack.isEmpty())) {
            if (root != null) {
                stack.push(root);
                root = root.left;
            } else {
                root = stack.pop();
                result.add(root.val);
                root = root.right;
            }
        }

        return result;
    }
} 
```

### 源码分析

使用栈的思想模拟递归，注意迭代的演进和边界条件即可。

### 复杂度分析

最坏情况下栈保存所有节点，空间复杂度 $$O(n)$$, 时间复杂度 $$O(n)$$.

## Reference

## Binary Tree Postorder Traversal

## Question

*   leetcode: [Binary Tree Postorder Traversal | LeetCode OJ](https://leetcode.com/problems/binary-tree-postorder-traversal/)
*   lintcode: [(68) Binary Tree Postorder Traversal](http://www.lintcode.com/en/problem/binary-tree-postorder-traversal/)

### Problem Statement

Given a binary tree, return the *postorder* traversal of its nodes' values.

#### Example

Given binary tree `{1,#,2,3}`,

```
 1
    \
     2
    /
   3 
```

return `[3,2,1]`.

#### Challenge

Can you do it without recursion?

## 题解 1 - 递归

首先使用递归便于理解。

### Python - Divide and Conquer

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    # @param {TreeNode} root
    # @return {integer[]}
    def postorderTraversal(self, root):
        if root is None:
            return []
        else:
            return self.postorderTraversal(root.left) +\
                   self.postorderTraversal(root.right) + [root.val] 
```

### C++ - Traversal

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
    /**
     * @param root: The root of binary tree.
     * @return: Postorder in vector which contains node values.
     */
public:
    vector<int> postorderTraversal(TreeNode *root) {
        vector<int> result;

        traverse(root, result);

        return result;
    }

private:
    void traverse(TreeNode *root, vector<int> &ret) {
        if (root == NULL) {
            return;
        }

        traverse(root->left, ret);
        traverse(root->right, ret);
        ret.push_back(root->val);
    }
}; 
```

### Java - Divide and Conquer

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
public class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();
        if (root != null) {
            List<Integer> left = postorderTraversal(root.left);
            result.addAll(left);
            List<Integer> right = postorderTraversal(root.right);
            result.addAll(right);
            result.add(root.val);
        }

        return result;
    }
} 
```

### 源码分析

递归版的太简单了，没啥好说的，注意入栈顺序。

### 复杂度分析

时间复杂度近似为 $$O(n)$$.

## 题解 2 - 迭代

使用递归写后序遍历那是相当的简单，我们来个不使用递归的迭代版。整体思路仍然为「左右根」，那么怎么才能知道什么时候该访问根节点呢？问题即转化为如何保证左右子节点一定先被访问到？由于入栈之后左右节点已无法区分，因此需要区分左右子节点是否被访问过(加入到最终返回结果中)。除了有左右节点的情况，根节点也可能没有任何子节点，此时也可直接将其值加入到最终返回结果中。

### Python

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    # @param {TreeNode} root
    # @return {integer[]}
    def postorderTraversal(self, root):
        result = []
        if root is None:
            return result
        s = []
        # previously traversed node
        prev = None
        s.append(root)
        while s:
            curr = s[-1]
            noChild = curr.left is None and curr.right is None
            childVisited = (prev is not None) and \
                           (curr.left == prev or curr.right == prev)
            if noChild or childVisited:
                result.append(curr.val)
                s.pop()
                prev = curr
            else:
                if curr.right is not None:
                    s.append(curr.right)
                if curr.left is not None:
                    s.append(curr.left)

        return result 
```

### C++

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
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> result;
        if (root == NULL) return result;

        TreeNode *prev = NULL;
        stack<TreeNode *> s;
        s.push(root);
        while (!s.empty()) {
            TreeNode *curr = s.top();
            bool noChild = false;
            if (curr->left == NULL && curr->right == NULL) {
                noChild = true;
            }
            bool childVisited = false;
            if (prev != NULL && (curr->left == prev || curr->right == prev)) {
                childVisited = true;
            }

            // traverse
            if (noChild || childVisited) {
                result.push_back(curr->val);
                s.pop();
                prev = curr;
            } else {
                if (curr->right != NULL) s.push(curr->right);
                if (curr->left != NULL) s.push(curr->left);
            }
        }

        return result;
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
public class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();
        if (root == null) return result;

        Deque<TreeNode> stack = new ArrayDeque<TreeNode>();
        stack.push(root);
        TreeNode prev = null;
        while (!stack.isEmpty()) {
            TreeNode curr = stack.peek();
            boolean noChild = (curr.left == null && curr.right == null);
            boolean childVisited = false;
            if (prev != null && (curr.left == prev || curr.right == prev)) {
                childVisited = true;
            }

            if (noChild || childVisited) {
                result.add(curr.val);
                stack.pop();
                prev = curr;
            } else {
                if (curr.right != null) stack.push(curr.right);
                if (curr.left != null) stack.push(curr.left);
            }
        }

        return result;
    }
} 
```

### 源码分析

遍历顺序为『左右根』，判断根节点是否应该从栈中剔除有两种条件，一为无子节点，二为子节点已遍历过。判断子节点是否遍历过需要排除`prev == null` 的情况，因为 prev 初始化为 null.

**将递归写成迭代的难点在于如何在迭代中体现递归本质及边界条件的确立，可使用简单示例和纸上画出栈调用图辅助分析。**

### 复杂度分析

最坏情况下栈内存储所有节点，空间复杂度近似为 $$O(n)$$, 每个节点遍历两次或以上，时间复杂度近似为 $$O(n)$$.

## 题解 3 - 反转先序遍历

要想得到『左右根』的后序遍历结果，我们发现只需将『根右左』的结果转置即可，而先序遍历通常为『根左右』，故改变『左右』的顺序即可，所以如此一来后序遍历的非递归实现起来就非常简单了。

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
    /**
     * @param root: The root of binary tree.
     * @return: Postorder in vector which contains node values.
     */
public:
    vector<int> postorderTraversal(TreeNode *root) {
        vector<int> result;
        if (root == NULL) return result;

        stack<TreeNode*> s;
        s.push(root);
        while (!s.empty()) {
            TreeNode *node = s.top();
            s.pop();
            result.push_back(node->val);
            // root, right, left => left, right, root
            if (node->left != NULL) s.push(node->left);
            if (node->right != NULL) s.push(node->right);
        }
        // reverse
        std::reverse(result.begin(), result.end());
        return result;
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
     * @param root: The root of binary tree.
     * @return: Postorder in ArrayList which contains node values.
     */
    public ArrayList<Integer> postorderTraversal(TreeNode root) {
        ArrayList<Integer> result = new ArrayList<Integer>();
        if (root == null) return result;

        Deque<TreeNode> stack = new ArrayDeque<TreeNode>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            result.add(node.val);
            if (node.left != null) stack.push(node.left);
            if (node.right != null) stack.push(node.right);
        }
        Collections.reverse(result);

        return result;
    }
} 
```

### 源码分析

注意入栈的顺序和最后转置即可。

### 复杂度分析

同先序遍历。

## Reference

*   [[leetcode]Binary Tree Postorder Traversal @ Python - 南郭子綦](http://www.cnblogs.com/zuoyuan/p/3720846.html) - 解释清晰
*   [更简单的非递归遍历二叉树的方法](http://zisong.me/post/suan-fa/geng-jian-dan-de-bian-li-er-cha-shu-de-fang-fa) - 比较新颖和简洁的实现