title: Leetcode 题解 - 231. Power of Two
date: 2016-07-10 18:10
categories: Leetcode
---

# 题目

给定一个整数，编写一个函数，判断该整数是否 2 的幂。

<!-- more -->

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    bool isPowerOfTwo(int n) {
        
    }
};
```

我们先列举一些 2 的幂：

* 2<sup>0</sup> = 1
* 2<sup>1</sup> = 2
* 2<sup>2</sup> = 4
* 2<sup>3</sup> = 8
* 2<sup>4</sup> = 16
* 2<sup>5</sup> = 32
* 2<sup>6</sup> = 64
* 2<sup>7</sup> = 128
* 2<sup>8</sup> = 256
* 2<sup>9</sup> = 512
* 2<sup>10</sup> = 1024

思路如下：

1. 如果 n == 1，那么 return true
2. 如果 n > 1
    * 如果 n 是奇数，return false
    * 如果 n 是偶数， n / 2 = m（整型除法）
        * 如果 m 是奇数
            * 如果 m ≠ 1，那么 return false
            * 如果 m == 1，那么 return true
        * 如果 m 是偶数，那么 n = m，回到 2

伪代码如下：

```
bool isPowerOfTwo(int n) {
    if (n < 1) {
        return false;
    }
    if (n == 1) {
        return true;
    }

    while (n > 1) {
        if (n 是奇数) {
            return false;
        }
        int m = n / 2;
        if (m 是奇数) {
            return m == 1;
        }
        else {
            n = m;
        }
    }

    return false;
}
```

代码如下：

```cpp
#include <iostream>
using namespace std;

class Solution {
public:
    bool isPowerOfTwo(int n) {
        if (n < 1) {
            return false;
        }
        if (n == 1) {
            return true;
        }

        while (n > 1) {
            if (isOdd(n)) {
                return false;
            }
            int m = n / 2;
            if (isOdd(m)) {
                return m == 1;
            }
            else {
                n = m;
            }
        }

        return false;
    }

    bool isOdd(int n) {
        return (n % 2 != 0);
    }
};

int main() {
    Solution sol;
    cout << sol.isPowerOfTwo(0) << endl;
    cout << sol.isPowerOfTwo(1) << endl;
    cout << sol.isPowerOfTwo(2) << endl;
    cout << sol.isPowerOfTwo(3) << endl;
    cout << sol.isPowerOfTwo(4) << endl;
    cout << sol.isPowerOfTwo(5) << endl;
}

// 输出结果：
// 0
// 1
// 1
// 0
// 1
// 0
```

提交到 Leetcode，Accepted! :) 运行时间为 8ms。
