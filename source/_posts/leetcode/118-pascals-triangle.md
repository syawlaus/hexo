title: Leetcode 题解 - 118. Pascal's Triangle
date: 2016-10-21 23:35
categories: Leetcode
---

#题目

给定 numRows，返回前 numRows 行的[帕斯卡三角](https://zh.wikipedia.org/wiki/%E6%9D%A8%E8%BE%89%E4%B8%89%E8%A7%92%E5%BD%A2)（即杨辉三角）。

<!-- more -->

**示例**：

例如，给定 numRows = 5，返回

    [
         [1],
        [1,1],
       [1,2,1],
      [1,3,3,1],
     [1,4,6,4,1]
    ]

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        
    }
};
```

我们首先把杨辉三角用二维数组直观地表示出来：

    \ 数组下标      0    1    2    3    4    5    6
     +--------+
      杨辉三角 \
    
      第 0 行       1
      第 1 行       1    1
      第 2 行       1    2    1
      第 3 行       1    3    3    1
      第 4 行       1    4    6    4    1

可以看到：

* 第一、二行为 1
* 第一列、最后一列为 1
* 第 i 行有 i 个元素
* triangle[row][col] = triangle[row-1][col-1] + triangle[row-1][col]

代码如下：

```cpp
#include <iostream>
#include <vector>
#include <frequently-used-code-snippets.h>
using namespace std;

class Solution {
public:
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
    vector<vector<int>> triangle = sol.generate(5);
    
    // 打印杨辉三角
    for (int row = 0; row < triangle.size(); row++) {
        printVector(triangle[row]);
    }
}

// 输出结果：
// 1
// 1 1
// 1 2 1
// 1 3 3 1
// 1 4 6 4 1
```

提交到 Leetcode，Accepted! :) 运行时间为 0ms。
