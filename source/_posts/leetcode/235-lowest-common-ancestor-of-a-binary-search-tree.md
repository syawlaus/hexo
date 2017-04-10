title: Leetcode 题解 - 235. Lowest Common Ancestor of a Binary Search Tree
date: 2016-07-20 15:00
categories: Leetcode
---

# 题目

给定一棵二叉搜索树（Binary Search Tree，简称 BST），找出两个给定节点的最近公共祖先（Lowest Common Ancestor，简称 LCA）。

<!-- more -->

根据 [LCA 的维基百科定义](https://en.wikipedia.org/wiki/Lowest_common_ancestor)：“在一个树中同时拥有 v, w 作为后代的最深的节点，我们定义一个节点也是其自己的后代。”

            _______6______
           /              \
        ___2__          ___8__
       /      \        /      \
       0      _4       7       9
             /  \
             3   5

**示例**：

节点 2、8 的 LCA 是 6。

节点 2、4 的 LCA 是 2，因为根据定义，一个节点也可以是自己的后代。

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
// Definition for a binary tree node.
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* v, TreeNode* w) {

    }
};
```

我的思路是：

1. 如果 v, w 是相同，那么 LCA 就是 v，否则到第 2 步
2. 判断 v 是否 w 的祖先节点（或相反），具体做法是：
    2.1. 求出 v, w 的深度
    2.2. 假设求出的 v 深度为 2，w 深度为 4
    2.3. 沿 w 往上走 (4-2) 层，到达 w 的父节点的父节点 w'
    2.4. 此时 v, w' 深度相同，判断 v, w' 是否相同，如果是，那么 v, w 的 LCA 就是 v，否则到第 3 步
3. v, w' 的深度相同（这里假设 v 的深度比 w 小），v, w' 每往上走一步（即回溯到父节点），判断两个父节点是否相同，相同则返回，不相同则继续往上走，直到相同则返回。

伪代码如下：

```
TreeNode* lowestCommonAncestor(TreeNode* root,
                               TreeNode* v,
                               TreeNode* w) {
    if (v == NULL || w == NULL) {
        return NULL;
    }

    // 第 1 步
    if (v == w) {
        return v;
    }

    // 第 2 步
    // 第 2.1 步
    int depthV = depth(root, v);
    int depthW = depth(root, w);

    // 第 2.2 步
    TreeNode* highNode;     // highNode 是深度较小的节点，在树中的位置较高
    TreeNode* lowNode;      //  lowNode 是深度较大的节点，在树中的位置较低
    int depthDiff;

    if (depthV < depthW) {
        highNode = v;
        lowNode = w;
        depthDiff = depthW - depthV;
    }
    else {
        highNode = w;
        lowNode = v;
        depthDiff = depthV - depthW;
    }

    // 第 2.3 步
    traverseUpward(lowNode, depthDiff);

    // 第 2.4 步
    if (highNode == lowNode) {
        return highNode;
    }

    // 第 3 步
    while (highNode != lowNode) {
        traverseUpward(highNode, 1);
        traverseUpward(lowNode, 1);
    }
    return highNode;
}

// 返回 node 在树中的深度
int depth(TreeNode* root,
          TreeNode* node) {

}

// node 在树上往上回溯 n 层，保证 n > 0
void traverseUpward(TreeNode* node,
                    int n){

}
```

好了，接下来 depth 函数要怎么写？我的思路是，使用一个 `map<TreeNode*, TreeNode*>`，key 为节点，value 为父节点，保存每个节点的父结点，那么往上回溯就方便了。然后从 root 开始，把 left, right 的父节点保存到 map 中，一直往下递归。在生成 map 后，往上回溯就能求出 depth。因为每个节点只能有一个父节点，所以满足 map 一一映射的属性。

```
int depth(TreeNode* root,
          TreeNode* node,
          map<TreeNode*, TreeNode*>& mapParents) {
    // 生成 map
    fillMapParents(root, mapParents);

    // 利用 map 往上回溯，求出 node 的深度
    int depth = 1;  // root 深度为 1
    while (node != root) {
        node = mapParents.getVal(node);
        depth++;
    }
    return depth;
}

void fillMapParents(TreeNode* root,
                    map<TreeNode*, TreeNode*>& mapParents) {
    if (root == NULL) {
        return;
    }

    addToMap(root->left, root, mapParents);
    addToMap(root->right, root, mapParents);

    fillMapParents(root->left, mapParents);
    fillMapParents(root->right, mapParents);
}

void addToMap(TreeNode* keyNode,
              TreeNode* valNode,
              map<TreeNode*, TreeNode*>& map) {
    if (containsKey(key, map) {
        return;
    }
    map.put(keyNode, valNode);
}
```

好了， 接下来 traverseUpward 函数：

```
void traverseUpward(TreeNode* node,
                    int n,
                    map<TreeNode*, TreeNode*>& mapParents){
    for (int i = 0; i < n; i++) {
        node = map.getVal(node);
    }
}
```

代码如下：

```cpp
#include <iostream>
#include <map>
using namespace std;

// Definition for a binary tree node.
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class Solution {
public:
    // 求 v, w 在以 root 为根节点的二叉树的最近公共祖先
    TreeNode* lowestCommonAncestor(TreeNode* root,
                                   TreeNode* v,
                                   TreeNode* w) {
        if (v == NULL || w == NULL) {
            return NULL;
        }

        // 第 1 步
        if (v == w) {
            return v;
        }

        // 第 2 步
        map<TreeNode*, TreeNode*> mapParents;
        fillMapParents(root, mapParents);   // 填充 map，不在 depth 函数里填充，避免重复计算

        // 第 2.1 步
        int depthV = depth(root, v, mapParents);
        int depthW = depth(root, w, mapParents);

        // 第 2.2 步
        TreeNode* highNode;     // highNode 是深度较小的节点，在树中的位置较高
        TreeNode* lowNode;      //  lowNode 是深度较大的节点，在树中的位置较低
        int depthDiff;

        if (depthV < depthW) {
            highNode = v;
            lowNode = w;
            depthDiff = depthW - depthV;
        }
        else {
            highNode = w;
            lowNode = v;
            depthDiff = depthV - depthW;
        }

        // 第 2.3 步
        traverseUpward(lowNode, depthDiff, mapParents);

        // 第 2.4 步
        if (highNode == lowNode) {
            return highNode;
        }

        // 第 3 步
        while (highNode != lowNode) {
            traverseUpward(highNode, 1, mapParents);
            traverseUpward(lowNode, 1, mapParents);
        }
        return highNode;
    }

    // 填充 map，包含 root
    void fillMapParents(TreeNode* root,
                        map<TreeNode*, TreeNode*>& mapParents) {
        fillMapParentsWithoutRoot(root, mapParents);
        addToMap(root, NULL, mapParents);   // 给根节点加上父节点 NULL
    }

    // 遍历填充 map 没有包含 root
    void fillMapParentsWithoutRoot(TreeNode* root,
                                   map<TreeNode*, TreeNode*>& mapParents) {
        if (root == NULL) {
            return;
        }

        addToMap(root->left, root, mapParents);
        addToMap(root->right, root, mapParents);

        fillMapParentsWithoutRoot(root->left, mapParents);
        fillMapParentsWithoutRoot(root->right, mapParents);
    }

    // 往 mapParents 添加 pair<childNode, parentNode>
    // 保证 childNode 唯一
    void addToMap(TreeNode* keyNode,
                  TreeNode* valNode,
                  map<TreeNode*, TreeNode*>& mapParents) {
        if (keyNode == NULL) {
            return;
        }

        // 保证 key 唯一
        if (mapParents.find(keyNode) == mapParents.end()) {
            mapParents.insert(pair<TreeNode*, TreeNode*>(keyNode, valNode));
        }
    }

    // 利用 map 往上回溯，求出 node 的深度
    int depth(TreeNode* root,
              TreeNode* node,
              map<TreeNode*, TreeNode*>& mapParents) {
        int depth = 1;  // root 深度为 1
        while (node != root) {
            node = mapParents.find(node)->second;
            depth++;
        }
        return depth;
    }

    // 利用 map，node 往上回溯 n 层
    void traverseUpward(TreeNode*& node,    // 注意这里使用 TreeNode*& 指针引用，如果使用 TreeNode*，只操作了形参，实参是不变的
                        int n,
                        map<TreeNode*, TreeNode*>& mapParents){
        cout << node->val << " 往上 " << n << " 层到达 ";
        for (int i = 0; i < n; i++) {
            node = mapParents.find(node)->second;
        }
        cout << node->val << endl;
    }

    // 打印 mapParents
    void printMapParents(map<TreeNode*, TreeNode*>& mapParents) {
        for (map<TreeNode*, TreeNode*>::iterator iter = mapParents.begin(); iter != mapParents.end(); iter++) {
            TreeNode* node = iter->first;
            TreeNode* parent = iter->second;
            if (parent == NULL) {
                cout << node->val << " 的父节点是 NULL" << endl;
            }
            else {
                cout << node->val << " 的父节点是 " << parent->val << endl;
            }
        }
        cout << endl;
    }

    // 打印所有节点深度
    void printAllNodesDepth(map<TreeNode*, TreeNode*>& mapParents,
                            TreeNode* root) {
        for (map<TreeNode*, TreeNode*>::iterator iter = mapParents.begin(); iter != mapParents.end(); iter++) {
            TreeNode* node = iter->first;
            int dep = depth(root, node, mapParents);
            cout << node->val << " 的深度为 " << dep << endl;
        }
        cout << endl;
    }
};

int main() {
    // init data
    TreeNode n6(6);
    TreeNode n2(2);
    TreeNode n8(8);
    TreeNode n0(0);
    TreeNode n4(4);
    TreeNode n7(7);
    TreeNode n9(9);
    TreeNode n3(3);
    TreeNode n5(5);

    n6.left  = &n2;
    n6.right = &n8;

    n2.left  = &n0;
    n2.right = &n4;

    n8.left  = &n7;
    n8.right = &n9;

    n4.left  = &n3;
    n4.right = &n5;

    // 测试 fillMapParents
    Solution sol;
    map<TreeNode*, TreeNode*> mapParents;
    sol.fillMapParents(&n6, mapParents);
    sol.printMapParents(mapParents);

    // 测试 depth
    sol.printAllNodesDepth(mapParents, &n6);
    
    // 测试 traverseUpward
    TreeNode* tempNode = &n2;
    sol.traverseUpward(tempNode, 1, mapParents);
    
    // 测试 lowestCommonAncestor
    TreeNode* v = &n2;
    TreeNode* w = &n4;
    TreeNode* lca = sol.lowestCommonAncestor(&n6, v, w);
    cout << endl << v->val << ", " << w->val << " 的 LCA 是 " << lca->val << endl << endl;

    v = &n2;
    w = &n8;
    lca = sol.lowestCommonAncestor(&n6, v, w);
    cout << endl << v->val << ", " << w->val << " 的 LCA 是 " << lca->val << endl << endl;

    return 0;
}

// 输出结果：
// 5 的父节点是 4
// 3 的父节点是 4
// 9 的父节点是 8
// 7 的父节点是 8
// 4 的父节点是 2
// 0 的父节点是 2
// 8 的父节点是 6
// 2 的父节点是 6
// 6 的父节点是 NULL
// 
// 5 的深度为 4
// 3 的深度为 4
// 9 的深度为 3
// 7 的深度为 3
// 4 的深度为 3
// 0 的深度为 3
// 8 的深度为 2
// 2 的深度为 2
// 6 的深度为 1
// 
// 2 往上 1 层到达 6
// 4 往上 1 层到达 2
// 
// 2, 4 的 LCA 是 2
// 
// 2 往上 0 层到达 2
// 8 往上 1 层到达 6
// 2 往上 1 层到达 6
// 
// 2, 8 的 LCA 是 6
```

提交到 Leetcode，Accepted! :)，运行时间为 112ms。

---

# 后续

再重新看了一遍题目，才发现求二叉搜索树（BST）而不是二叉树（BT）的 LCA。我们看看[二叉搜索树的维基百科定义](https://zh.wikipedia.org/zh-cn/%E4%BA%8C%E5%85%83%E6%90%9C%E5%B0%8B%E6%A8%B9)：

> 1. 任意节点的左子树不空，则左子树上**所有**结点的值均小于它的根结点的值；
> 2. 任意节点的右子树不空，则右子树上**所有**结点的值均大于它的根结点的值；
> 3. 任意节点的左、右子树也分别为二叉查找树；
> 4. 没有键值相等的节点。

根据 BST 的定义，计算 LCA(v, w) 的算法就简单多了。LCA(v, w) 是从根节点开始（包括根节点）往下的第一个满足 node->val 在 [v->val, w->val] 区间的节点。

我们回看示例给出的 BST：

            _______6______
           /              \
        ___2__          ___8__
       /      \        /      \
       0      _4       7       9
             /  \
             3   5

## 示例一

3、5 的最近公共祖先是 4，再往上的公共祖先是 2、6（一直到根节点）。那我们分析一下整条公共祖先链 6 -> 2 -> 4。

* 以 6 为根节点，3、5 都是在左子树，左子树所有节点值都小于根节点值，不满足 node->val 在 [v->val, w->val] 区间
* 以 2 为根节点，3、5 都是在右子树，右子树所有节点值都大于根节点值，不满足 node->val 在 [v->val, w->val] 区间
* 以 4 为根节点，3、5 分别是左、右子树，满足 node->val 在 [v->val, w->val] 区间
* 所以 4 是 3、5 的 LCA

## 示例二

我们再看看 LCA(4, 7)，4、7 的最近公共祖先是 6，满足 node->val 在 [v->val, w->val] 区间。

## 示例三

我们再看看 LCA(2, 4)，2、4 的最近公共祖先是 2，再往上的公共祖先是 6（一直到根节点）。那我们分析一下整条公共祖先链 6 -> 2。

* 以 6 为根节点，2、4 都是在左子树，左子树所有节点值都小于根节点值，不满足 node->val 在 [v->val, w->val] 区间
* 以 2 为根节点，满足 node->val 在 [v->val, w->val] 区间

抽象一点来说，v, w 可以分为三种情况：

1. v == w，同一个节点，满足 v->val 和 w->val 在 [v->val, w->val] 区间
2. v 是 w 的父节点，满足 v->val 在 [v->val, w->val] 区间
   w 是 v 的父节点，满足 w->val 在 [v->val, w->val] 区间
3. v, w 分别在最近公共祖先 lca 的左、右子树中，满足 lca->val 在 [v->val, w->val] 区间
   * 从 lca 往上回溯，得到其父节点 lcaParent，那么 lca 就是 lcaParent 的左／右子节点。
   * 如果 lca 是 lcaParent 的左子节点，那么 lcaParent->val 大于 lca->val，也大于 v->val 和 w->val，不满足 lcaParent->val 在 [v->val, w->val] 区间，所以 lcaParent 不是 LCA(v, w)，lcaParent 再往上回溯同理。
   * 如果 lca 是 lcaParent 的右子节点，那么 lcaParent->val 小于 lca->val，也小于 v->val 和 w->val，不满足 lcaParent->val 在 [v->val, w->val] 区间，所以 lcaParent 不是 LCA(v, w)，lcaParent 再往上回溯同理。

## 代码

综上：LCA(v, w) 是从根节点开始（包括根节点）往下的第一个满足 node->val 在 [v->val, w->val] 区间的节点。

伪代码如下：

```
TreeNode* lowestCommonAncestor(TreeNode* root,
                               TreeNode* v,
                               TreeNode* w) {
    if (v == NULL || w == NULL) {
        return NULL;
    }

    // 第 1 步
    if (v == w) {
        return v;
    }

    // 第 2、3 步
    while (true) {
        int result = foo(root, v, w);
        if (result == 1) {
            root = root->left;
        }
        else if (result == 2) {
            root = root->right;
        }
        else {      // result == 3
            return root;
        }
    }
}

// 判断是下列哪种情况
int foo(TreeNode* root,
        TreeNode* v,
        TreeNode* w) {

    //  v, w 都在 root 左子树
    if (root->val > v->val &&
        root->val > w->val) {
        return 1;
    }

    // v, w 都在 root 右子树
    if (root->val < v->val &&
        root->val < w->val) {
        return 2;
    }

    // v, w 分别在 root 左、右子树
    return 3;
}
```

代码如下：

```cpp
#include <iostream>
using namespace std;

// Definition for a binary tree node.
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class Solution {
public:
    // 求 v, w 在以 root 为根节点的二叉搜索树的最近公共祖先
    TreeNode* lowestCommonAncestor(TreeNode* root,
                                   TreeNode* v,
                                   TreeNode* w) {
        if (v == NULL || w == NULL) {
            return NULL;
        }

        // 第 1 步
        if (v == w) {
            return v;
        }

        // 第 2、3 步
        while (true) {
            int result = subtree(root, v, w);
            if (result == TWO_LEFT) {
                root = root->left;
            }
            else if (result == TWO_RIGHT) {
                root = root->right;
            }
            else {      // result == ONE_LEFT_ONE_RIGHT
                return root;
            }
        }
    }

    // 判断 v, w 在 root 的哪棵子树下
    int subtree(TreeNode* root,
        TreeNode* v,
        TreeNode* w) {

        //  v, w 都在 root 左子树
        if (root->val > v->val &&
            root->val > w->val) {
            return TWO_LEFT;
        }

        // v, w 都在 root 右子树
        if (root->val < v->val &&
            root->val < w->val) {
            return TWO_RIGHT;
        }

        // v, w 分别在 root 左、右子树
        return ONE_LEFT_ONE_RIGHT;
    }

private:
    const int TWO_LEFT = 1;
    const int TWO_RIGHT = 2;
    const int ONE_LEFT_ONE_RIGHT = 3;
};

int main() {
    // init data
    TreeNode n6(6);
    TreeNode n2(2);
    TreeNode n8(8);
    TreeNode n0(0);
    TreeNode n4(4);
    TreeNode n7(7);
    TreeNode n9(9);
    TreeNode n3(3);
    TreeNode n5(5);

    n6.left  = &n2;
    n6.right = &n8;

    n2.left  = &n0;
    n2.right = &n4;

    n8.left  = &n7;
    n8.right = &n9;

    n4.left  = &n3;
    n4.right = &n5;

    // LCA
    Solution sol;
    TreeNode* root = &n6;
    TreeNode* v = &n2;
    TreeNode* w = &n4;
    TreeNode* lca = sol.lowestCommonAncestor(root, v, w);
    cout << v->val << ", " << w->val << " 的 LCA 是 " << lca->val << endl;

    v = &n2;
    w = &n8;
    lca = sol.lowestCommonAncestor(root, v, w);
    cout << v->val << ", " << w->val << " 的 LCA 是 " << lca->val << endl;

    return 0;
}

// 输出结果：
// 2, 4 的 LCA 是 2
// 2, 8 的 LCA 是 6
```

提交到 Leetcode，Accepted! :)，运行时间为 44ms。
