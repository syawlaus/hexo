title: Leetcode 题解 - 171. Excel Sheet Column Number
date: 2016-07-05 01:20
categories: Leetcode
---

#题目

给定如下的字母列表，返回某字母行对应的行数。

    A -> 1
    B -> 2
    C -> 3
    ...
    Z -> 26
    AA -> 27
    AB -> 28 

<!-- more -->

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
class Solution {
public:
    int titleToNumber(string s) {
        
    }
};
```

在字母表里，我们很明显能看出，每 26 次为一个循环，而 AA 是 27，这样我们可以把字母表的循环规律理解为 26 进制的计数。

    A -> 1，即 26^0 + 1
    B -> 2，即 26^0 + 2
    C -> 3，即 26^0 + 3
    ...
    Z -> 26，即 26^0 + 26

    AA -> 27，即 26^1 + 1
    AB -> 28，即 26^1 + 2

首先我们要建立 A～Z 到 1～26 的一一映射，这个很简单，只要把 A～Z 的 [ASCII 编码](https://zh.wikipedia.org/wiki/ASCII)减去 64 即可。

然后每行的字母 string，先把 string 转化为 char 数组，再对每一位进行 26 进制计数。如：

       AB
    -> ['A', 'B']
    -> A * 26^1 + B * 26*0
    -> 1 * 26^1 + 2 * 26*0
    -> 26 + 2
    -> 28

数组 index 越小的字母，其 26 次幂越高。比如，字母 index = 0，size = 2，其 26 次幂 = size - 1 - index。

伪代码如下：

```
int titleToNumber(string s) {
    char letterArr[] = s.toCharArray();
    int size = s.size();

    // 遍历 char 数组，取每个字母出来，(letter - 64) * 26^pow
    // index 越小的字母，表示其 26 次幂越高
    // 比如，字母 index = 0，size = 2，其 26 次幂 = size - 1 - index

    int sum = 0;
    for (int i = 0; i < size; i++) {
        char letter = letterArr[i];    
        int pow = size - 1 - i;
        sum += (letter - 64) * 26^pow;
    }

    return sum;
}
```

代码如下：

```cpp
#include <iostream>
#include <string>
#include <math.h>
using namespace std;

class Solution {
public:
    int titleToNumber(string s) {
        char* letterArr = &s[0];
        int size = s.size();

        // 遍历 char 数组，取出每个字母，(letter - 64) * (26 ^ pow)
        int sum = 0;
        for (int i = 0; i < size; i++) {
            char letter = letterArr[i];
            int pow26 = size - 1 - i;
            sum += (letter - 64) * pow(26, pow26);
        }

        return sum;
    }
};

int main() {
    Solution sol;

    string str = "A";
    cout << str << " -> " << sol.titleToNumber(str) << endl;

    str = "B";
    cout << str << " -> " << sol.titleToNumber(str) << endl;

    str = "AA";
    cout << str << " -> " << sol.titleToNumber(str) << endl;

    str = "AB";
    cout << str << " -> " << sol.titleToNumber(str) << endl;
}

// 输出结果：
// A -> 1
// B -> 2
// AA -> 27
// AB -> 28 
```

提交到 Leetcode，Accepted! :) 运行时间为 8ms。
