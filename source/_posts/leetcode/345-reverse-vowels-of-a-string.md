title: Leetcode 题解 - 345. Reverse Vowels of a String
date: 2016-07-27 15:30
categories: Leetcode
---

# 题目

编写一个函数，接收一个字符串作为输入，并反转字符串其中的元音字母。

<!-- more -->

**示例**：

给定 s = "hello"，返回 "holle"。

给定 s = "leetcode"，返回 "leotcede"。

**注意**：

元音字母不包括字母 'y'。

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```
class Solution {
public:
    string reverseVowels(string s) {

    }
};
```

我的思路是：

* 使用两个 char* 指针 pHead, pTail
* pHead 往后遍历直到遇到元音字母，pTail 往前遍历直到遇到元音字母
* 交换两个元音字母
* 重复这个过程，直到 pHead, pTail 相遇

代码如下：

```cpp
#include <iostream>
#include <string>
using namespace std;

class Solution {
public:
    string reverseVowels(string s) {
        int len = s.length();
        if (len <= 1) {
            return s;
        }

        char* pHead = &s[0];
        char* pTail = &s[len - 1];

        while (true) {
            findNextVowelForward(pHead);
            findNextVowelBackward(pTail);
            if (pHead < pTail) {
                swapVowel(pHead, pTail);
                pHead++;
                pTail--;
            }
            else {
                break;
            }
        }
        return s;
    }

private:
    void findNextVowelForward(char*& pHead) {
        while (true) {
            if (isVowel(*pHead)) {
                return;
            }
            pHead++;
        }
    }

    void findNextVowelBackward(char*& pTail) {
        while (true) {
            if (isVowel(*pTail)) {
                return;
            }
            pTail--;
        }
    }

    bool isVowel(char c) {
        return (c == 'a' || c == 'A' ||
                c == 'e' || c == 'E' ||
                c == 'i' || c == 'I' ||
                c == 'o' || c == 'O' ||
                c == 'u' || c == 'U');
    }

    void swapVowel(char*& pHead,
                   char*& pTail) {
        char temp = *pHead;
        *pHead = *pTail;
        *pTail = temp;
    }

};

int main() {
    Solution sol;
    cout << sol.reverseVowels("") << endl;
    cout << sol.reverseVowels("hello") << endl;
    cout << sol.reverseVowels("leetcode") << endl;
}
```

提交到 Leetcode，Accepted! :)，运行时间为 12ms。
