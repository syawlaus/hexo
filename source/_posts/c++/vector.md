title: C++ vector
date: 2016-07-05 16:00
categories: C++
---

本文意在整理 C++ vector 的用法，和需要注意的地方。

<!--- more -->

# include

在 C++ 程序中，使用 vector 要 `#include <vector>`。

---

# 定义 vector

vector 是 STL 里一种类似链表的数据结构。

    vector<Type> v;             // 定义 vector v，类型为 Type
    vector<Type> v(10);         // 定义 vector v，类型为 Type，大小为 10

---

# 初始化

统一初始化：

    vector<Type> v(10, val);    // 定义 vector v，类型为 Type，大小为 10，全部元素初始化为 val

使用数组进行初始化：

    int a[] = { 1, 2, 3, 4, 5, 6, 7 };
    int len = sizeof(a) / sizeof(a[0]);
    vector<int> v(a, a + len);

---

# 插入元素

往 vector 尾插入元素：

    v.push_back(element);           // 方式一
    v.insert(v.end(), element);     // 方式二

往 vector 头插入元素：

    v.insert(v.begin(), element);

往 vector 中间某 index 插入元素：

    int index = X;
    v.insert(v.begin() + index, element);

---

# 访问元素

访问第 index 个元素（index 从 0 开始）：

    v[index]

---

# 删除元素

按 index 删除：

    int index = X;
    v.erase(v.begin() + index);

按 value 删除：

    for (vector<int>::iterator iter = nums.begin(); iter != nums.end(); ) {
        if (*iter == val) {
            iter = nums.erase(iter);    // erase 返回删除元素的后一个元素的迭代器
        }
        else {
            iter++;
        }
    }

---

# 遍历元素

遍历方式一：

    for(int i = 0; i < v.size(); i++) {
        // 操作 v[i]
    }

遍历方式二：

    for (Type element : v) {
        // 操作 element
    }

遍历方式三：

    for(vector<Type>::iterator iter = v.begin(); iter != v.end(); iter++) {
        // 操作 (*iter)
    }

---

# 示例代码

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

void printVector(vector<int>& v, string str) {
    cout << str << endl;
    cout << "size = " << v.size() << " | ";
    for (int i : v) {
        cout << i << ' ';
    }
    cout << endl << "--------------------" << endl;
}

int main() {
    // 初始化
    int a[] = { 1, 2, 3, 4, 5, 6, 7 };
    int len = sizeof(a) / sizeof(a[0]);
    vector<int> v(a, a + len);
    printVector(v, "初始化");

    // 尾插入元素
    v.push_back(8);
    v.insert(v.end(), 9);
    printVector(v, "尾插入元素");

    // 头插入元素
    v.insert(v.begin(), 0);
    printVector(v, "头插入元素");

    // 中间插入元素
    int index = 3;
    v.insert(v.begin() + index, 33);
    printVector(v, "中间插入元素");

    // 遍历
    for (vector<int>::iterator iter = v.begin(); iter != v.end(); iter++) {
        // 操作 iter
        (*iter)++;
    }
    printVector(v, "遍历元素");

    // 删除元素
    index = 3;
    v.erase(v.begin() + index);
    printVector(v, "删除元素");

}

// 输出结果：
// 
// 初始化
// size = 7 | 1 2 3 4 5 6 7
// --------------------
// 尾插入元素
// size = 9 | 1 2 3 4 5 6 7 8 9
// --------------------
// 头插入元素
// size = 10 | 0 1 2 3 4 5 6 7 8 9
// --------------------
// 中间插入元素
// size = 11 | 0 1 2 33 3 4 5 6 7 8 9
// --------------------
// 遍历元素
// size = 11 | 1 2 3 34 4 5 6 7 8 9 10
// --------------------
// 删除元素
// size = 10 | 1 2 3 4 5 6 7 8 9 10
// --------------------
```
