title: C++ 循环内局部变量的地址分配
date: 2016-07-19 12:00
categories: C++
---

这个问题之前和同学交流过，一时之间没有记录下来，前两天在刷 Leetcode [350. Intersection of Two Arrays II](https://leetcode.com/problems/intersection-of-two-arrays-ii/) 时[又遇到一次](http://syawlaus.github.io/blog/leetcode/350-intersection-of-two-arrays-ii/#local-variables-address-in-a-loop)，这次要解决掉这个问题。

<!--- more -->

# 示例一

我们先看看能重现这个问题的最简短的代码：

```cpp
#include <iostream>
using namespace std;

int main() {
    // 示例一
    for (int i = 0; i < 3; i++) {
        int a = i;
        cout << &a << endl;
    }
}

// 输出结果：
// 0045F738
// 0045F738
// 0045F738
```

虽然每次执行程序输出的地址都不相同，但三个地址总是相同。为什么会这样？

查看 stackoverflow [这篇问答](http://stackoverflow.com/questions/12446586/local-variables-defined-inside-for-loops-in-c)和[这篇问答](http://stackoverflow.com/questions/5136393/for-loop-local-variables-in-c)后，了解到这是编译器的优化，因为 a 在完成循环里的一次迭代后，作用域失效，在下一次迭代时又要创建 int 型数据，所以直接重用了该地址，不再另外分配内存地址。也可以理解为销毁了 a 所在内存地址的数据，在下次迭代时，又分配到那个地址。

---

# 示例二

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 示例一
    //for (int i = 0; i < 3; i++) {
    //    int a = 0;
    //    cout << &a << "  " << a << endl;
    //}

    // 示例二
    vector<int> v;
    v.push_back(0);
    v.push_back(1);
    v.push_back(2);
    v.push_back(3);
    v.push_back(4);

    for (int i : v) {
        cout << "   i: " << &i << "  " << i << endl;

        static int j = 0;
        cout << "v[" << j << "]: " << &v[j] << "  " << v[j] << endl << endl;
        j++;
    }
}

// 输出结果：
//    i: 0020FE94  0
// v[0]: 00334D38  0
// 
//    i: 0020FE94  1
// v[1]: 00334D3C  1
// 
//    i: 0020FE94  2
// v[2]: 00334D40  2
// 
//    i: 0020FE94  3
// v[3]: 00334D44  3
// 
//    i: 0020FE94  4
// v[4]: 00334D48  4
```

示例二的 int i，跟示例一的 int a 很像，我猜想 for (int i : v) 的语法背后就是用示例一的做法实现的。那么我们只要把 for (int i : v) 改为 for (int& i : v)，结果就一样了（即对于 v 里的每个 int 型数据，通过引用 i 操作）：

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 示例一
    //for (int i = 0; i < 3; i++) {
    //    int a = 0;
    //    cout << &a << "  " << a << endl;
    //}

    // 示例二
    vector<int> v;
    v.push_back(0);
    v.push_back(1);
    v.push_back(2);
    v.push_back(3);
    v.push_back(4);

    for (int& i : v) {
        cout << "   i: " << &i << "  " << i << endl;

        static int j = 0;
        cout << "v[" << j << "]: " << &v[j] << "  " << v[j] << endl << endl;
        j++;
    }
}

// 输出结果：
//    i: 00564D38  0
// v[0]: 00564D38  0
// 
//    i: 00564D3C  1
// v[1]: 00564D3C  1
// 
//    i: 00564D40  2
// v[2]: 00564D40  2
// 
//    i: 00564D44  3
// v[3]: 00564D44  3
// 
//    i: 00564D48  4
// v[4]: 00564D48  4
```

---

# 示例三

```cpp
#include <iostream>
#include <vector>
using namespace std;

void foo() {
    int x = 1;
    cout << "x: " << &x << "  " << x << endl;
}

int main() {
    // 示例一
    //for (int i = 0; i < 3; i++) {
    //    int a = 0;
    //    cout << &a << "  " << a << endl;
    //}

    // 示例二
    //vector<int> v;
    //v.push_back(0);
    //v.push_back(1);
    //v.push_back(2);
    //v.push_back(3);
    //v.push_back(4);

    //for (int& i : v) {
    //    cout << "   i: " << &i << "  " << i << endl;

    //    static int j = 0;
    //    cout << "v[" << j << "]: " << &v[j] << "  " << v[j] << endl << endl;
    //    j++;
    //}

    // 示例三
    for (int i = 0; i < 3; i++) {
        foo();
    }
}

// 输出结果：
// x: 0034FA30  1
// x: 0034FA30  1
// x: 0034FA30  1
```

为什么三次调用 foo，三次的 x 内存地址都是相同的？

我们再看看示例四。

---

# 示例四

```cpp
#include <iostream>
#include <vector>
using namespace std;

//void foo() {
//    int x = 1;
//    cout << "x: " << &x << "  " << x << endl;
//}

void foo2(int val) {
    int y = val;
    cout << "y: " << &y << "  " << y << endl;
}

void foo3(int val) {
    int z = val;
    cout << "z: " << &z << "  " << z << endl;
}

int main() {
    // 示例一
    //for (int i = 0; i < 3; i++) {
    //    int a = 0;
    //    cout << &a << "  " << a << endl;
    //}

    // 示例二
    //vector<int> v;
    //v.push_back(0);
    //v.push_back(1);
    //v.push_back(2);
    //v.push_back(3);
    //v.push_back(4);

    //for (int& i : v) {
    //    cout << "   i: " << &i << "  " << i << endl;

    //    static int j = 0;
    //    cout << "v[" << j << "]: " << &v[j] << "  " << v[j] << endl << endl;
    //    j++;
    //}

    // 示例三
    //for (int i = 0; i < 3; i++) {
    //    foo();
    //}

    // 示例四
    foo2(7);
    foo3(11);
}

// 输出结果：
// y: 0045F72C  7
// z: 0045F72C  11
```

为什么调用不同的函数 foo2, foo3，各自不同的局部变量 y, z 的地址也一样？

详见 stackoverflow [这篇问答](http://stackoverflow.com/questions/24117002/same-address-for-different-variables-of-different-functions-in-c)。

相关资料：《[C++ 变量存储方式](http://syawlaus.github.io/blog/c++/ways-of-storing-variables/)》
