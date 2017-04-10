title: Leetcode 题解 - 107. Binary Tree Level Order Traversal II
date: 2016-10-13 17:25
categories: Leetcode
---

#题目

给定一棵二叉树，对于节点值，返回*自底向上的层次顺序（bottom-up level order）*的遍历结果（即是，从左到右，从叶子到根节点，一层接一层）。

<!-- more -->

**示例**：

给定二叉树 [3, 9, 20, null, null, 15, 7]，

        3
       / \
      9  20
        /  \
       15   7

返回自底向上层次顺序的遍历结果：

    [
      [15,7],
      [9,20],
      [3]
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
    vector<vector<int>> levelOrderBottom(TreeNode *root) {
        
    }
};
```

从示例的返回来看，跟[广度优先搜索（Breadth-First-Search）](https://zh.wikipedia.org/wiki/%E5%B9%BF%E5%BA%A6%E4%BC%98%E5%85%88%E6%90%9C%E7%B4%A2)（以下简称 BFS）遍历二叉树的结果很像。那么我的思路是，先用 BFS 遍历，再看如何修改 BFS 以满足题目要求。

BFS 代码如下：

```cpp
#include <frequently-used-code-snippets.h>
using namespace std;

void BFS(TreeNode *root) {
    vector<TreeNode *> queue;
    queue.push_back(root);
    int index = 0;
    while (index < queue.size()) {
        TreeNode *node = queue[index];
        index++;
        cout << node->val << ' ';

        if (node->left != NULL) {
            queue.push_back(node->left);
        }
        if (node->right != NULL) {
            queue.push_back(node->right);
        }
    }
}

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

    BFS(&node3);
}

// 输出结果：
// 3 9 20 15 7
```

接下来的思路是，先基于 queue 构造出一个 qLevel。如 queue = [3, 9, 20, null, null, 15, 7]，那么 qLevel = [1, 2, 2, null, null, 3, 3]，然后再基于 queue 和 qLevel 构造出 `vector<vector<int>>`。

伪代码为：

```
vector<vector<int>> levelOrderBottom(TreeNode *root) {
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

    // 反转 vv
    vector<vector<int>> reverseVv;
    for (int i = vv.size() - 1; i >= 0; i--) {
        reverseVv.push_back(vv[i]);
    }

    return reverseVv;
}
```

代码如下：

```cpp
#include <frequently-used-code-snippets.h>
using namespace std;

class Solution {
public:
    vector<vector<int>> levelOrderBottom(TreeNode *root) {
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

        // 反转 vv
        vector<vector<int>> reverseVv;
        for (int i = vv.size() - 1; i >= 0; i--) {
            reverseVv.push_back(vv[i]);
        }

        return reverseVv;
    }

    void BFS(TreeNode *root) {
        vector<TreeNode *> queue;
        queue.push_back(root);
        int index = 0;
        while (index < queue.size()) {
            TreeNode *node = queue[index];
            index++;
            cout << node->val << ' ';

            if (node->left != NULL) {
                queue.push_back(node->left);
            }
            if (node->right != NULL) {
                queue.push_back(node->right);
            }
        }
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
    vector<vector<int>> vv = sol.levelOrderBottom(&node3);
}

// 输出结果：
// [[15,7],[9,20],[3]]
```

提交到 Leetcode，Accepted! :) 运行时间为 6ms。
