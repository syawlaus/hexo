title: Leetcode 题解 - 342. Power of Four 
date: 2016-10-26 01:30
categories: Leetcode
---

# 题目

给定一个整数（有符号 32 位），编写一个函数，检查它是否 4 的幂。

<!-- more -->

**示例**：

给定 num = 16，返回 true。给定 num = 5，返回 false。

**后续**：

你的函数能够不使用任何循环／递归吗？

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    bool isPowerOfFour(int num) {
        
    }
};
```

思路跟 [Leetcode 题解 - 326. Power of Three](http://syawlaus.github.io/blog/leetcode/326-power-of-three/) 一样，求 log4(n) 是否为整数。

代码如下：

```cpp
#include <math.h>
#include <iostream>
using namespace std;

class Solution {
public:
    bool isPowerOfFour(int num) {
        double m = logbase(num, 4);
        double roundM = round(m);
        double absoluteDiff = abs(m - roundM);  // 取两者之差的绝对值
        return (absoluteDiff < EPSILON);
    }

private:
    double EPSILON = 10e-15;

    // log 对数函数换底公式
    double logbase(double n, double base) {
        return log(n) / log(base);
    }
};

int main() {
    Solution sol;
    cout << sol.isPowerOfFour(16) << endl;
    cout << sol.isPowerOfFour(17) << endl;
}

// 输出结果：
// 1
// 0
```

提交到 Leetcode，Accepted! :) 运行时间为 3ms。
