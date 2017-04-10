title: Leetcode 题解 - 110. Balanced Binary Tree
date: 2016-10-22 12:40
categories: Leetcode
---

#题目

给定一棵二叉树，判断其高度是否平衡。

对于这个问题，一棵高度为平衡的二叉树的定义是，每个节点的左右子树的高度差不大于 1。

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
    bool isBalanced(TreeNode *root) {
        
    }
};
```

思路就是递归，如果 root 的左右子树平衡，那么判断左右子树高度差是否不大于 1。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>
using namespace std;

class Solution {
public:
    bool isBalanced(TreeNode *root) {
        if (root == NULL) {
            return true;
        }

        // 判断左子树是否平衡
        bool isLeftBalanced = isBalanced(root->left);
        if (! isLeftBalanced) {
            return false;
        }

        // 判断右子树是否平衡
        bool isRightBalanced = isBalanced(root->right);
        if (! isRightBalanced) {
            return false;
        }

        // 左、右子树都平衡，判断两者高度差是否不大于 1
        int leftHeight = getHeight(root->left);
        int rightHeight = getHeight(root->right);

        return (abs(leftHeight - rightHeight) <= 1);
    }

private:
    int getHeight(TreeNode *root) {
        if (root == NULL) {
            return 0;
        }

        int leftHeight = getHeight(root->left);
        int rightHeight = getHeight(root->right);
        
        return MAX_TWO(leftHeight, rightHeight) + 1;
    }
};

int main() {
    TreeNode A(1);
    TreeNode B(2);
    TreeNode C(3);
    TreeNode D(4);
    TreeNode E(5);

    A.left = &B;
    A.right = &C;
    B.left = &D;
    D.left = &E;

    Solution sol;
    cout << sol.isBalanced(&A);
}

// 输出结果：
// 0
```

提交到 Leetcode，Accepted! 运行时间为 16ms。
