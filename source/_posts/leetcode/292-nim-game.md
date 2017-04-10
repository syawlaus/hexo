title: Leetcode 题解 - 292. Nim Game
date: 2016-06-23 13:30
categories: Leetcode
---

#题目

你正在和朋友玩取物游戏：桌上有一堆石头，每人轮流取出 1～3 块石头。取出最后一块石头的人胜出，首先从你开始取。

你们两人都很聪明，对赢得游戏都有最优策略。编写一个函数，给定桌上石头堆的数量，判断你是否能胜出。

<!-- more -->

**示例**：

例如，桌上有 4 块石头，那么你无法胜出，因为不管你取 1～3 块石头，最后一块都会被你的朋友取出。

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
class Solution {
public:
    bool canWinNim(int n) {

    }
};

int main() {
    Solution sol;
    sol.canWinNim(4);
}
```

好像没什么思路，我先把问题具体化一下。把 N 设为 7，我会怎么玩？

    * 如果我取 1 块，剩下 6 块
        * 对方取 1 块，剩下 5 块
            * 我取 1 块，剩下 4 块
                * 对方取 1 块，剩下 3 块    // 赢
                * 对方取 2 块，剩下 2 块    // 赢
                * 对方取 3 块，剩下 1 块    // 赢
            * 我取 2 块，剩下 3 块          //   输
            * 我取 3 块，剩下 2 块          //   输
        * 对方取 2 块，剩下 4 块
            * 我取 1 块，剩下 3 块          //   输
            * 我取 2 块，剩下 2 块          //   输
            * 我取 3 块，剩下 1 块          //   输
        * 对方取 3 块，剩下 3 块            // 赢
    * ...

好像没什么思路，那么反过来想：如果我要赢，那么最后剩下的石头一定要是 1～3 块，我全部取走就赢了。

    我取（最后一次）        剩下 1～3 块
    对方取（倒数第二次）    取 1 块，即是取之前还剩下 2～4 块，因为取之前剩下 2～3 块时，对方全部取走就赢了，所以只能是剩下 4 块
                            取 2 块，即是取之前还剩下 3～5 块，因为取之前剩下 3 块时，对方全部取走就赢了，所以只能是剩下 5 块
                            取 3 块，即是取之前还剩下 4～6 块，我赢

好像也没什么思路。

玩这个游戏的最优策略是什么？比如当有 6 块石头时，我肯定不会取 3 块，因为取了 3 块剩下 3 块，对方全取走就赢了。而我取 2 块，就会剩下 4 块，那么不管对方取走 1～3 块，剩下 1～3 块，我全部取走就能赢。如果我取走 1 块，剩下 5 块，对方取走 1～3 块，剩下 2～4 块，我就不一定赢了（当对方再取走 1 块剩下 4 块时，我必输）。

似乎有点思路了：如果我本次取走石头后，下一次不管对方取走 1～3 块，都能剩下 1～3 块，那么我必赢。

再把所有 N 的取值列出来，看看能不能找到什么规律。

    轮到我取时      1～3                                    必赢
                    4       不管取 1～3，都剩下 1～3            必输
                    5       取 1，剩下 4                    必赢
                            取 2，剩下 3                        必输
                            取 3，剩下 2                        必输
                    6       取 1，剩下 5，回到 5
                            取 2，剩下 4                    必赢
                            取 3，剩下 3                    必赢
                    7       取 1，剩下 6，回到 6
                            取 2，剩下 5，回到 5
                            取 3，剩下 4                    必赢
                    8       取 1，剩下 7，回到 7
                            取 2，剩下 6，回到 6
                            取 3，剩下 5，回到 5

我们把必赢的取法过滤出来：

    石头数量    我的胜负    原因分析
        1       必赢
        2       必赢
        3       必赢
        4           必输    不管我取 1～3，都剩下 1～3，对方都必赢
        5       必赢        取 1
        6       必赢        取 2 或 3
        7       必赢        取 3
        8           必输    不管我取 1～3，都剩下 5～7，对方都必赢
        9       必赢        取 1，剩下 8，对方必输
        10      必赢        取 2，剩下 8，对方必输
        11      必赢        取 3，剩下 8，对方必输
        12          必输    不管我取 1～3，都剩下 9～11，对方都必赢

规律很明显了：

* N 整除 4，我必输
* N 不能整除 4，我必赢

代码如下：

```cpp
class Solution {
public:
    bool canWinNim(int n) {
        return n % 4 != 0;
    }
};
```

提交到 Leetcode，Accepted! :) 运行时间为 2 ms。