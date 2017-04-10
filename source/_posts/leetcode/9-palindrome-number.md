title: Leetcode 题解 - 9. Palindrome Number
date: 2016-10-26 16:20
categories: Leetcode
---

#题目

判断一个整数是否[回文数](https://zh.wikipedia.org/zh-hans/%E5%9B%9E%E6%96%87%E6%95%B0)，不要使用额外的内存空间。

<!-- more -->

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```
class Solution {
public:
    bool isPalindrome(int n) {
        
    }
};
```

我的思路是，从个位开始，把 n 的逐个数位通过模运算取出来，低位不断乘以 10 升至高位，再判断两个数是否相等。假设 n = 12321，那么：

* m = 0
* 12321 % 10 = 1，12321 / 10 = 1232，m = m * 10 + 1 = 1
* 1232 % 10 = 2，1232 / 10 = 123，m = m * 10 + 2 = 12
* 123 % 10 = 3，123 / 10 = 12，m = m * 10 + 3 = 123
* 12 % 10 = 2，12 / 10 = 1，m = m * 10 + 2 = 1232
* 1 % 10 = 1，1 / 10 = 0，m = m * 10 + 1 = 12321
* 结束循环，因为 m == n，所以 n 是回文数。
* 注意：负数不是回文数。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    bool isPalindrome(int n) {
        if (n < 0) {
            return false;
        }
        int originN = n;
        int m = 0;
        while (n > 0) {
            m = m * 10 + n % 10;
            n /= 10;
        }
        return m == originN;
    }
};

int main() {
    Solution sol;
    cout << sol.isPalindrome(12321) << endl;
    cout << sol.isPalindrome(1232) << endl;
}

// 输出结果：
// 1
// 0
```

提交到 Leetcode，Accepted! :) 运行时间为 102ms。
