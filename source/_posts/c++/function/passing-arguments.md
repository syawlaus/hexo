title: C++ 函数：传参方式
date: 2016-05-08 16:00
categories: C++
---

C++ 函数有多种传参方式，本文意在清晰整理各种传参方式的原理，并区分在什么情况下使用哪种。

<!-- more -->

# Pass-by-value（按值传参）

我们首先看看一个最简单的 C++ 函数定义：

```cpp
#include <iostream>
using namespace std;

void addOne(int value) {
    value++;
}

int main() {
    int i = 1;
    addOne(i);
    cout << i << endl;
    return 0;
}

// 输出结果：
1
```

可以看到，在调用了 `addOne` 后，实参 i 的值仍是 1。这是因为在每次调用 `addOne` 时，形参 value 的值由实参 i 的值复制而来，`addOne` 执行完毕后，value 离开了其作用域，新的值 2 无法在 `addOne` 外部（即 `main`）生效，所以 i 的值仍是 1。即只改变复制者（value）的值，没有改变原来（i）的值。

默认情况下，C++ 函数都是 pass-by-value。

pass-by-value 的好处：

* 形参值（value）由函数被调用时实参值（i）复制而来，不会影响函数外部实参（i）原来的值。

pass-by-value 的坏处：

* 如果实参的值占用的内存很多（如一个有大量元素的数组，或一个庞大复杂的结构体／类实例，或一个很长的字符串），那么在函数调用时复制数据到形参的过程既耗内存又耗时。

何时应该使用 pass-by-value？

* 实参是基本数据类型或枚举类型。

何时不应该使用 pass-by-value？

* 实参是数组、结构体、类。

---

# Pass-by-reference（按引用传参）

如果我们想在 `addOne` 执行时能直接操作实参 i 的值，那么需要把形参修改为实参的[引用（reference）](http://syawlaus.github.io/blog/c++/reference/)：

```cpp
#include <iostream>
using namespace std;

void addOne(int& value) {       // pass-by-reference
    value++;
}

int main() {
    int i = 1;
    addOne(i);
    cout << i << endl;
    return 0;
}

// 输出结果：
2
```

`int& value` 的意思是，在实参 i 传入到 `addOne` 时，创建一个名为 value 的引用，可理解为 value 即 i 本身。

pass-by-reference 还有一个好处是，因为引用必须被初始化，所以引用作为形参可避免空值。

何时该使用 pass-by-reference？

* 实参占用大量内存，调用函数时不宜把实参完整复制给形参。
* 实参需要被修改。
* 形参是数组、结构体、类。
* 因为函数只能有一个返回值，所以当我们需要返回多个值时，实参使用 pass-by-reference 能起到相同效果。

何时不应该使用 pass-by-reference？

* 形参为基本数据类型。

---

# Pass-by-const-reference（按常量引用传参）

如果我们需要不允许形参引用能修改被引用的变量（即实参），可以 pass-by-const-reference：

    void print(const vector<int>& v);

此时修改 v 会报错。

一个使用原则是：函数使用 pass-by-reference 时，尽量使用 pass-by-const-reference，除非明确需要修改形参。

---

# Pass-by-address（按地址传参）

类似 pass-by-reference，我们也可以把实参的地址传入到函数中，此时形参的类型就是[指针](http://syawlaus.github.io/blog/c++/pointer/)了。见代码：

```cpp
#include <iostream>
using namespace std;

void addOne(int* pi) {
    (*pi)++;    // dereference
}

int main() {
    int i = 0;
    cout << "i = " << i << endl;

    addOne(&i);     // 传入 i 的地址
    cout << "i = " << i << endl;

    return 0;
}

// 输出结果：
i = 0
i = 1
```

我们再看看下面的代码：

```cpp
#include <iostream>
using namespace std;

void setToNull(int* ptr) {
    ptr = NULL;
}

int main() {
    int five = 5;
    int* ptr = &five;
    cout << *ptr << endl;

    setToNull(ptr);
    if (ptr) {
        cout << *ptr << endl;
    }
    else {
        cout << "ptr is NULL" << endl;
    }

    return 0;
}

// 输出结果：
5
5
```

可以看到，`setToNull` 把形参 ptr 置为空指针，没有影响实参 ptr，那就是说，`setToNull` 也是 pass-by-value。如果想要把形参 ptr 置为空指针后，实参 ptr 也为空指针，则需要 pass-by-reference，只不过这次的 reference 是指针的 reference：

```cpp
#include <iostream>
using namespace std;

void setToNull(int* ptr) {
    ptr = NULL;
}

void setToNull2(int*& rptr) {   // rptr 是指针的引用
    rptr = NULL;
}

int main() {
    int five = 5;
    int* ptr = &five;
    cout << *ptr << endl;

    setToNull(ptr);
    if (ptr) {
        cout << *ptr << endl;
    }
    else {
        cout << "ptr is NULL" << endl;
    }

    setToNull2(ptr);
    if (ptr) {
        cout << *ptr << endl;
    }
    else {
        cout << "ptr is NULL" << endl;
    }

    return 0;
}

// 输出结果：
5
5
ptr is NULL
```

我们看到，pass-by-address 跟 pass-by-reference 很相像。pass-by-address 实际上也是 pass-by-value，只不过复制的是地址。因为 reference 本身也是地址，所以 pass-by-reference 也是 pass-by-value。Everything is pass-by-value!

pass-by-address 的好处：

* 实参需要被修改，如果不想被修改，形参加上 const 关键字。
* 实参占用大量内存，如数组、结构体、类。
* 需要函数返回多个值。

pass-by-address 的坏处：

* 需要判断指针是否为空，dereference 空指针会导致程序崩溃。

何时应该使用 pass-by-address？

* 实参是指针／数组。

何时不应该使用 pass-by-address？

* 实参是结构体／类（使用 pass-by-reference）。
* 实参是基本数据类型（使用 pass-by-value）。

我们看到，pass-by-address 与 pass-by-reference 几乎一模一样。另外因为 pass-by-reference 总体上来说比 pass-by-address 更安全（reference 必须被初始化，这样能避免 pass-by-reference 形参为空指针的问题），所以应优先使用 pass-by-reference。

另外注意，如果要取 pass-by-address 的参数指向的元素（即 *p），先判断指针是否为空。对空指针取指向的元素，会导致程序崩溃。

---

# 参考资料

* 《[Programming -- Principles and Practice Using C++](http://www.stroustrup.com/programming.html)》第 8.5.3~8.5.5 节
* [7.2 — Passing arguments by value](http://www.learncpp.com/cpp-tutorial/72-passing-arguments-by-value/)
* [7.3 — Passing arguments by reference](http://www.learncpp.com/cpp-tutorial/73-passing-arguments-by-reference/)
* [7.4 — Passing arguments by address]()
