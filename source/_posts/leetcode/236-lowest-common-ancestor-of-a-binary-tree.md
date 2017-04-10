title: Leetcode 题解 - 236. Lowest Common Ancestor of a Binary Tree
date: 2016-07-21 17:40
categories: Leetcode
---

# 题目

给定一棵二叉树（Binary Tree），找出两个给定节点的最近公共祖先（Lowest Common Ancestor，简称 LCA）。

<!-- more -->

根据 [LCA 的维基百科定义](https://en.wikipedia.org/wiki/Lowest_common_ancestor)：“在一个树中同时拥有 v, w 作为后代的最深的节点，我们定义一个节点也是其自己的后代。”

            _______3______
           /              \
        ___5__          ___1__
       /      \        /      \
       6      _2       0       8
             /  \
             7   4

**示例**：

节点 5、1 的 LCA 是 3。

节点 5、4 的 LCA 是 5，因为根据定义，一个节点也可以是自己的后代。

**难度**：中等

**编程语言**：C++

---

# 分析

236 这道题跟 [235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/) 非常相像，235 的是二叉搜索树，236 是二叉树。

我在解决 235 时一开始没有留意二叉搜索树的节点值有序的属性，当成是一般二叉树来解决，代码跟 236 一模一样，[分析过程在此](http://syawlaus.github.io/blog/leetcode/235-lowest-common-ancestor-of-a-binary-search-tree/)，就不再赘述了。
