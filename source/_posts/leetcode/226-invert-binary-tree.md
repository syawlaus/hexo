title: Leetcode 题解 - 226. Invert Binary Tree
date: 2016-06-28 18:00
categories: Leetcode
---

#题目

把下面的二叉树从

         4
       /   \
      2     7
     / \   / \
    1   3 6   9

反转为

         4
       /   \
      7     2
     / \   / \
    9   6 3   1

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
    TreeNode* invertTree(TreeNode* root) {

    }
};
```

从 invertTree 的函数签名看到，输入为二叉树根结点的指针，输出为反转后的二叉树的根结点指针。

容易想到递归的思路：要反转以 1 为根结点的二叉树，先反转以 2、3 为根结点的二叉树，再交换 2、3 两个子节点即可。伪代码为：

```
TreeNode* invertTree(TreeNode* root)
    if (root 为空结点)
        return NULL;

    if (root 为叶子结点）
        return root

    // 反转左子树
    TreeNode* left = invertTree(root->left)

    // 反转右子树
    TreeNode* right = invertTree(root->right)

    // 交换左右子结点
    TreeNode* temp = root->left
    root->left = root->right
    root->right = temp

    // 返回根结点
    return root
```

代码如下：

```cpp
#include <iostream>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;

    // constructor
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        // root 为空结点
        if (root == NULL) {
            return NULL;
        }

        // root 是叶子节点
        if (root->left == NULL &&
            root->right == NULL) {
            return root;
        }

        // 反转左子树
        TreeNode* left = invertTree(root->left);

        // 反转右子树
        TreeNode* right = invertTree(root->right);

        // 交换左右子结点
        TreeNode* temp = root->left;
        root->left = root->right;
        root->right = temp;

        // 返回根结点
        return root;
    }
};
```

把 Solution 提交到 Leetcode，Accepted! :) 运行时间为 0ms。
