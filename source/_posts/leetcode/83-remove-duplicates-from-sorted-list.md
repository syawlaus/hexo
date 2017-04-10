title: Leetcode 题解 - 83. Remove Duplicates from Sorted List
date: 2016-07-22 19:00
categories: Leetcode
---

#题目

给定一个有序链表，删除所有重复的元素，使得每个元素只出现一次。

<!-- more -->

**示例**：

给定 1 -> 1 -> 2，返回 1 -> 2.
给定 1 -> 1 -> 2 -> 3 -> 3，返回 1 -> 2 -> 3。

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(NULL) {}
};

class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {

    }
};
```

我的思路是，遍历链表的所有元素，每遍历一个元素，判断该元素值是否跟下一个元素值相同，如果相同，则删除下一个元素。

伪代码如下：

```
ListNode* deleteDuplicates(ListNode* head) {
    ListNode* temp = head;
    while (temp != NULL) {
        if (temp->next != NULL &&
            temp->val == temp->next->val) {

            // 删除 temp->next
            temp->next = temp->next->next;

            // 清空重复元素占用的内存

        }

        // 后移 temp
        temp = temp->next;
    }
    return head;
}
```

提交到 Leetcode，Wrong Answer! 对于输入 [1, 1, 1]，正确的应该是输出 [1]，我的函数输出了 [1, 1]。

原因是，不管 temp->val 是否等于 temp->next->val，在 if 处理后都会 temp = temp->next，所以实际上只删除了奇数个重复元素。即是对于输入 [1, 1, 1, 1, 1]，我的函数会输出 [1, 1, 1]，仅把第 2、4 个 1 删除。

上面的整个思路都错了，或者说没有想完整。应该是每遍历一个元素，判断该元素值是否跟下一个元素值相同，如果相同，则删除下一个元素。直到发现下一个元素值跟本元素值不一样，本元素的指针才后移一位。

伪代码如下：

```
ListNode* deleteDuplicates(ListNode* head) {
    ListNode* temp = head;
    while (temp != NULL) {
        if (temp->next != NULL) {
            if (temp->val == temp->next->val) {
                temp->next = temp->next->next;

                // 清空重复元素占用的内存
            }
            else {
                temp = temp->next;
            }
        }
        else {
            return head;
        }
    }
    return head;
}
```

代码如下：

```cpp
#include <iostream>
using namespace std;

// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(NULL) {}
};

class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode* temp = head;
        while (temp != NULL) {
            if (temp->next != NULL) {
                if (temp->val == temp->next->val) {
                    temp->next = temp->next->next;

                    // 清空重复元素占用的内存
                }
                else {
                    temp = temp->next;
                }
            }
            else {
                return head;
            }
        }
        return head;
    }
};

void printLinkedlist(ListNode* head) {
    while (head != NULL) {
        cout << head->val << ' ';
        head = head->next;
    }
    cout << endl;
}

int main() {
    // init data
    ListNode n1(1);
    ListNode n2(1);
    ListNode n3(1);
    ListNode n4(1);
    ListNode n5(1);

    n1.next = &n2;
    n2.next = &n3;
    n3.next = &n4;
    n4.next = &n5;
    printLinkedlist(&n1);

    // solution
    Solution sol;
    sol.deleteDuplicates(&n1);
    printLinkedlist(&n1);
}

// 输出结果：
// 1 1 1 1 1
// 1
```

提交到 Leetcode，Accepted! :) 运行时间为 16ms。

另外一个经验是，尽量不要 if 里有多重判断，每个 if 只判断一个条件。每个 if 都考虑 else 分支。
