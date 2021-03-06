title: Leetcode 题解 - 344. Reverse String
date: 2016-06-21 21:00
categories: Leetcode
---

# 题目

编写一个函数，输入为一个 string，输出为反转的 string。

<!-- more -->

**示例**：

给定 s = "hello"，返回 "olleh"

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
#include <iostream>
#include <string>
using namespace std;

string reverse(string& s) {
}

int main() {
    string s = "hello";
    cout << "original string: " << s << endl;

    s = reverse(s);
    cout << "reversed string: " << s << endl;
}
```

reverse 函数的形参为 string&，[按引用传参](http://syawlaus.github.io/blog/c++/function/passing-arguments/)，避免复制整个 s 到 reverse 形参中。

对于 reverse，我有两种思路：

1. [in-place](https://zh.wikipedia.org/zh/%E5%8E%9F%E5%9C%B0%E7%AE%97%E6%B3%95) 反转：覆盖 s，把 s 在原地反转
2. not-in-place 反转：不操作 s，新建 newStr 并保存反转后的 s

## in-place reverse

### 写法一

我们观察 s = "hello"，由 5 个字符组成。想要反转 s，容易想到下面的执行过程：

* 反转第 1 和第 5 个字符，得到 "oellh"
* 反转第 2 和第 4 个字符，得到 "olleh"
* 反转第 3 和第 3 个字符，得到 "olleh"（这一步其实是多余的）
* 结束程序

我们知道，[string 也可以当作字符数组使用](http://syawlaus.github.io/blog/c++/string)，s[i] 即第 i 个字符。那么我们只需要首尾两个 index，构造一个 while 循环，结束条件是首尾 index 相遇。循环里每次反转首尾元素，再更新 index 即可。

伪代码如下：

```
head = 0
tail = s.length - 1
while (head < tail)
    swap s[head] and s[tail]
    head + 1
    tail - 1
```

代码如下：

```cpp
#include <iostream>
#include <string>
using namespace std;

class Solution {
public:
    string reverseString(string& s) {
        int len = s.length();
        int head = 0;
        int tail = len - 1;

        while (head < tail) {
            // swap
            char temp = s[head];
            s[head] = s[tail];
            s[tail] = temp;

            // update indices
            head++;
            tail--;
        }

        return s;
    }
};

int main() {
    string s = "hello";
    cout << "original string: " << s << endl;

    Solution sol;
    s = sol.reverseString(s);
    cout << "reversed string: " << s << endl;
}
```

提交到 Leetcode，Accepted! :) 运行时间为 12 ms。

### 写法二

还是上面的 in-place 思路，在 swap s[head] and s[tail] 这一步可稍作修改，head 和 tail idx 可以改为两个指针，swap 时交换两个指针指向的值。

伪代码如下：

```
pHead 指向 s 第一个元素
pTail 指向 s 最后一个元素
while (pHead < pTail)
    swap pHead, pTail 指向的元素值
    pHead 指向下一个元素
    pTail 指向上一个元素
```

代码如下：

```cpp
#include <iostream>
#include <string>
using namespace std;

class Solution {
public:
    string reverseString(string& s) {
        char* pHead = &s[0];

        int len = s.length();
        char* pTail = &s[len - 1];

        while (pHead < pTail) {
            // swap chars which pHead & pTail point to
            char temp = *pHead;
            *pHead = *pTail;
            *pTail = temp;

            // update pHead & pTail
            pHead++;
            pTail--;
        }

        return s;
    }
};

int main() {
    string s = "hello";
    cout << "original string: " << s << endl;

    Solution sol;
    s = sol.reverseString(s);
    cout << "reversed string: " << s << endl;
}
```

提交到 Leetcode，Accepted! :) 运行时间为 12 ms。

## not-in-place reverse

not-in-place 的思路也很简单：不操作 s，新建 newStr 并保存反转后的 s。具体的执行过程为：

* tail 保存 s 最后一个元素的值
* newStr += s[tail]
* tail--
* 直到 tail 遍历了所有元素，结束程序

伪代码如下：

```cpp
新建 newStr
for (s 的元素从末尾开始遍历, 下标为 tail)
    newStr += s[tail]
    tail - 1
```

代码如下：

```cpp
#include <iostream>
#include <string>
using namespace std;

class Solution {
public:
    string reverseString(const string& s) {     // 既然不操作 s，那么就不允许修改 s
        string newStr;
        int len = s.length();
        for (int tail = len - 1; tail >= 0; tail--) {
            newStr += s[tail];
        }
        return newStr;
    }
};

int main() {
    string s = "hello";
    cout << "original string: " << s << endl;

    Solution sol;
    string newStr = sol.reverseString(s);
    cout << "reversed string: " << newStr << endl;
}
```

提交到 Leetcode，Accepted! :) 运行时间为 12 ms。
