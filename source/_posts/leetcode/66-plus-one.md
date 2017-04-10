title: Leetcode 题解 - 66. Plus One
date: 2016-10-20 18:40
categories: Leetcode
---

# 题目

给定一个非负整数，其各数位保存在数组，对这个数 + 1。

数组头保存高数位，数组尾保存低数位。

<!-- more -->

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        
    }
};
```

既然是做 +1 操作，那就要考虑满 10 进位的情况。假设 n = 49，即 `vector<int>& digits = { 4, 9 }`。对 vector 末尾元素 +1 后，vector 就变成 { 4, 10 }。满 10 就要往前进一位，个位重置为 0，所以变成 { 5, 0 }。

如果遇到需要连续进位的情况，如 { 9, 9 }，那么个位 +1 后，变成 { 9, 10 }，进一位变成 { 10, 0 }，再进一位，变成 { 1, 0, 0 }。因为进位时可能会导致 vector 长度增加 1 的情况，所以先在 vector 头插入元素 0，在整个 +1 的循环操作完成后，再把 vector 头的 0 去掉。

伪代码如下：

```
vector<int> plusOne(vector<int>& digits) {
    // 在 vector 头插入 0
    vector.insert(0, 0);
    
    // 最低位 +1
    vector[最低位]++;
    
    // 处理进位情况
    int carryBit = 0;
    for (i = 最低位到最高位) {
        vector[i] += carryBit;
        if (vector[i] == 10) {
            carryBit = 1;   // 下一位进一位
            vector[i] = 0;  // 重置当前数位
        }
        else {
            carryBit = 0;
            break;
        }
    }
    
    // 去掉 vector 头的 0
    if (vector[0] == 0) {
        删除 vector[0]
    }

    return digits;
}
```

代码如下：

```cpp
#include <iostream>
#include <vector>
#include <frequently-used-code-snippets.h>
using namespace std;

class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        // 在 vector 头插入 0
        digits.insert(digits.begin(), 0);

        // 最低位 +1
        int lastIndex = digits.size() - 1;
        digits[lastIndex]++;

        // 处理进位情况
        int carryBit = 0;
        for (int i = lastIndex; i >= 0; i--) {
            digits[i] += carryBit;
            if (digits[i] == 10) {
                carryBit = 1;   // 下一位进一位
                digits[i] = 0;  // 重置当前数位为 0
            }
            else {
                carryBit = 0;
                break;
            }
        }

        // 去掉 vector 头的 0
        if (digits[0] == 0) {
            digits.erase(digits.begin());
        }

        return digits;
    }
};

int main() {
    int array[] = { 9, 9 };
    vector<int> v = convertArrayToVector(array, ARRAY_LENGTH(array));

    Solution sol;
    sol.plusOne(v);
}
```

提交到 Leetcode，Accepted! :) 运行时间为 3ms。

---

# 思路二

还有一个思路，就是先把 vector 转成一个 int n，在执行 n++ 之后，再把 n 转为 vector 返回。

代码如下：

```cpp
vector<int> plusOne2(vector<int>& digits) {
    // 把 digits 转化为 int
    int num = 0;
    int highestPow = digits.size() - 1;     // 最高次幂
    for (int i = 0; i < digits.size(); i++) {
        int digit = digits[i];
        num += digit * pow(10, highestPow);
        highestPow--;
    }

    // int++
    num++;

    // 把 int 转化为 digits
    vector<int> newDigits;
    while (num > 0) {
        newDigits.insert(newDigits.begin(), num % 10);
        num /= 10;
    }
    return newDigits;
}
```

但这个方案有一个严重问题，如果 digits 表示的数字超出了 int 取值范围，程序会出错。
