title: Leetcode 题解 - 326. Power of Three
date: 2016-07-18 12:00
categories: Leetcode
---

# 题目

给定一个整数，编写一个函数，判断该整数是否 3 的幂。

<!-- more -->

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
    bool isPowerOfThree(int n) {
        
    }
};
```

我们先列举一些 3 的幂：

* 3<sup>0</sup> = 1
* 3<sup>1</sup> = 3
* 3<sup>2</sup> = 9
* 3<sup>3</sup> = 27
* 3<sup>4</sup> = 81
* 3<sup>5</sup> = 243
* 3<sup>6</sup> = 729
* 3<sup>7</sup> = 2187
* 3<sup>8</sup> = 6561
* 3<sup>9</sup> = 19683
* 3<sup>10</sup> = 59049

思路如下：

1. 如果 n == 1，那么 return true
2. 如果 n > 1，不断除以 3，如果出现余数，则 return false
                           如果一直不出现余数，直到商为 1，则 return true

伪代码如下：

```
bool isPowerOfThree(int n) {
    if (n < 1) {
        return false;
    }

    if (n == 1) {
        return true;
    }
    
    while (n > 1) {
        int remainder = n % 3;
        if (remainder == 0) {
            n = n / 3;
        }
        else {
            return false;
        }
    }
    return true;
}
```

代码如下：
```cpp
#include <iostream>
using namespace std;

class Solution {
public:
    bool isPowerOfThree(int n) {
        if (n < 1) {
            return false;
        }

        if (n == 1) {
            return true;
        }

        while (n > 1) {
            int remainder = n % 3;
            if (remainder == 0) {
                n = n / 3;
            }
            else {
                return false;
            }
        }
        return true;
    }
};

int main() {
    Solution sol;
    cout << sol.isPowerOfThree(0) << endl;
    cout << sol.isPowerOfThree(1) << endl;
    cout << sol.isPowerOfThree(3) << endl;
    cout << sol.isPowerOfThree(9) << endl;
    cout << sol.isPowerOfThree(27) << endl;
    cout << sol.isPowerOfThree(28) << endl;
}

// 输出结果：
// 0
// 1
// 1
// 1
// 1
// 0
```

提交到 Leetcode，Accepted! :) 运行时间为 136ms。

# 后续

> 你的函数能够不使用任何循环／递归吗？

我们回看一些 3 的幂，看看能不能找出一些规律：

* 3<sup>0</sup> = 1
* 3<sup>1</sup> = 3
* 3<sup>2</sup> = 9
* 3<sup>3</sup> = 27        // 2 + 7 = 9
* 3<sup>4</sup> = 81        // 8 + 1 = 9
* 3<sup>5</sup> = 243       // 2 + 4 + 3 = 9
* 3<sup>6</sup> = 729       // 7 + 2 + 9 = 18
* 3<sup>7</sup> = 2187      // 2 + 1 + 8 + 7 = 18
* 3<sup>8</sup> = 6561      // 6 + 5 + 6 + 1 = 18
* 3<sup>9</sup> = 19683     // 1 + 9 + 6 + 8 + 3 = 27
* 3<sup>10</sup> = 59049    // 5 + 9 + 0 + 4 + 9 = 27

可以看到，每个幂的各位相加之和也是 3 的幂。但不是各位相加之和是 3 的幂的数就一定是 3 的幂，如 45。

想起了一个很简单的数学办法：求 log 值。

* log<sub>3</sub>1 = 0
* log<sub>3</sub>3 = 1
* log<sub>3</sub>9 = 2
* log<sub>3</sub>27 = 3
* log<sub>3</sub>81 = 4
* log<sub>3</sub>243 = 5
* log<sub>3</sub>729 = 6

我们调用 double m = log<sub>3</sub>n，但是 m 是非常接近实际整数值结果的 double 值，这时候我们认为，只要 |m - 整数值| 少于某个误差范围（如 10<sup>-5</sup>），两者相等。

伪代码如下：

```
bool isPowerOfThree(int n) {
    double m = Math.log(n, 3);
    double roundM = Math.round(m);  // roundM 是最接近 m 的整数值
    double absoluteDiff = abs(m - roundM);  // 取两者之差的绝对值
    return (absoluteDiff < 10^-5);
}
```

代码如下：

```cpp
#include <iostream>
#include <math.h>
using namespace std;

class Solution {
public:

    double EPSILON = 10e-5;

    bool isPowerOfThree2(int n) {
        double m = logbase(n, 3);
        double roundM = round(m);
        double absoluteDiff = abs(m - roundM);  // 取两者之差的绝对值
        return (absoluteDiff < EPSILON);

    }

    // log 换底公式
    double logbase(double a, double base) {
        return log(a) / log(base);
    }
};

int main() {
    Solution sol;
    cout << sol.isPowerOfThree2(0) << endl;
    cout << sol.isPowerOfThree2(1) << endl;
    cout << sol.isPowerOfThree2(3) << endl;
    cout << sol.isPowerOfThree2(9) << endl;
    cout << sol.isPowerOfThree2(27) << endl;
    cout << sol.isPowerOfThree2(28) << endl;
}

// 输出结果：
// 0
// 1
// 1
// 1
// 1
// 0
```

提交到 Leetcode，Wrong Answer，在测试用例 19682，我的函数返回 true，但正确的应该返回 false。

在单步调试后，得出的结果如下：

    m            = 8.9999537538815275
    roundM       = 9.0000000000000000
    absoluteDiff = 0.000046246118472481612
    EPSILON      = 0.0001

那么只要把 EPSILON 设为更小的 10<sup>-15</sup> 即可。

提交到 Leetcode，Accepted! :) 运行时间为 168ms。
