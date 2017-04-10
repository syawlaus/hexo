title: Leetcode 题解 - 58. Length of Last Word
date: 2016-11-11 13:40
categories: Leetcode
---

# 题目

给定一个字符串 s，由大写/小写字母和空格 ' ' 组成，返回字符串最后一个单词的长度。

如果最后一个单词不存在，返回 0。

<!-- more -->

**注意**：

一个单词定义为，由非空格的字符组成的字符串。

**示例**：

给定 s = "Hello World"，返回 5。

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    int lengthOfLastWord(string s) {
        
    }
};
```

思路是，从 s 的最后一个字符往前遍历，直到遇到第一个空格，返回单词长度。代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    int lengthOfLastWord(string s) {
        int length = 0;
        for (int i = s.size() - 1; i >= 0 && s[i] != ' '; i--) {
            length++;
        }
        return length;
    }
};

int main() {
    Solution sol;
    cout << sol.lengthOfLastWord("Hello world") << endl;
    cout << sol.lengthOfLastWord("Helloworld") << endl;
    cout << sol.lengthOfLastWord(" ") << endl;
}

// 输出结果：
// 5
// 10
// 0
```

提交到 Leetcode，Wrong Answer! 导致错误的输入为 "a "，正确应返回 1，我的代码返回 0。看来我是理解错了题意，修改很简单，只有在第 7 行 for 循环前，先找到最后一个非空格字符的下标即可。代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    int lengthOfLastWord(string s) {
        int length = 0;
        int lastIndex = findLastNonEmptySpaceIndex(s);
        for (int i = lastIndex; i >= 0 && s[i] != ' '; i--) {
            length++;
        }
        return length;
    }

private:
    const int UNDEFINED = -1;

    int findLastNonEmptySpaceIndex(string& s) {
        int i = UNDEFINED;
        for (i = s.size() - 1; i >= 0 && s[i] == ' '; i--) {

        }
        return i;
    }
};

int main() {
    Solution sol;
    cout << sol.lengthOfLastWord("Hello world") << endl;
    cout << sol.lengthOfLastWord("Helloworld") << endl;
    cout << sol.lengthOfLastWord("abc     ") << endl;
    cout << sol.lengthOfLastWord(" ") << endl;
}

// 输出结果：
// 5
// 10
// 3
// 0
```

把 Solution 提交到 Leetcode，Accepted! :) 运行时间为 3ms。
