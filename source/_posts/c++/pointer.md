title: C++ 指针
date: 2016-06-22 16:00
categories: C++
---

本文意在整理 C++ 指针的用法，和需要注意的地方。见下面代码：

<!--- more --->

```cpp
#include <iostream>
using namespace std;

int main() {

    // pi 是一个指针，指针的值是 i 的地址
    int i = 0;
    int* pi = &i;

    // 每次运行程序，pi 的值都不同
    cout << "pi 保存的地址为：" << pi << endl << endl;

    // *pi 即是 i
    cout << "pi 指向的值为：" << *pi << endl;
    i++;
    cout << "pi 指向的值为：" << *pi << endl << endl;

    // pi++ 表示 pi 向后移动一个 int 字长（如 int 是四个字节，则 pi++ 向后移动四个字节）
    cout << "pi 保存的地址为：" << pi << endl;
    pi++;
    cout << "pi 保存的地址为：" << pi << endl << endl;

    // NULL 指针的地址固定为 0x00000000
    pi = NULL;
    cout << "NULL 的地址为：" << pi << endl << endl;

    // pi, pj 都指向同一个地址，也就是 i 的地址
    pi = &i;
    int* pj = &i;
    cout << "pi 保存的地址为：" << pi << endl;
    cout << "pj 保存的地址为：" << pj << endl << endl;
}

/*
输出：
pi 保存的地址为：0026F8B4

pi 指向的值为：0
pi 指向的值为：1

pi 保存的地址为：0026F8B4
pi 保存的地址为：0026F8B8

NULL 指针的地址为：00000000

pi 保存的地址为：0026F8B4
pj 保存的地址为：0026F8B4
*/
```

#指针定义

    int i = 0;
    int* pi = &i;

定义 pi 为“指向 int 型数据的指针”，即“保存 int 型数据地址的变量”，值为 i 的地址，& 为取地址操作符。

---

#取指针所指的数据

pi 是指针，`*pi` 是取 pi 指向的变量，即是 i 本身，所以 `*pi = i`。

---

#移动指针

pi 是 int 型指针，pi++ 表示 pi 向后移动一个 int 字长（如 int 是四个字节，则 pi++ 向后移动四个字节），见上面代码的运行结果。

---

#判断两个指针是否相同

两个指针相同，即是指向的地址相同：pi = pj。

---

#NULL

[C++11 的说明](http://www.cplusplus.com/reference/cstring/NULL/)：

> This macro expands to a *null pointer constant*.

> 这个宏展开为一个“空指针常量”。

> A *null-pointer constant* is either an integral constant expression that evaluates to zero (such as 0 or 0L), or a value of type nullptr_t (such as nullptr).

> 一个“空指针常量”要么是一个整型常量表达式，求值得出零（如 0 或 0L），要么是类型 nullptr_t 的一个值（如 nullptr）。

> A null pointer constant can be converted to any *pointer type* (or *pointer-to-member type*), which acquires a *null pointer value*. This is a special value that indicates that the pointer is not pointing to any object.

> 一个空指针常量可以转型为任意“指针类型”（或“成员指针类型”），它获取一个“空指针值”。这是一个特殊的值，表示指针没有指向任何一个对象。

更详细的讨论请见[这篇知乎](https://www.zhihu.com/question/22203461)。

---

#void* 指针

`void*` 指针是无类型指针。可理解为 `void*` 指针指向一块未知类型的内存。`void*` 可以转为任意类型的指针。
