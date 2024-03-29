# 第 21 节 Binary Search Tree - 二叉搜索树

二叉搜索树的定义及简介在 [Binary Search Trees](http://algorithm.yuanbin.me/zh-hans/basics_data_structure/binary_search_tree.html) 中已经有所介绍。简单来说就是当前节点的值大于等于左子结点的值，而小于右子节点的值。

## Insert Node in a Binary Search Tree

## Question

*   lintcode: [(85) Insert Node in a Binary Search Tree](http://www.lintcode.com/en/problem/insert-node-in-a-binary-search-tree/)

```
Given a binary search tree  and a new tree node, insert the node into the tree. You should keep the tree still be a valid binary search tree.

Example
Given binary search tree as follow:

     2

  /    \

1        4

         /

       3

after Insert node 6, the tree should be:

     2

  /    \

1        4

         /   \

       3        6

Challenge
Do it without recursion 
```

## 题解 - 递归

二叉树的题使用递归自然是最好理解的，代码也简洁易懂，缺点就是递归调用时栈空间容易溢出，故实际实现中一般使用迭代替代递归，性能更佳嘛。不过迭代的缺点就是代码量稍(很)大，逻辑也可能不是那么好懂。

既然确定使用递归，那么接下来就应该考虑具体的实现问题了。在递归的具体实现中，主要考虑如下两点：

1.  基本条件/终止条件 - 返回值需斟酌。
2.  递归步/条件递归 - 能使原始问题收敛。

首先来找找递归步，根据二叉查找树的定义，若插入节点的值若大于当前节点的值，则继续与当前节点的右子树的值进行比较；反之则继续与当前节点的左子树的值进行比较。题目的要求是返回最终二叉搜索树的根节点，从以上递归步的描述中似乎还难以对应到实际代码，这时不妨分析下终止条件。

有了递归步，终止条件也就水到渠成了，若当前节点为空时，即返回结果。问题是——返回什么结果？当前节点为空时，说明应该将「插入节点」插入到上一个遍历节点的左子节点或右子节点。对应到程序代码中即为`root->right = node`或者`root->left = node`. 也就是说递归步使用`root->right/left = func(...)`即可。

### C++ Recursion

```
/**
 * forked from http://www.jiuzhang.com/solutions/insert-node-in-binary-search-tree/
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
     * @param node: insert this node into the binary search tree
     * @return: The root of the new binary search tree.
     */
    TreeNode* insertNode(TreeNode* root, TreeNode* node) {
        if (NULL == root) {
            return node;
        }

        if (node->val <= root->val) {
            root->left = insertNode(root->left, node);
        } else {
            root->right = insertNode(root->right, node);
        }

        return root;
    }
}; 
```

### Java Recursion

```
public class Solution {
    /**
     * @param root: The root of the binary search tree.
     * @param node: insert this node into the binary search tree
     * @return: The root of the new binary search tree.
     */
    public TreeNode insertNode(TreeNode root, TreeNode node) {
        if (root == null) {
            return node;
        }
        if (root.val > node.val) {
            root.left = insertNode(root.left, node);
        } else {
            root.right = insertNode(root.right, node);
        }
        return root;
    }
} 
```

## 题解 - 迭代

看过了以上递归版的题解，对于这个题来说，将递归转化为迭代的思路也是非常清晰易懂的。迭代比较当前节点的值和插入节点的值，到了二叉树的最后一层时选择是链接至左子结点还是右子节点。

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
     * @param node: insert this node into the binary search tree
     * @return: The root of the new binary search tree.
     */
    TreeNode* insertNode(TreeNode* root, TreeNode* node) {
        if (NULL == root) {
            return node;
        }

        TreeNode* tempNode = root;
        while (NULL != tempNode) {
            if (node->val <= tempNode->val) {
                if (NULL == tempNode->left) {
                    tempNode->left = node;
                    return root;
                }
                tempNode = tempNode->left;
            } else {
                if (NULL == tempNode->right) {
                    tempNode->right = node;
                    return root;
                }
                tempNode = tempNode->right;
            }
        }

        return root;
    }
}; 
```

### 源码分析

在`NULL == tempNode->right`或者`NULL == tempNode->left`时需要在链接完`node`后立即返回`root`，避免死循环。

### Java Iterative

```
public class Solution {
    /**
     * @param root: The root of the binary search tree.
     * @param node: insert this node into the binary search tree
     * @return: The root of the new binary search tree.
     */
    public TreeNode insertNode(TreeNode root, TreeNode node) {
        // write your code here
        if (root == null) return node;
        if (node == null) return root;

        TreeNode rootcopy = root;
        while (root != null) {
            if (root.val <= node.val && root.right == null) {
                root.right = node;
                break;
            }
            else if (root.val > node.val && root.left == null) {
                root.left = node;
                break;
            }
            else if(root.val <= node.val) root = root.right;
            else root = root.left;
        }
        return rootcopy;
    }
} 
```

## Validate Binary Search Tree

## Question

*   lintcode: [(95) Validate Binary Search Tree](http://www.lintcode.com/en/problem/validate-binary-search-tree/)

```
Given a binary tree, determine if it is a valid binary search tree (BST).

Assume a BST is defined as follows:

The left subtree of a node contains only nodes with keys less than the node's key.
The right subtree of a node contains only nodes with keys greater than the node's key.
Both the left and right subtrees must also be binary search trees.
Example
An example:

   1
  / \
 2   3
    /
   4
    \
     5
The above binary tree is serialized as "{1,2,3,#,#,4,#,#,5}". 
```

## 题解 1 - recursion

按照题中对二叉搜索树所给的定义递归判断，我们从递归的两个步骤出发分析：

1.  基本条件/终止条件 - 返回值需斟酌。
2.  递归步/条件递归 - 能使原始问题收敛。

终止条件好确定——当前节点为空，或者不符合二叉搜索树的定义，返回值分别是什么呢？先别急，分析下递归步试试先。递归步的核心步骤为比较当前节点的`key`和左右子节点的`key`大小，和定义不符则返回`false`, 否则递归处理。从这里可以看出在节点为空时应返回`true`, 由上层的其他条件判断。但需要注意的是这里不仅要考虑根节点与当前的左右子节点，**还需要考虑左子树中父节点的最小值和右子树中父节点的最大值。**否则程序在`[10,5,15,#,#,6,20]` 这种 case 误判。

由于不仅需要考虑当前父节点，还需要考虑父节点的父节点... 故递归时需要引入上界和下界值。画图分析可知对于左子树我们需要比较父节点中最小值，对于右子树则是父节点中的最大值。又由于满足二叉搜索树的定义时，左子结点的值一定小于根节点，右子节点的值一定大于根节点，故无需比较所有父节点的值，使用递推即可得上界与下界，这里的实现非常巧妙。

### C++ - long long

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
     * @return: True if the binary tree is BST, or false
     */
    bool isValidBST(TreeNode *root) {
        if (root == NULL) return true;

        return helper(root, LLONG_MIN, LLONG_MAX);
    }

    bool helper(TreeNode *root, long long lower, long long upper) {
        if (root == NULL) return true;

        if (root->val <= lower || root->val >= upper) return false;
        bool isLeftValidBST = helper(root->left, lower, root->val);
        bool isRightValidBST = helper(root->right, root->val, upper);

        return isLeftValidBST && isRightValidBST;
    }
}; 
```

### C++ - without long long

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
     * @return: True if the binary tree is BST, or false
     */
    bool isValidBST(TreeNode *root) {
        if (root == NULL) return true;

        return helper(root, INT_MIN, INT_MAX);
    }

    bool helper(TreeNode *root, int lower, int upper) {
        if (root == NULL) return true;

        if (root->val <= lower || root->val >= upper) {
            bool right_max = root->val == INT_MAX && root->right == NULL;
            bool left_min = root->val == INT_MIN && root->left == NULL;
            if (!(right_max || left_min)) {
                return false;
            }
        }
        bool isLeftValidBST = helper(root->left, lower, root->val);
        bool isRightValidBST = helper(root->right, root->val, upper);

        return isLeftValidBST && isRightValidBST;
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
     * @return: True if the binary tree is BST, or false
     */
    public boolean isValidBST(TreeNode root) {
        if (root == null) return true;

        return helper(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }

    private boolean helper(TreeNode root, long lower, long upper) {
        if (root == null) return true;
        // System.out.println("root.val = " + root.val + ", lower = " + lower + ", upper = " + upper);
        // left node value < root node value < right node value
        if (root.val >= upper || root.val <= lower) return false;
        boolean isLeftValidBST = helper(root.left, lower, root.val);
        boolean isRightValidBST = helper(root.right, root.val, upper);

        return isLeftValidBST && isRightValidBST;
    }
} 
```

### 源码分析

为避免节点中出现整型的最大最小值，引入 long 型进行比较。有些 BST 的定义允许左子结点的值与根节点相同，此时需要更改比较条件为`root.val > upper`. C++ 中 long 可能与 int 范围相同，故使用 long long. 如果不使用比 int 型更大的类型，那么就需要在相等时多加一些判断。

### 复杂度分析

递归遍历所有节点，时间复杂度为 $$O(n)$$, 使用了部分额外空间，空间复杂度为 $$O(1)$$.

## 题解 2 - iteration

联想到二叉树的中序遍历。 TBD

## Reference

*   [LeetCode: Validate Binary Search Tree 解题报告 - Yu's Garden - 博客园](http://www.cnblogs.com/yuzhangcmu/p/4177047.html) - 提供了 4 种不同的方法，思路可以参考。

## Search Range in Binary Search Tree

## Question

*   lintcode: [(11) Search Range in Binary Search Tree](http://www.lintcode.com/en/problem/search-range-in-binary-search-tree/)

### Problem Statement

Given two values k1 and k2 (where k1 < k2) and a root pointer to a Binary Search Tree. Find all the keys of tree in range k1 to k2\. i.e. print all x such that k1<=x<=k2 and x is a key of given BST. Return all the keys in ascending order.

#### Example

If k1 = `10` and k2 = `22`, then your function should return `[12, 20, 22]`.

```
 20
   /  \
  8   22
 / \
4   12 
```

## 题解 - 中序遍历

中等偏易难度题，本题涉及到二叉查找树的按序输出，应马上联想到二叉树的中序遍历，对于二叉查找树而言，使用中序遍历即可得到有序元素。对每次访问的元素加以判断即可得最后结果，由于 OJ 上给的模板不适合递归处理，新建一个私有方法即可。

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
     * @param k1 and k2: range k1 to k2.
     * @return: Return all keys that k1<=key<=k2 in ascending order.
     */
    vector<int> searchRange(TreeNode* root, int k1, int k2) {
        vector<int> result;
        inorder_dfs(result, root, k1, k2);

        return result;
    }

private:
    void inorder_dfs(vector<int> &ret, TreeNode *root, int k1, int k2) {
        if (NULL == root) {
            return;
        }

        inorder_dfs(ret, root->left, k1, k2);
        if ((root->val >= k1) && (root->val <= k2)) {
            ret.push_back(root->val);
        }
        inorder_dfs(ret, root->right, k1, k2);
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
     * @param k1 and k2: range k1 to k2.
     * @return: Return all keys that k1<=key<=k2 in ascending order.
     */
    public ArrayList<Integer> searchRange(TreeNode root, int k1, int k2) {
        ArrayList<Integer> result = new ArrayList<Integer>();
        helper(root, k1, k2, result);

        return result;
    }

    private void helper(TreeNode root, int k1, int k2, ArrayList<Integer> result) {
        if (root == null) return;

        // in-order binary tree iteration
        helper(root.left, k1, k2, result);
        if (k1 <= root.val && root.val <= k2) {
            result.add(root.val);
        }
        helper(root.right, k1, k2, result);
    }
} 
```

### 源码分析

以上为题解思路的简易实现，可以优化的地方为「剪枝过程」的处理——不递归遍历不可能有解的节点。优化后的`inorder_dfs`如下：

```
 void inorder_dfs(vector<int> &ret, TreeNode *root, int k1, int k2) {
        if (NULL == root) {
            return;
        }

        if ((NULL != root->left) && (root->val > k1)) {
            inorder_dfs(ret, root->left, k1, k2);
        } // cut-off for left sub tree

        if ((root->val >= k1) && (root->val <= k2)) {
            ret.push_back(root->val);
        } // add valid value

        if ((NULL != root->right) && (root->val < k2)) {
            inorder_dfs(ret, root->right, k1, k2);
        } // cut-off for right sub tree
    } 
```

> **Warning** 「剪枝」的判断条件容易出错，应将当前节点的值与`k1`和`k2`进行比较而不是其左子节点或右子节点的值。

## Convert Sorted Array to Binary Search Tree

## Question

*   leetcode: [Convert Sorted Array to Binary Search Tree | LeetCode OJ](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)
*   lintcode: [(177) Convert Sorted Array to Binary Search Tree With Minimal Height](http://www.lintcode.com/en/problem/convert-sorted-array-to-binary-search-tree-with-minimal-height/)

```
Given an array where elements are sorted in ascending order,
convert it to a height balanced BST.

Given a sorted (increasing order) array,
Convert it to create a binary tree with minimal height.

Example
Given [1,2,3,4,5,6,7], return

     4
   /   \
  2     6
 / \    / \
1   3  5   7
Note
There may exist multiple valid solutions, return any of them. 
```

## 题解 - 折半取中

将二叉搜索树按中序遍历即可得升序 key 这个容易实现，但反过来由升序 key 逆推生成二叉搜索树呢？按照二叉搜索树的定义我们可以将较大的 key 链接到前一个树的最右侧节点，这种方法实现极其简单，但是无法达到本题「树高平衡-左右子树的高度差绝对值不超过 1」的要求，因此只能另辟蹊径以达到「平衡二叉搜索树」的要求。

要达到「平衡二叉搜索树」这个条件，我们首先应从「平衡二叉搜索树」的特性入手。简单起见，我们先考虑下特殊的满二叉搜索树，满二叉搜索树的一个重要特征就是各根节点的 key 不小于左子树的 key ，而小于右子树的所有 key；另一个则是左右子树数目均相等，那么我们只要能将所给升序序列分成一大一小的左右两半部分即可满足题目要求。又由于此题所给的链表结构中仅有左右子树的链接而无指向根节点的链接，故我们只能从中间的根节点进行分析逐层往下递推直至取完数组中所有 key, 数组中间的索引自然就成为了根节点。由于 OJ 上方法入口参数仅有升序序列，方便起见我们可以另写一私有方法，加入`start`和`end`两个参数，至此递归模型初步建立。

### C++

```
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode *sortedArrayToBST(vector<int> &num) {
        if (num.empty()) {
            return NULL;
        }

        return middleNode(num, 0, num.size() - 1);
    }

private:
    TreeNode *middleNode(vector<int> &num, const int start, const int end) {
        if (start > end) {
            return NULL;
        }

        TreeNode *root = new TreeNode(num[start + (end - start) / 2]);
        root->left = middleNode(num, start, start + (end - start) / 2 - 1);
        root->right = middleNode(num, start + (end - start) / 2 + 1, end);

        return root;
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
     * @param A: an integer array
     * @return: a tree node
     */
    public TreeNode sortedArrayToBST(int[] A) {
        if (A == null || A.length == 0) return null;

        return helper(A, 0, A.length - 1);
    }

    private TreeNode helper(int[] nums, int start, int end) {
        if (start > end) return null;

        int mid = start + (end - start) / 2;
        TreeNode root = new TreeNode(nums[mid]);
        root.left = helper(nums, start, mid - 1);
        root.right = helper(nums, mid + 1, end);

        return root;
    }
} 
```

### 源码分析

从题解的分析中可以看出中间根节点的建立至关重要！由于数组是可以进行随机访问的，故可取数组中间的索引为根节点，左右子树节点可递归求解。虽然这种递归的过程和「二分搜索」的模板非常像，但是切记本题中根据所给升序序列建立平衡二叉搜索树的过程中需要做到**不重不漏**，故边界处理需要异常小心，不能再套用`start + 1 < end`的模板了。

### 复杂度分析

递归调用`middleNode`方法时每个`key`被访问一次，故时间复杂度可近似认为是 $$O(n)$$.

## Reference

*   [Convert Sorted Array to Binary Search Tree | 九章算法](http://www.jiuzhang.com/solutions/convert-sorted-array-to-binary-search-tree/)

## Convert Sorted List to Binary Search Tree

## Question

*   leetcode - [Convert Sorted List to Binary Search Tree | LeetCode OJ](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/)
*   lintcode - [(106) Convert Sorted List to Binary Search Tree](http://www.lintcode.com/en/problem/convert-sorted-list-to-binary-search-tree/)

```
Given a singly linked list where elements are sorted in ascending order,
convert it to a height balanced BST. 
```

## 题解 - 折半取中

题 [Convert Sorted Array to Binary Search Tree | Data Structure and Algorithm](http://algorithm.yuanbin.me/zh-hans/binary_search_tree/convert_sorted_array_to_binary_search_tree.html) 的升级版，不过这里把「有序数组」换成了「有序链表」。我们可以参考上题的题解思路，思考如何才能在链表中找到「中间节点」。对于本题的单向链表来说，要想知道中间位置的节点，则必须需要知道链表的长度，因此我们就自然联想到了可以通过遍历链表来求得其长度。求得长度我们就知道了链表中间位置节点的索引了，进而根据头节点和当前节点则可将链表分为左右两半形成递归模型。到这里还只能算是解决了问题的一半，这道题另一比较麻烦的地方在于边界条件的取舍，很难第一次就 AC, 下面结合代码做进一步的分析。

### C++

```
/**
 * Definition of ListNode
 * class ListNode {
 * public:
 *     int val;
 *     ListNode *next;
 *     ListNode(int val) {
 *         this->val = val;
 *         this->next = NULL;
 *     }
 * }
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
     * @param head: The first node of linked list.
     * @return: a tree node
     */
    TreeNode *sortedListToBST(ListNode *head) {
        if (NULL == head) {
            return NULL;
        }

        // get the size of List
        ListNode *node = head;
        int len = 0;
        while (NULL != node) {
            node = node->next;
            ++len;
        }

        return buildBSTHelper(head, len);
    }

private:
    TreeNode *buildBSTHelper(ListNode *head, int length) {
        if (NULL == head || length <= 0) {
            return NULL;
        }

        // get the middle ListNode as root TreeNode
        ListNode *lnode = head;
        int count = 0;
        while (count < length / 2) {
            lnode = lnode->next;
            ++count;
        }

        TreeNode *root = new TreeNode(lnode->val);
        root->left = buildBSTHelper(head, length / 2);
        root->right = buildBSTHelper(lnode->next, length - 1 - length / 2);

        return root;
    }
}; 
```

### 源码分析

1.  异常处理。
2.  获取链表长度。
3.  `buildBSTHelper`输入参数为表头节点地址以及相应的链表长度，递归获取根节点、左节点和右节点。

其中`buildBSTHelper`的边界处理很有技巧，首先是递推的终止条件，头节点为`NULL`时显然应该返回`NULL`. 但`length`的终止条件又如何确定？拿不定主意时就用几个简单例子来试试，比如`1`, `1->2`, `1->2->3`.

先来分析下给`buildBSTHelper`传入的`length`的含义——从表头节点`head`开始往后递推长度为`length`的链表。故`length`为 0 时表示不访问链表中的任一节点，也就是说应该返回`NULL`.

再来分析链表的中间位置如何确定，我们引入计数器`count`来表示**目前需要遍历`count`个链表节点数目**才能得到中间位置的节点。看看四种不同链表长度下的表现。

1.  链表长度为 1 时，中间位置即为自身，计数器的值为 0.
2.  链表长度为 2 时，中间位置可选第一个节点，也可选第二个节点，相应的计数器值为 0 或 1.
3.  链表长度为 3 时，中间位置为第二个节点，相应的计数器应为 1，表示从表头节点往后递推一个节点。
4.  链表长度为 4 时，... 计数器的值为 1 或者 2.

从以上四种情况我们可以推断出`count`的值可取为`length / 2`或者`length / 2 + 1`, 简单起见我们先取`length / 2`试试，对应的边界条件即为`count < length / 2`, `count`初始值为 0\. 经过`count`次迭代后，目前`lnode`即为所需的链表中间节点，取出其值初始化为`TreeNode`的根节点。

确定根节点后还需要做的事情就是左子树和右子树中链表头和链表长度的取舍。首先来看看左子树根节点的确定，**`count`的含义为到达中间节点前遍历过的链表节点数目，那么从另一方面来说它就是前半部分链表的长度！**故将此长度`length / 2`作为得到左子树根节点所需的链表长度参数。除掉链表前半部分节点和中间位置节点这两部分外，剩下的链表长度即为`length - 1 - length / 2`.

> **Warning** `length - 1 - length / 2 != length / 2 - 1`

有没有觉得可以进一步化简为`length / 2 - 1`? 我首先也是这么做的，后来发现一直遇到`TERMSIG= 11`错误信息，这种错误一般是指针乱指或者指针未初始化就去访问。但自己仔细检查后发现并没有这种错误，于是乎在本地做单元测试，发现原来是死循环造成了栈空间溢出(猜的)！也就是说边界条件有问题！可自己的分析明明是没啥问题的啊...

在这种情况下我默默地打开了九章的参考代码，发现他们竟然没有用`length / 2 - 1`，而是`length - 1 - length / 2`. 立马意识到这两者可能并不相等。用错误数据试了下，长度为 1 或者 3 时两者即不相等。知道对于整型数来说，`1 / 2`为 0，但是却没能活学活用，血泪的教训。:-( 一个美好的下午就没了。

在测试出错的时候，还是要相信测试数据的力量，而不是凭自己以前认为对的方式去解决问题。

### 复杂度分析

首先遍历链表得到链表长度，复杂度为 $$O(n)$$. 递归遍历链表时，每个链表节点被访问一次，故时间复杂度为 $$O(n)$$, 两者加起来总的时间复杂度仍为 $$O(n)$$.

### 进一步简化代码

```
class Solution {
public:
    TreeNode *sortedListToBST(ListNode *head) {
        int length = 0;
        ListNode *curr = head;
        while (curr != NULL) {
            curr = curr->next;
            ++length;
        }
        return helper(head, length);
    }
private:
    TreeNode *helper(ListNode *&pos, int length) {
        if (length <= 0) {
            return NULL;
        }

        TreeNode *left = helper(pos, length / 2);
        TreeNode *root = new TreeNode(pos->val); // the sequence cannot be changed!
                                                 // this is important difference of the solution above
        pos = pos->next;
        root->left = left;
        root->right = helper(pos, length - length / 2 - 1);
        return root;
    }
}; 
```

### 源码分析

1.  可以进一步简化 helper 函数代码，注意参数的接口设计。
2.  即是把传入的链表指针向前递进 n 步，并返回经过的链表节点转化成的二分查找树的根节点。
3.  注意注释中的那两句实现，`new root` 和 `new left` 不可调换顺序。这才是精简的要点。但是这种方法不如上面的分治法容易理解。

### O(nlogn) 的实现，避免 length 边界

```
/**
 * Definition for ListNode.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int val) {
 *         this.val = val;
 *         this.next = null;
 *     }
 * }
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
     * @param head: The first node of linked list.
     * @return: a tree node
     */
    public TreeNode sortedListToBST(ListNode head) {
        if (head == null) {
            return null;
        }
        return helper(head);
    } 

    private TreeNode helper(ListNode head) {
        if (head == null) {
            return null;
        }
        if (head.next == null) {
            return new TreeNode(head.val);
        }

        ListNode pre = null;
        ListNode slow = head, fast = head;

        while (fast != null && fast.next != null) {
            pre = slow;
            slow = slow.next;
            fast = fast.next.next;
        }
        pre.next = null;

        TreeNode root = new TreeNode(slow.val);
        TreeNode L = helper(head);
        TreeNode R = helper(slow.next);
        root.left = L;
        root.right = R;

        return root;
    } 
} 
```

### 源码分析

1.  如果想避免上述 length 边界搞错的问题，可以使用分治法遍历树求中点的方法。
2.  但这种时间复杂度是 $$O(nlogn)$$，性能上还是比 $$O(n)$$ 差一点。

## Reference

*   [Convert Sorted List to Binary Search Tree | 九章算法](http://www.jiuzhang.com/solutions/convert-sorted-list-to-binary-search-tree/)

## Binary Search Tree Iterator

## Question

*   lintcode: [(86) Binary Search Tree Iterator](http://www.lintcode.com/en/problem/binary-search-tree-iterator/) <i class="fa fa-star"></i><i class="fa fa-star"></i>

```
Design an iterator over a binary search tree with the following rules:

- Elements are visited in ascending order (i.e. an in-order traversal)
- next() and hasNext() queries run in O(1) time in average.

Example
For the following binary search tree, in-order traversal by using iterator is [1, 6, 10, 11, 12]

           10
         /    \
        1      11
         \       \
            6       12

Challenge
Extra memory usage O(h), h is the height of the tree.

Super Star: Extra memory usage O(1) 
```

## 题解 - 中序遍历

仍然考的是中序遍历，但是是非递归实现。其实这道题等价于写一个二叉树中序遍历的迭代器。需要内置一个栈，一开始先存储到最左叶子节点的路径。在遍历的过程中，只要当前节点存在右子树，则进入右子树，存储从此处开始到当前子树里最左叶子节点的路径。

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
 * Example of iterate a tree:
 * BSTIterator iterator = BSTIterator(root);
 * while (iterator.hasNext()) {
 *    TreeNode * node = iterator.next();
 *    do something for node
 */
    class BSTIterator {
    private:
        stack<TreeNode*> stack_;
        TreeNode* cur_ = NULL;

    public:
        //@param root: The root of binary tree.
        BSTIterator(TreeNode *root) {
            // write your code here
            cur_ = root;
        }

        //@return: True if there has next node, or false
        bool hasNext() {
            // write your code here
            return (cur_ || !stack_.empty());
        }

        //@return: return next node
        TreeNode* next() {
            // write your code here
            while (cur_) {
                stack_.push(cur_);
                cur_ = cur_->left;
            }
            cur_ = stack_.top();
            stack_.pop();
            TreeNode* node = cur_;
            cur_ = cur_->right;

            return node;
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
 * Example of iterate a tree:
 * Solution iterator = new Solution(root);
 * while (iterator.hasNext()) {
 *    TreeNode node = iterator.next();
 *    do something for node
 * } 
 */
public class Solution {
    private Stack<TreeNode> stack = new Stack<>();
    private TreeNode curt;

    // @param root: The root of binary tree.
    public Solution(TreeNode root) {
        curt = root;
    }

    //@return: True if there has next node, or false
    public boolean hasNext() {
        return (curt != null || !stack.isEmpty()); //important to judge curt != null
    }

    //@return: return next node
    public TreeNode next() {
        while (curt != null) {
            stack.push(curt);
            curt = curt.left;
        }

        curt = stack.pop();
        TreeNode node = curt;
        curt = curt.right;

        return node;
    }
} 
```

### 源码分析

1.  这里容易出错的是 `hasNext()` 函数中的判断语句，不能漏掉 `curt != null`。
2.  如果是 leetcode 上的原题，由于接口不同，则不需要维护 current 指针。