title: Leetcode 题解 - 111. Minimum Depth of Binary Tree
date: 2016-10-29 15:25
categories: Leetcode
---

#题目

给定一棵二叉树，找出它的最小高度。

最小高度即是从根节点到叶子节点的最短路径上的节点数目。

<!-- more -->

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
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
    int minDepth(TreeNode *root) {
        
    }
};
```

思路是递归：

* 如果 root 是空节点，返回 0。
* 如果 root 是叶子节点，返回 1。
* 如果只有左子树，则返回 leftMinDepth + 1。
* 如果只有右子树，则返回 rightMinDepth + 1。
* 如果有左右子树，则返回 min(leftMinDepth, rightMinDepth) + 1。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

#define NOT_LEAF_NODE -1

// 求两个数的较小值
#define MIN_TWO(a, b) (a < b ? a : b)

bool isLeafNode(TreeNode *node) {
    return (node->left == NULL && node->right == NULL);
}

class Solution {
public:
    int minDepth(TreeNode* root) {
        if (root == NULL) {
            return 0;
        }

        if (isLeafNode(root)) {
            return 1;
        }

        // 处理左子树
        int leftMinDepth = NOT_LEAF_NODE;   // 防止把只有左子节点的二叉树的 minDepth 判断为 1（因为右子树 minDepth 为 0）
        if (root->left != NULL) {
            leftMinDepth = minDepth(root->left);
        }

        // 处理右子树
        int rightMinDepth = NOT_LEAF_NODE;  // 防止把只有右子节点的二叉树的 minDepth 判断为 1（因为左子树 minDepth 为 0）
        if (root->right != NULL) {
            rightMinDepth = minDepth(root->right);
        }

        // 返回：root 只有左子树
        if (leftMinDepth != NOT_LEAF_NODE && rightMinDepth == NOT_LEAF_NODE) {
            return leftMinDepth + 1;
        }
        // 返回：root 只有右子树
        if (leftMinDepth == NOT_LEAF_NODE && rightMinDepth != NOT_LEAF_NODE) {
            return rightMinDepth + 1;
        }
        // 返回：root 有左右子树
        else {      // 即 leftMinDepth != NOT_LEAF_NODE && rightMinDepth != NOT_LEAF_NODE
            return MIN_TWO(leftMinDepth, rightMinDepth) + 1;
        }
    }
};

int main() {
    TreeNode A(5);
    TreeNode B(4);
    TreeNode C(8);
    TreeNode D(11);
    TreeNode E(13);
    TreeNode F(4);
    TreeNode G(7);
    TreeNode H(2);
    TreeNode I(1);

    A.left = &B;
    A.right = &C;
    B.left = &D;
    C.left = &E;
    C.right = &F;
    D.left = &G;
    D.right = &H;
    F.right = &I;

    Solution sol;
    cout << sol.minDepth(&A) << endl;
}

// 输出结果：
// 3
```

需要注意代码的第 24、30 行，leftMinDepth 和 rightMinDepth 的初始值是 -1，而不是 0。因为只有一个子节点的节点不是叶子节点，如果初始值为 0，那么表示该节点被错误判断为叶子节点，导致 minDepth 计算错误。

提交到 Leetcode，Accepted! 运行时间为 12ms。
