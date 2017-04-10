title: Leetcode 题解 - 191. Number of 1 Bits
date: 2016-07-18 11:30
categories: Leetcode
---

#题目

编写一个函数，接收一个无符号整数作为参数，返回“1”位的数量（这个数量也称为 [Hamming weight](https://en.wikipedia.org/wiki/Hamming_weight)）。

<!-- more -->

**示例**：

32 位整数“11”的二进制表示为 00000000000000000000000000001011，那么函数应该返回 3。

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
class Solution {
public:
    int hammingWeight(uint32_t n) {
        
    }
};
```

我们用输入参数 11 作为例子，最直观的思路是：

* 把 11 从十进制转为二进制
* 统计 1 的数量

把 11 从十进制转为二进制的思路是：

* 11 / 2 = 5 余 1
*  5 / 2 = 2 余 1
*  2 / 2 = 1 余 0
*  1 / 2 = 0 余 1，商为 0 结束

可以看到，我们可以循环计算 n / 2，余数为 1 则 sum++，直到商为 0 时结束程序，返回 sum。

代码如下：

```cpp
int hammingWeight(unsigned n) {
    int sum = 0;
    while (n > 0) {
        int remainder = n % 2;
        if (remainder == 1) {
            sum++;
        }
        n = n / 2;
    }
    return sum;
}
```

提交到 Leetcode，Accepted! :)，运行时间为 8ms。
