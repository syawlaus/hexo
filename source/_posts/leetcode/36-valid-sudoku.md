title: Leetcode 题解 - 36. Valid Sudoku
date: 2016-10-27 21:30
categories: Leetcode
---

#题目

根据[数独谜题 - 规则](http://sudoku.com.au/TheRules.aspx)，判断一个数独是否有效。

数独板可以填充一部分，剩下的空格子使用字符 `'.'` 填充。

![](/images/leetcode/36-valid-sudoku/partially-filled-sudoku.png)

<center>一个部分填充的有效数独。</center>

<!-- more -->

**注意**：

一个有效的数独板（部分填充）不一定有解，只需要判断已填充的格子是否有效。

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        
    }
};
```

根据题目给出的数独规则，我们知道：

* 每行 1~9 只出现一次。
* 每列 1~9 只出现一次。
* 每个九宫格 1~9 只出现一次。注意：数独盘分割为 9 个九宫格，而不是我一开始理解的 49 个。

按照题目的意思，我们的程序需要既能判断完全填充的数独是否有效，

![](/images/leetcode/36-valid-sudoku/fully-filled-sudoku.jpg)

也能判断部分填充的数独是否有效。而每行、每列、每个九宫格缺失的数字不用管，只要已填充数字满足数独规则即可。

![](/images/leetcode/36-valid-sudoku/partially-filled-sudoku.png)

我的思路是，使用一个 `map<char, bool>` 保存本次检查（行／列／九宫格）1～9 是否出现过，每次检查前清空 map。

代码如下：

```cpp
#include <iostream>
#include <vector>
#include <map>
using namespace std;

class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        int size = 9;
        map<char, bool> numMap;

        // 检查所有行
        for (int row = 0; row < size; row++) {
            // 检查单行
            numMap.clear();
            for (int col = 0; col < size; col++) {
                if (containsKeyInMap(numMap, board[row][col])) {    // 1~9 出现过两次
                    return false;
                }
                else {
                    addToMap(numMap, board[row][col], true);
                }
            }
        }

        // 检查所有列
        for (int col = 0; col < size; col++) {
            // 检查单列
            numMap.clear();
            for (int row = 0; row < size; row++) {
                if (containsKeyInMap(numMap, board[row][col])) {    // 1~9 出现过两次
                    return false;
                }
                else {
                    addToMap(numMap, board[row][col], true);
                }
            }
        }

        // 检查所有九宫格
        int squareSize = 3;
        // 0~6 行
        for (int row = 0; row < 7; row += 3) {
            // 0~6 列
            for (int col = 0; col < 7; col += 3) {
                // 检查单个九宫格
                numMap.clear();
                for (int squareRow = row; squareRow < row + squareSize; squareRow++) {
                    for (int squareCol = col; squareCol < col + squareSize; squareCol++) {
                        if (containsKeyInMap(numMap, board[squareRow][squareCol])) {     // 1~9 出现过两次
                            return false;
                        }
                        else {
                            addToMap(numMap, board[squareRow][squareCol], true);
                        }
                    }
                }
            }
        }
        return true;

    }

private:
    // 功能：添加 <key, val> 到 map
    // 参数：map, key, val
    // 返回：void
    void addToMap(map<char, bool>& mapp,
                  char key,
                  bool val) {
        if (key == NULL || key == '.') {
            return;
        }

        map<char, bool>::iterator iter = mapp.find(key);

        // 找不到 key，添加 <key, val>
        if (iter == mapp.end()) {
            mapp.insert(pair<char, bool>(key, val));
        }
        // 找到 key，更新 val
        else {
            (iter->second) = val;
        }
    }

    // 功能：判断 map 是否包含 key
    // 参数：map, key
    // 返回：bool
    bool containsKeyInMap(map<char, bool>& mapp,
                          char key) {
        return mapp.find(key) != mapp.end();
    }
};

int main() {
    vector<vector<char>> full_filled_board = {
        { '4', '6', '3', '7', '2', '8', '9', '5', '1' },
        { '2', '5', '9', '4', '6', '1', '7', '3', '8' },
        { '7', '8', '1', '3', '5', '9', '6', '4', '2' },
        { '5', '3', '2', '1', '9', '7', '4', '8', '6' },
        { '9', '1', '4', '6', '8', '2', '5', '7', '3' },
        { '6', '7', '8', '5', '4', '3', '1', '2', '9' },
        { '8', '2', '6', '9', '7', '5', '3', '1', '4' },
        { '1', '4', '7', '2', '3', '6', '8', '9', '5' },
        { '3', '9', '5', '8', '1', '4', '2', '6', '7' }
    };

    vector<vector<char>> partially_filled_board = {
        { '5', '3', '.', '.', '7', '.', '.', '.', '.' },
        { '6', '.', '.', '1', '9', '5', '.', '.', '.' },
        { '.', '9', '8', '.', '.', '.', '.', '6', '.' },
        { '8', '.', '.', '.', '6', '.', '.', '.', '3' },
        { '4', '.', '.', '8', '.', '3', '.', '.', '1' },
        { '7', '.', '.', '.', '2', '.', '.', '.', '6' },
        { '.', '6', '.', '.', '.', '.', '2', '8', '.' },
        { '.', '.', '.', '4', '1', '9', '.', '.', '5' },
        { '.', '.', '.', '.', '8', '.', '.', '7', '9' }
    };

    Solution sol;
    cout << sol.isValidSudoku(full_filled_board) << endl;
    cout << sol.isValidSudoku(partially_filled_board) << endl;
}

// 输出结果：
// 0
// 1
```

提交到 Leetcode，Accepted! :) 运行时间为 32ms。
