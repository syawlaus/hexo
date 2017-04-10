title: Leetcode 题解 - 21. Merge Two Sorted Lists
date: 2016-07-25 17:50
categories: Leetcode
---

#题目

合并两个有序链表并返回一个新链表。新链表由两个有序链表的节点共同组成。

<!-- more -->

**难度**：容易

**编程语言**：C++

---

#分析

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
    ListNode* mergeTwoLists(ListNode* L1, ListNode* L2) {

    }
};
```

mergeTwoLists 就是《[合并排序](http://syawlaus.github.io/blog/data-structures-and-algorithms/sorting/merge-sort/)》里的合并操作。本文不讨论合并排序，仅讨论其中的合并操作。

设 L1 = [1, 2, 4, 6, 8]，L2 = [0, 3, 5, 7, 9]，合并的思路是：

    L2 = [1, 2, 4, 6, 8]
          ↑
          i1

    L2 = [0, 3, 5, 7, 9]
          ↑
          i2

    L = []

比较 L1[i1] 和 L2[i2] 的值，因为 L2[i2] 较小，所以添加 L2[i2] 到 L，i2 后移一位：

    L1 = [1, 2, 4, 6, 8]
          ↑
          i1

    L2 = [0, 3, 5, 7, 9]
             ↑
             i2
              
    L = [0]

比较 L1[i1] 和 L2[i2] 的值，因为 L1[i1] 较小，所以添加 L1[i1] 到 L，i1 后移一位：

    L1 = [1, 2, 4, 6, 8]
             ↑
             i1

    L2 = [0, 3, 5, 7, 9]
             ↑
             i2
              
    L = [0, 1]

一直重复这个过程，直到遍历完 L1 或 L2 的全部元素。在这个例子里，L1 会先遍历完，此时 L2 未遍历的元素只剩下 9：

    L1 = [1, 2, 4, 6, 8]
                         ↑
                         i1 (完成遍历）

    L2 = [0, 3, 5, 7, 9]
                      ↑
                      i2
              
    L = [0, 1, 2, 3, 4, 5, 6, 7, 8]

L2 剩下的全部元素，肯定比 L 中全部元素都大，且本身也有序，所以只要按顺序添加到 L 即可：

    L = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

接下来编写伪代码。因为需要遍历 L1 和 L2，直到其中一个序列遍历完毕，所以需要一个 while 循环，while 循环之后，再把未遍历完毕的序列的剩下元素全部添加到 L：

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
    ListNode* mergeTwoLists(ListNode* h1, ListNode* h2) {

        if (h1 == NULL && h2 == NULL) {
            return NULL;
        }

        ListNode* L = new ListNode(INIT_VAL);   // 实例化 ListNode 对象
        ListNode* head = L;

        // 合并 h1 和 h2 到 L
        while (true) {
            if (h1 == NULL || h2 == NULL) {
                break;
            }

            int val1 = h1->val;
            int val2 = h2->val;

            if (val1 < val2) {
                L->val = val1;
                h1 = h1->next;
            }
            else {
                L->val = val2;
                h2 = h2->next;
            }
            newListNodeAndMoveForward(L);
        }

        // 添加 h2 剩余元素到 L
        if (h1 == NULL) {
            addRemaining(h2, L);
        }
        // 添加 h1 剩余元素到 L
        else {      // 即 h2 == NULL
            addRemaining(h1, L);
        }

        return head;
    }

private:
    int INIT_VAL = -1;

    void newListNodeAndMoveForward(ListNode*& L) {
        ListNode* nextNode = new ListNode(INIT_VAL);
        L->next = nextNode;
        L = L->next;
    }

    void addRemaining(ListNode*& h, ListNode*& L) {
        while (h != NULL) {
            L->val = h->val;
            h = h->next;
            if (h != NULL) {   // 下一个节点不为 NULL 时才 new ListNode
                newListNodeAndMoveForward(L);
            }
        }
    }
};

void printList(ListNode* h) {
    while (h != NULL) {
        cout << h->val << ' ';
        h = h->next;
    }
    cout << endl;
}

int main() {
    // h1
    ListNode n1(1);
    ListNode n2(2);
    ListNode n4(4);
    ListNode n6(6);
    ListNode n8(8);

    n1.next = &n2;
    n2.next = &n4;
    n4.next = &n6;
    n6.next = &n8;

    printList(&n1);

    // h2
    ListNode n0(0);
    ListNode n3(3);
    ListNode n5(5);
    ListNode n7(7);
    ListNode n9(9);

    n0.next = &n3;
    n3.next = &n5;
    n5.next = &n7;
    n7.next = &n9;

    printList(&n0);

    // merge h1, h2
    Solution sol;
    ListNode* L = sol.mergeTwoLists(&n1, &n0);
    printList(L);
}

// 输出结果：
// 1 2 4 6 8
// 0 3 5 7 9
// 0 1 2 3 4 5 6 7 8 9
```

把 Solution 提交到 Leetcode，Accepted! :) 运行时间为 12ms。
