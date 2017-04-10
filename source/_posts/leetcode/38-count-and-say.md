title: Leetcode 题解 - 38. Count and Say
date: 2016-10-30 12:50
categories: Leetcode
---

#题目

count-and-say 序列是形如这样的序列：1, 11, 21, 1211, 111221, ...

* 1 读作“一个 1”或“11”
* 11 读作“两个 1” 或“21”
* 21 读作“一个 2”，然后“一个 1”，或“1211”。

给定一个整数 n，生成第 n 个 cout-and-say 序列。

<!-- more -->

**注意**：

整数序列用字符串表示。

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
class Solution {
public:
    string countAndSay(int n) {
        
    }
};
```

我对 count-and-say 序列的理解是，相邻并相同的数字 i 数量为 n，读作“n 个 i”。对于求第 n 个 cout-and-say 序列，程序的执行过程为：

* str = ""
* 从第 n-1 个序列的第一个数字 i1 开始遍历，直到遇上不同的数字 i2，记录 i1 出现的次数 n1，str += n1 + i1
* 从 i2 开始继续，直到遇上不同的数字 i3，记录 i2 出现的次数 n2，str += n2 + i2
* 重复上面的过程，直到第 n-1 个序列遍历完为止，这时得出的 str 即是第 n 个序列

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    string countAndSay(int n) {
        string sPrev = "1";
        for (int i = 1; i < n; i++) {
            // 每计算下一个 count-and-say 序列，都要重置 prevIndex 和 sNext
            int prevIndex = 0;
            string sNext = "";

            // 使用 sPrev 计算 sNext
            while (prevIndex < sPrev.length()) {
                int count = getConsecutiveCount(sPrev, prevIndex);
                sNext += to_string(count) + sPrev[prevIndex];
                prevIndex += count;
            }
            sPrev = sNext;
        }
        return sPrev;
    }

private:
    // 在 s 中，从 index 开始，返回 index 所在字符后面与之连续相同字符的个数
    int getConsecutiveCount(string& s, int index) {
        if (index >= s.length()) {
            return 0;
        }
        char currCh = s[index];
        int count = 0;
        for (; index < s.length(); index++) {
            if (s[index] == currCh) {
                count++;
            }
            else {
                break;
            }
        }
        return count;
    }
};

int main() {
    Solution sol;
    cout << sol.countAndSay(1) << endl;
    cout << sol.countAndSay(2) << endl;
    cout << sol.countAndSay(3) << endl;
    cout << sol.countAndSay(4) << endl;
    cout << sol.countAndSay(5) << endl;
    cout << sol.countAndSay(6) << endl;
    cout << sol.countAndSay(7) << endl;
}

// 输出结果：
// 1
// 11
// 21
// 1211
// 111221
// 312211
// 13112221
```

把 Solution 提交到 Leetcode，Accepted! :) 运行时间为 9ms。
