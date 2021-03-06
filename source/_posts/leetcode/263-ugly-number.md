title: Leetcode 题解 - 263. Ugly Number
date: 2016-07-18 16:40
categories: Leetcode
---

# 题目

编写一个函数，检查给定的一个数是否丑陋数。

丑陋数是一个正数，它的素数因子只包含 2、3、5。

<!-- more -->

**示例**：

6、8 是丑陋数，14 不是，因为 14 包含 7 这个素数因子。

注意：1 认为是丑陋数。

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    bool isUgly(int num) {
        
    }
};
```

我们知道，任何一个大于 1 的正数总能分解为一串素数因子的乘积，而且这些素数因子按大小排列之后，写法只有一种方式。即是说，如果 n 能分解为仅包含 2、3、5 的乘积，就不能分解为包含 7 和其它素数因子的乘积。那么问题就转化为：对于给定的 n，能不能分解为仅包含 2、3、5 的乘积。

基本思路是递归：

* 如果 n 能整除 2，判断 m = n / 2 是否丑陋数
    * 如果 m 是丑陋数，说明 m 能分解为 2、3、5 的乘积，那么 n 也是丑陋数
    * 如果 m 不是丑陋数，说明 m 的素数因子乘积里包含非 2、3、5 的素数，那么 n 也不是丑陋数
* 如果 n 不能整除 2，说明 n 的素数因子乘积里不包含 2，那么看是否仅包含 3、5
    * 如果 n 能整除 3，判断 m = n / 3 是否丑陋数
* 如果 n 不能整除 2、3，说明 n 的素数因子乘积里不包含 2、3，那么看是否仅包含 5
    * 如果 n 能整除 5，判断 m = n / 5 是否丑陋数

代码如下：

```
#include <iostream>
using namespace std;

class Solution {
public:
    bool isUgly(int n) {
        if (n < 1) {
            return false;
        }
        if (n == 1) {
            return true;
        }

        // 判断 n 的素数因子乘积是否仅包含 2、3、5
        if (n % 2 == 0) {
            return isUgly(n / 2);
        }

        // 判断 n 的素数因子乘积是否仅包含 3、5
        if (n % 3 == 0) {
            return isUgly(n / 3);
        }

        // 判断 n 的素数因子乘积是否仅包含 5
        if (n % 5 == 0) {
            return isUgly(n / 5);
        }

        // n 的素数因子乘积不包含 2、3、5
        return false;
    }
};

int main() {
    Solution sol;
    cout << sol.isUgly(6) << endl;
    cout << sol.isUgly(8) << endl;
    cout << sol.isUgly(14) << endl;
}
```

提交到 Leetcode，Accepted! :) 运行时间为 8ms。
