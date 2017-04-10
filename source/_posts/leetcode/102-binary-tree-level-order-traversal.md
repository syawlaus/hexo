title: Leetcode 题解 - 102. Binary Tree Level Order Traversal
date: 2016-10-22 12:00
categories: Leetcode
---

#题目

给定一棵二叉树，对于节点值，返回*层次顺序（level order）*的遍历结果（即是，从左到右，一层接一层）。

<!-- more -->

**示例**：

给定二叉树 [3, 9, 20, null, null, 15, 7]，

        3
       / \
      9  20
        /  \
       15   7

返回层次顺序的遍历结果：

    [
      [3],
      [15, 7],
      [9, 20]
    ]

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
    vector<vector<int>> levelOrder(TreeNode *root) {
        
    }
};
```

这道题跟 [Leetcode 题解 - 107. Binary Tree Level Order Traversal II](http://syawlaus.github.io/blog/leetcode/107-binary-tree-level-order-traversal-ii/) 非常相像，只是层次顺序不是自底向上，而是自顶向下。

代码如下（只需要把 107 题解最后一步“反转 vv”去掉即可）：

```cpp
#include <frequently-used-code-snippets.h>
using namespace std;

class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode *root) {
        // 初始化
        vector<vector<int>> vv;
        if (root == NULL) {
            return vv;
        }

        vector<TreeNode *> queue;
        vector<int> qLevel;

        queue.push_back(root);
        qLevel.push_back(1);

        // 循环构造 queue 和 qLevel
        for (int i = 0; i < queue.size(); i++) {
            TreeNode *node = queue[i];
            if (node->left != NULL) {
                queue.push_back(node->left);
                qLevel.push_back(qLevel[i] + 1);
            }
            if (node->right != NULL) {
                queue.push_back(node->right);
                qLevel.push_back(qLevel[i] + 1);
            }
        }

        // 基于 queue 和 qLevel 正向构造 vv
        qLevel.insert(qLevel.begin(), 0);       // 头插入 sentinel 元素，方便统一循环
        for (int i = 0; i < queue.size(); i++) {
            if (qLevel[i] < qLevel[i + 1]) {    // 有 sentinel 元素后这个判断就可以统一
                vector<int> vNewLevel;
                vv.push_back(vNewLevel);
            }
            vv[vv.size() - 1].push_back(queue[i]->val);
        }

        return vv;
    }
};

int main() {
    // 设置二叉树
    TreeNode node3(3);
    TreeNode node9(9);
    TreeNode node20(20);
    TreeNode node15(15);
    TreeNode node7(7);

    node3.left = &node9;
    node3.right = &node20;

    node20.left = &node15;
    node20.right = &node7;

    // Solution
    Solution sol;
    vector<vector<int>> vv = sol.levelOrder(&node3);
}
```

把 Solution 提交到 Leetcode，Accepted! :) 运行时间为 9ms。
