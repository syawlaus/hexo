title: Leetcode 题解 - 119. Pascal's Triangle II
date: 2016-10-26 00:40
categories: Leetcode
---

#题目

给定一个下标 k，返回[帕斯卡三角](https://zh.wikipedia.org/wiki/%E6%9D%A8%E8%BE%89%E4%B8%89%E8%A7%92%E5%BD%A2)（即杨辉三角）的第 k 行。

<!-- more -->

**示例**：

给定 k = 3，返回 [1, 3, 3, 1]。

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        
    }
};
```

思路很简单，使用 [Leetcode 题解 - 118. Pascal's Triangle](http://syawlaus.github.io/blog/leetcode/118-pascals-triangle/) 的 `vector<vector<int>> generate(int numRows)` 函数生成 rowIndex + 1 行的杨辉三角，然后返回第 rowIndex 行即可。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<vector<int>> triangle = generate(rowIndex + 1);
        return triangle[rowIndex];
    }

private:
    vector<vector<int>> generate(int numRows) {
        // 生成杨辉三角，全部元素都为 1
        vector<vector<int>> triangle;
        for (int row = 0; row < numRows; row++) {
            vector<int> rowVec;
            triangle.push_back(rowVec);
            for (int col = 0; col <= row; col++) {
                triangle[row].push_back(1);
            }
        }

        // 对于非边缘的元素，计算其值
        for (int row = 2; row < numRows; row++) {
            for (int col = 1; col < row; col++) {
                triangle[row][col] = triangle[row - 1][col - 1] + triangle[row - 1][col];
            }
        }

        // 输出杨辉三角
        return triangle;
    }
};

int main() {
    // 生成杨辉三角
    Solution sol;
    printVector(sol.getRow(0));
    printVector(sol.getRow(1));
    printVector(sol.getRow(2));
    printVector(sol.getRow(3));
    printVector(sol.getRow(4));
    printVector(sol.getRow(5));
}

// 输出结果：
// 1
// 1 1
// 1 2 1
// 1 3 3 1
// 1 4 6 4 1
// 1 5 10 10 5 1
```

提交到 Leetcode，Accepted! :) 运行时间为 3ms。
