title: C++ struct
date: 2016-07-10 17:35
categories: C++
---

本文意在整理 C++ struct 的用法，和需要注意的地方。

<!-- more -->

见下面的示例代码：

```cpp
#include <iostream>
#include <string>
using namespace std;

// 声明
struct Student {
    string name;
    int age;
    double finalExamScore;
};

void printStudent(Student& stu) {
    // 访问 struct 成员
    cout << stu.name << endl;
    cout << stu.age << endl;
    cout << stu.finalExamScore << endl;
    cout << endl;
}

int main() {
    // 定义 & 初始化
    Student stu = { "Zhang San", 16, 91.5 };
    printStudent(stu);

    // 动态创建实例指针
    Student* stu2 = new Student();
    stu2->name = "Li Si";
    stu2->age = 17;
    stu2->finalExamScore = 92.5;
    printStudent(*stu2);
}
```

输出结果：

```
Zhang San
16
91.5

Li Si
17
92.5
```

带构造的 struct：

```cpp
// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(NULL) {}
};
```
