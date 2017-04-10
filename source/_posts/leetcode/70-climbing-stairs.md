title: Leetcode 题解 - 70. Climbing Stairs
date: 2016-07-24 12:30
categories: Leetcode
---

# 题目

你正在走上一个楼梯，要走 n 步才能到顶。

每一次你可以走 1 或 2 步，走到顶一共有多少种不同的走法？

<!-- more -->

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    int climbStairs(int n) {

    }
};
```

比如楼梯有 6 个台阶，我理解的题目中“不同的走法”的意思是，对于“1 1 1 1 2”和“2 1 1 1 1”应理解同一种走法。

那么对于 6 个台阶的楼梯，次数最多的走法当然是每次只走 1 步，那么要走 6 次。次数最少的走法是每次走 2 步，那么要走 3 次。

对于 7 个台阶的楼梯，最多走 7 次，每次 1 步。最少走 4 次，2 步走三次，1 步走一次。

所以对于 n 个台阶的楼梯来说，次数最多为 n，次数最少为 (n - 1) / 2 + 1 次（int 除法）。所以走法的次数是两者之差。

代码如下：

```cpp
int climbStairs(int n) {
    int max = n;
    int min = (n - 1) / 2 + 1;
    return (max - min + 1);
}
```

提交到 Leetcode，Wrong Answer! 对于输入 3，正确应输出 2，我的函数输出 3。

看来题目“不同的走法“应该还要考虑同一组合的不同排列。所以 3 有 3 种走法，分别是“1 1 1”、“1 2”、“2 1”。

那么就使用递归：

* 对于 n 个台阶的楼梯，先走 1 步，剩下的递归
* 对于 n 个台阶的楼梯，先走 2 步，剩下的递归

代码如下：

```cpp
int climbStairs(int n) {
    if (n <= 0) {
        return 0;
    }
    return climbStairsRecur(n);
}

// 保证 n > 0
int climbStairsRecur(int n) {
    cout << "n = " << n << endl;
    if (n == 0) {
        return 1;
    }
    int sum = 0;
    if (n >= 2) {
        sum += climbStairsRecur(n - 1);
        sum += climbStairsRecur(n - 2);
    }
    else {      // 即 n == 1
        sum += climbStairsRecur(n - 1);
    }
    return sum;
}
```

提交到 Leetcode，Time Limit Exceeded! 超时的输入是 44。

上面的代码很严重的一个问题是重复计算太多，运行时间是指数。在计算 climbStairsRecur(44) 时（以下简称 csr），先计算 if 里的 csr(43) 和 csr(42)，再计算 else 里的 csr(42)，就跟递归写法的 fibonacci 算法一样。

上面的算法是正确的，虽然运行时间太长，但我们可以输出一些结果，看看有没有什么规律。

    n  | csr(n)
    ----------------------------
    1  |   1
    2  |   2
    3  |   3
    4  |   5
    5  |   8
    6  |  13
    7  |  21
    8  |  34

能看到很明显的规律，即：

* n <= 2, csr(n) = n
* n > 2, csr(n) = csr(n-1) + csr(n-2)

这样计算的思路是，使用两个 int 值 a = csr(n-1) 和 b =csr(n-2)，计算出 csr(n) 后，a = csr(n-1), b = csr(n)

代码如下：

```cpp
#include <iostream>
using namespace std;

class Solution {
public:
    int climbStairs(int n) {
        if (n <= 0) {
            return 0;
        }
        // n = 1 或 2
        if (n <= 2) {
            return n;
        }
        // n >= 3
        int a = 1;
        int b = 2;
        int count = 3;
        while (count <= n) {
            int temp = b;
            b = a + b;
            a = temp;
            count++;
        }
        return b;
    }
};

int main() {
    Solution sol;
    for (int i = 0; i < 9; i++) {
        cout << sol.climbStairs(i) << endl;
    }
    return 0;
}

// 输出结果：
// 0
// 1
// 2
// 3
// 5
// 8
// 13
// 21
// 34
```

提交到 Leetcode，Accepted! 运行时间为 0ms。
