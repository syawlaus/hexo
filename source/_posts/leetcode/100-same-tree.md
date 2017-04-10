title: Leetcode 题解 - 100. Same Tree
date: 2016-06-29 16:20
categories: Leetcode
---

#题目

给定两棵二叉树，编写一个函数，判断它们是否相等。

如果两棵二叉树结构上一模一样，且每个结点值都相等，那么认定它们相等。

<!-- more -->

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```
#include <iostream>
using namespace std;

//Definition for a binary tree node.
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {

    }
};
```

又是简单的递归思路，先判断根结点值是否相等，相等则继续递归判断左子树和右子树是否相等，伪代码如下：

```
bool isSameTree(TreeNode* p, TreeNode* q) {

    if (p、q 都空)
        return true;

    // 结构不同
    if (p 非空 && q 空)
        return false;

    // 结构不同
    if (p 空 && q 非空)
        return false;

    if (p、q 都非空) {
        // 值不同
        if (p->val != q->val)
            return false;

        bool isSameLeft = isSameTree(p->left, q->left)
        bool isSameRight = isSameTree(p->right, q->right)

        if (isSameLeft && isSameRight)
            return true;
        return false;
    }
}
```

代码如下：

```cpp
#include <iostream>
using namespace std;

//Definition for a binary tree node.
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (p == NULL && q == NULL) {
            return true;
        }

        // 结构不同
        if (p != NULL && q == NULL) {
            return false;
        }

        // 结构不同
        if (p == NULL && q != NULL) {
            return false;
        }

        else {      // 即 p != NULL && q != NULL
            // 值不同
            if (p->val != q->val) {
                return false;
            }

            bool isSameLeft = isSameTree(p->left, q->left);
            bool isSameRight = isSameTree(p->right, q->right);

            if (isSameLeft && isSameRight) {
                return true;
            }
            return false;
        }
    }
};

int main() {

    // P 树
    //      1
    //     / \
    //   2     3
    //  / \   / \
    // 4   5 6   7

    TreeNode p1(1);
    TreeNode p2(2);
    TreeNode p3(3);
    TreeNode p4(4);
    TreeNode p5(5);
    TreeNode p6(6);
    TreeNode p7(7);

    p1.left  = &p2;
    p1.right = &p3;

    p2.left  = &p4;
    p2.right = &p5;

    p3.left  = &p6;
    p3.right = &p7;

    // Q 树
    //      1
    //     / \
    //   2     3
    //  / \   /
    // 4   5 6

    TreeNode q1(1);
    TreeNode q2(2);
    TreeNode q3(3);
    TreeNode q4(4);
    TreeNode q5(5);
    TreeNode q6(6);

    q1.left  = &q2;
    q1.right = &q3;

    q2.left  = &q4;
    q2.right = &q5;
    
    q3.left  = &q6;

    // 判断 P、Q 树是否相等
    Solution sol;
    bool isSame = sol.isSameTree(&p1, &q1);
    cout << isSame << endl;
}

// 输出结果：
// 0
```

把 Solution 提交到 Leetcode，Accepted! :) 运行时间为 0ms。
