title: C++ 程序常见报错
date: 2016-06-22 12:30
categories: C++
---

本文意在整理 C++ 程序常见报错，并分析报错的原因。

<!--- more -->

# string

使用 string 要 `#include <string>`，否则 cout << str 时会报错：

    No operator "<<" matches these operands.

意思是，没有操作符 "<<" 匹配这些（string 类型）操作数。

这是因为，string 类重载了 << 操作符，如果没有 `#include <string>`，那么 << 不能作用在 string 上，所以报错。
