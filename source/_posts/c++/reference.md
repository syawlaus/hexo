title: C++ 引用
date: 2016-05-10 12:00
categories: C++
---

# 什么是引用？

在《[C++ 函数：传参方式](http://syawlaus.github.io/blog/c++/function/passing-arguments/)》一文中，整理了 [按引用传参](http://syawlaus.github.io/blog/c++/function/passing-arguments/#Pass-by-reference（按引用传参）) 的用法。那么到底什么是 reference？

<!-- more -->

reference 是一个变量，使得程序可以重定向访问一个特定数据。具体实现是，reference 保存其引用的数据的内存地址。

---

# 语法

## 引用（reference）

reference 的语法是：

    int i = 1;
    int& ri = i;    // ri 是 i 的引用

再看看下面的代码：

```cpp
#include <iostream>
using namespace std;

int main() {
    int i = 0;
    int& ri = i;
    cout << " i = " << i << endl;
    cout << "ri = " << ri << endl << endl;

    i++;
    cout << " i = " << i << endl;
    cout << "ri = " << ri << endl << endl;

    ri++;
    cout << " i = " << i << endl;
    cout << "ri = " << ri << endl << endl;

    return 0;
}
```

输出结果：

     i = 0
    ri = 0
    
     i = 1
    ri = 1
    
     i = 2
    ri = 2

可以看到，ri 的作用等同于 i，可认为 ri 的 i 的别名。

另外，指针的引用是：

    int*& rpi;

## 常量引用（const-reference）

如果我们需要不允许 reference 能修改被引用的变量，可以使用 const-reference，我们对上面的代码稍作修改：

```cpp
#include <iostream>
using namespace std;

int main() {
    int i = 0;
    const int& cri = i;     // cri 是 const-reference
    cri++;      // Error: expression must be a modifiable lvalue

    return 0;
}
```

编译器会在 `cri++` 时报错“Error: expression must be a modifiable lvalue”。

---

# 注意事项

既然 reference 保存其引用的数据的内存地址，那么跟 pointer 有什么区别？

---

# 参考资料

* [Reference (C++) - MSDN](https://msdn.microsoft.com/en-us/library/dz43scw4.aspx)
* [Reference (C++) - Wikipedia][1]
* [Reference (computer science) - Wikipedia][2]
* [What are the differences between a pointer variable and a reference variable in C++?](http://stackoverflow.com/questions/57483/what-are-the-differences-between-a-pointer-variable-and-a-reference-variable-in)


[1]: https://en.wikipedia.org/wiki/Reference_(C%2B%2B)
[2]: https://en.wikipedia.org/wiki/Reference_(computer_science)
