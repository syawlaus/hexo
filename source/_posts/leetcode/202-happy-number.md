title: Leetcode 题解 - 202. Happy Number
date: 2016-07-18 16:40
categories: Leetcode
---

# 题目

编写一个算法，判断一个数是否“快乐数”。

快乐数的定义是：对于一个正整数，用各个位的平方和替换这个正整数，一直循环直到正整数为 1（并能一直保持），或循环因为不包含 1 而无法停止。那些循环最终得到 1 的数是快乐数。

<!-- more -->

**示例**：

19 是快乐数：

* 1<sup>2</sup> + 9<sup>2</sup> = 82
* 8<sup>2</sup> + 2<sup>2</sup> = 68
* 6<sup>2</sup> + 8<sup>2</sup> = 100
* 1<sup>2</sup> + 0<sup>2</sup> + 0<sup>2</sup> = 1

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    bool isHappy(int n) {
        
    }
};
```

示例里给出的 19 是快乐数，那么我们看看不是快乐数的执行过程是怎样的，试试 20：

* 2<sup>2</sup> + 0<sup>2</sup> = 4
* 4<sup>2</sup> + 0<sup>2</sup> = 16
* 1<sup>2</sup> + 6<sup>2</sup> = 37
* 3<sup>2</sup> + 7<sup>2</sup> = 58
* 5<sup>2</sup> + 8<sup>2</sup> = 89
* 8<sup>2</sup> + 9<sup>2</sup> = 145
* 1<sup>2</sup> + 4<sup>2</sup> + 5<sup>2</sup> = 42
* 4<sup>2</sup> + 2<sup>2</sup> = 20
* 2<sup>2</sup> + 0<sup>2</sup> = 4     // 与第 1 行循环了

可以看到，上述的执行过程出现死循环，无法收敛到 1，所以 20 不是快乐数。

为了检查是否会出现死循环，我们需要一个 vector 保存所有平方和。在添加一个平方和之前，遍历 vector 检查是否与已保存的相同，如果是，说明会形成死循环，n 不是快乐数。如果一直添加，直到添加 1，那么 n 是快乐数。

伪代码如下：

```
bool isHappy(int n) {
    if (n < 1) {
        return false;
    }

    vector<int> v;
    while (true) {
        int sumSquare = getSumSquare(n);
        if (sumSquare == 1) {
            return true;
        }
        if (containsElement(v, sumSquare)) {    // 死循环
            return false;
        }
        v.push_back(sumSquare);
        n = sumSquare;  // 更新 n
    }
}
```

代码如下：

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    bool isHappy(int n) {
        if (n < 1) {
            return false;
        }

        vector<int> v;
        while (true) {
            int sumSquare = getSumSquare(n);
            if (sumSquare == 1) {
                cout << "sumSquare == 1，return true" << endl;
                return true;
            }

            if (containsElement(v, sumSquare)) {    // 死循环
                cout << "vector 已存在 " << sumSquare << "，死循环，return false" << endl;
                return false;
            }

            v.push_back(sumSquare);
            cout << "vector 添加 " << sumSquare << endl;

            n = sumSquare;  // 更新 n
        }
    }

    // 求 n 各位数字的平方和
    int getSumSquare(int n) {
        int sumSquare = 0;
        while (n > 0) {
            int digit = n % 10;
            sumSquare += digit * digit;
            n /= 10;
        }
        return sumSquare;
    }

    bool containsElement(vector<int> v, int n) {
        for (int element : v) {
            if (element == n) {
                return true;
            }
        }
        return false;
    }
};

int main() {
    Solution sol;
    cout << sol.isHappy(19) << endl << endl;
    cout << sol.isHappy(20) << endl << endl;
}

// 输出结果：
// vector 添加 82
// vector 添加 68
// vector 添加 100
// sumSquare == 1，return true
// 1
// 
// vector 添加 4
// vector 添加 16
// vector 添加 37
// vector 添加 58
// vector 添加 89
// vector 添加 145
// vector 添加 42
// vector 添加 20
// vector 已存在 4，死循环，return false
// 0
// 
```

提交到 Leetcode，Accepted! :) 运行时间为 40ms。
