title: Leetcode 题解 - 141. Linked List Cycle
date: 2016-07-25 11:30
categories: Leetcode
---

# 题目

给定一个链表，判断是否循环链表。

<!-- more -->

**后续**：

你的解决方案能够不使用额外的（内存）空间吗？

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```
// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(NULL) {}
};

class Solution {
public:
    bool hasCycle(ListNode* head) {
    
    }
};
```

最简单的思路是：

* 使用一个 map 记录所有链表节点
* 每遍历一个节点，检查 map 是否已存在该节点
    * 如果已存在，说明是循环链表
    * 如果不存在，添加该节点到 map
* 一直遍历链表节点，直到出现循环，或到达链表尾

伪代码如下：

```
bool hasCycle(ListNode* head) {
    map<ListNode*, bool> mapp;
    while (head != NULL) {
        if (mapp.find(head) != mapp.end()) {  // head 已存在
            return true;
        }
        mapp[head] = true;
        head = head->next;
    }
    return false;
}
```

代码如下：

```cpp
#include <iostream>
#include <map>
using namespace std;

// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(NULL) {}
};

class Solution {
public:
    bool hasCycle(ListNode* head) {
        map<ListNode*, bool> mapp;
        while (head != NULL) {
            if (mapp.find(head) != mapp.end()) {  // head 已存在
                return true;
            }
            mapp[head] = true;
            head = head->next;
        }
        return false;
    }
};

int main() {
    ListNode n1(1);
    ListNode n2(2);
    ListNode n3(3);
    ListNode n4(4);
    ListNode n5(5);
    ListNode n6(6);
    ListNode n7(7);

    n1.next = &n2;
    n2.next = &n3;
    n3.next = &n4;
    n4.next = &n5;
    n5.next = &n6;
    n6.next = &n1;

    Solution sol;
    cout << sol.hasCycle(&n1) << endl;
}

// 输出结果：
// 1
```

提交到 Leetcode，Accepted! :) 运行时间为 60ms。

---

# 后续

> 你的解决方案能够不使用额外的（内存）空间吗？

我实在想不到 O(1) 空间复杂度的算法，参考[这篇文章](http://blog.csdn.net/ebowtang/article/details/50507131)的思路：

* 用两个指针 pi, pi2 遍历链表，pi 每次走一步，pi2 每次走两步。
* 如果 pi2 先走到链表尾，说明不是循环链表。
* 如果 pi2（走了一圈后）与 pi相遇，说明是循环链表。

代码如下：

```cpp
#include <iostream>
#include <map>
using namespace std;

// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(NULL) {}
};

class Solution {
public:
    // O(n) 空间复杂度
    //bool hasCycle(ListNode* head) {
    //    map<ListNode*, bool> mapp;
    //    while (head != NULL) {
    //        if (mapp.find(head) != mapp.end()) {  // head 已存在
    //            return true;
    //        }
    //        mapp[head] = true;
    //        head = head->next;
    //    }
    //    return false;
    //}

    // O(1) 空间复杂度
    bool hasCycle2(ListNode* head) {
        if (head == NULL) {
            return false;
        }

        ListNode* pi = head;
        ListNode* pi2 = head;
        while (true) {
            // pi  前进一步
            if (pi->next != NULL) {
                pi = pi->next;
            }
            else {
                return false;
            }

            // pi2 前进两步
            if (pi2->next != NULL) {
                if (pi2->next->next != NULL) {
                    pi2 = pi2->next->next;
                }
                else {
                    return false;
                }
            }
            else {
                return false;
            }

            // pi 与 pi2 相遇
            if (pi == pi2) {
                return true;
            }
        }
    }
};

int main() {
    ListNode n1(1);
    ListNode n2(2);
    ListNode n3(3);
    ListNode n4(4);
    ListNode n5(5);

    n1.next = &n2;
    n2.next = &n3;
    n3.next = &n4;
    n4.next = &n5;
    n5.next = &n1;

    Solution sol;
    cout << sol.hasCycle2(&n1) << endl;
}

// 输出结果：
// 1
```

提交到 Leetcode，Accepted! :) 运行时间为 12ms。
