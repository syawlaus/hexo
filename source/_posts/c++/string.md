title: C++ string
date: 2016-06-22 11:30
categories: C++
---

本文意在整理 C++ string 的用法，和需要注意的地方。

<!-- more -->

# include

在 C++ 程序中，使用 string 要 `#include <string>`，原因见《[C++ 程序常见报错](http://syawlaus.github.io/blog/c++/frequent-errors/)》。

---

# string 作为 char 数组

string 可以当 char 数组使用，变量名为数组首元素地址。见下面代码：

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string s = "hello";

    for (int i = 0; i < s.length(); i++) {
        cout << s[i] << ' ';
    }
    cout << endl;

    for (char ch : s) {
        cout << ch << ' ';
    }
}

/*
输出：
h e l l o 
h e l l o 
*/
```

---

# 操作符重载

    operator+       字符串链接
    operator==      判断是否相等
    operator!=      判断是否不等于
    operator<       判断是否小于
    operator>>      从输入流中读入字符串
    operator<<      字符串写入输出流

---

# 常用函数

To be continued.
