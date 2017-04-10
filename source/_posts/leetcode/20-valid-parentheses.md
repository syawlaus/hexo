title: Leetcode 题解 - 20. Valid Parentheses
date: 2016-11-05 01:00
categories: Leetcode
---

# 题目

给定一个字符串，仅包含字符 `(`, `)`, `{`, `}`, `[` 和 `]`，判断输入的字符串是否有效。

括号的开闭必须有序，如 `()` 和 `()[]{}` 有序，但 `(]` 和 `([)]` 无序。

<!-- more -->

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    bool isValid(string s) {
        
    }
};
```

有效的括号 string 的结构是先入后出，所以思路是使用一个 stack，如果当前字符是 `(`, `[` 或 `{`，则入栈。如果当前字符是 `)`, `]` 或 `}`，则检查栈顶元素是否匹配，如果匹配就出栈，继续遍历 s，不匹配则 return false。如果遍历完整个 s，栈里还有元素，则 return false，没有元素则 return true。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    bool isValid(string s) {
        stack<char> bracketStack;
        for (int i = 0; i < s.length(); i++) {
            char c = s[i];
            if (c == '(' || c == '[' || c == '{') {
                bracketStack.push(c);
                continue;
            }
            if (c == ')' &&
                ! bracketStack.empty() &&
                bracketStack.top() == '(') {
                bracketStack.pop();
                continue;
            }
            if (c == ']' &&
                ! bracketStack.empty() &&
                bracketStack.top() == '[') {
                bracketStack.pop();
                continue;
            }
            if (c == '}' &&
                ! bracketStack.empty() &&
                bracketStack.top() == '{') {
                bracketStack.pop();
                continue;
            }
            return false;
        }
        return bracketStack.empty();
    }
};

int main() {
    Solution sol;
    cout << sol.isValid("()") << endl;
    cout << sol.isValid("(]{)") << endl;
    cout << sol.isValid("()[]{}") << endl;
    cout << sol.isValid("([)]{}") << endl;
    cout << sol.isValid("[") << endl;
    cout << sol.isValid("]") << endl;
}
```

提交到 Leetcode，Accepted! :) 运行时间为 0ms。
