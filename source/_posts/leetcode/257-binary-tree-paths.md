title: Leetcode 题解 - 257. Binary Tree Paths
date: 2016-10-26 17:00
categories: Leetcode
---

#题目

给定一棵二叉树，返回所有「根节点到叶子节点」的路径。

<!-- more -->

**示例**：

给定下面的二叉树：

       1
     /   \
    2     3
     \
      5

所有「根节点到叶子节点」的路径为：

    ["1->2->5", "1->3"]

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
    vector<string> binaryTreePaths(TreeNode *root) {
        
    }
};
```

思路是使用[深度优先搜索](https://zh.wikipedia.org/zh-hans/%E6%B7%B1%E5%BA%A6%E4%BC%98%E5%85%88%E6%90%9C%E7%B4%A2)（DFS）的[先序遍历](https://zh.wikipedia.org/wiki/%E6%A0%91%E7%9A%84%E9%81%8D%E5%8E%86#.E5.85.88.E5.BA.8F.E9.81.8D.E5.8E.86.28Pre-Order_Traversal.29)。用一个 struct 把 TreeNode 及其 ParentNode 封装起来。用 DFS 填充各个 ParentNode。然后从各个叶子节点开始，往上回溯到根节点，'每次回溯就能得出一条「根节点到叶子节点的路径」。但 binaryTreePaths 接收的是 TreeNode 数据，暂时想不到什么办法通过 TreeNode 获取到其对应的 ParentNode，所以这个思路的实现先搁置。

另一个思路是，利用二叉树的递归结构。对于 root 来说，假设已经有了左右子树的两个路径 `vector<string>`，以示例的二叉树来说，左子树的路径 `vector<string> = {"2->5"}`，右子树的路径 `vector<string> = {"3"}`，那么在两个路径 `vector<string>` 中，添加上 root 即可。如果 root 的左右子节点为 NULL，那么 `vector<string> = { root->val }`。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    vector<string> binaryTreePaths(TreeNode *root) {
        vector<string> paths;
        if (root == NULL) {
            return paths;
        }

        if (root->left == NULL && root->right == NULL) {
            paths.push_back(to_string(root->val));
        }

        // 处理左子树
        vector<string> leftPaths;
        if (root->left != NULL) {
            leftPaths = binaryTreePaths(root->left);
        }
        for (string leftPath : leftPaths) {
            string path = to_string(root->val) + "->" + leftPath;
            paths.push_back(path);
        }

        // 处理右子树
        vector<string> rightPaths;
        if (root->right != NULL) {
            rightPaths = binaryTreePaths(root->right);
        }
        for (string rightPath : rightPaths) {
            string path = to_string(root->val) + "->" + rightPath;
            paths.push_back(path);
        }

        return paths;
    }
};

int main() {
    TreeNode A(1);
    TreeNode B(2);
    TreeNode C(3);
    TreeNode D(5);

    A.left = &B;
    A.right = &C;
    B.right = &D;

    Solution sol;
    vector<string> paths = sol.binaryTreePaths(&A);
    printVector(paths);
}

// 输出结果：
// 1->2->5 1->3
```

把 Solution 提交到 Leetcode，Accepted! :) 运行时间为 3ms。
