title: Leetcode 题解 - 112. Path Sum
date: 2016-10-28 22:50
categories: Leetcode
---

#题目

给定一棵二叉树以及一个和值 sum，判断二叉树是否存在一条根到叶子节点的路径，该路径上所有节点值的和与 sum 相等。

<!-- more -->

**示例**：

给定下面的二叉树和 sum = 22，

          5
         / \
        4   8
       /   / \
      11  13  4
     /  \      \
    7    2      1

返回 true，因为存在一条根到叶子节点的路径 5 -> 4 -> 11 -> 2，所有节点值之和等于 22。

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
    bool hasPathSum(TreeNode* root, int sum) {
        
    }
};
```

思路是递归，以示例的二叉树为例，要判断是否存在一条根到叶子节点的路径使得节点和为 22，对于左右子树来说，需要判断是否存在一条以左右子节点（即 4、8）为根节点到叶子节点的路径，使得节点和为 22－5＝17。这样可以递归判断下去，直到 root 为叶子节点，这时判断 root->val 是否等于 sum。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    bool hasPathSum(TreeNode *root, int sum) {
        if (root == NULL) {
            return false;
        }

        // 如果 root 是叶子节点
        if (root->left == NULL && root->right == NULL) {
            return root->val == sum;
        }

        // 判断左子树
        if (root->left != NULL) {
            bool hasLeftPathSum = hasPathSum(root->left, sum - root->val);
            if (hasLeftPathSum) {
                return true;
            }
        }

        // 判断右子树
        if (root->right != NULL) {
            bool hasRightPathSum = hasPathSum(root->right, sum - root->val);
            if (hasRightPathSum) {
                return true;
            }
        }

        return false;
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
    cout << sol.hasPathSum(&A, 27) << endl;
    cout << sol.hasPathSum(&A, 22) << endl;
    cout << sol.hasPathSum(&A, 26) << endl;
    cout << sol.hasPathSum(&A, 18) << endl;
    cout << sol.hasPathSum(&A, 17) << endl;
    cout << sol.hasPathSum(&A, 19) << endl;
}

// 输出结果：
// 1
// 1
// 1
// 1
// 0
// 0
```

提交到 Leetcode，Accepted! 运行时间为 19ms。
