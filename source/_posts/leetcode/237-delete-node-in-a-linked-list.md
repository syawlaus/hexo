title: Leetcode 题解 - 237. Delete Node in a Linked List
date: 2016-06-29 13:00
categories: Leetcode
---

# 题目

编写一个函数，删除单向链表的一个结点（除了末尾结点），只给出该结点的地址。

<!-- more -->

**示例**：

假定链表为 1 -> 2 -> 3 -> 4，并给出值为 3 的第三个结点的地址，在调用了你的函数后，链表应该为 1 -> 2 -> 4。

**难度**：容易

**编程语言**：C++

---

# 分析

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
    void deleteNode(ListNode* node) {

    }
};
```

题目要求删除的结点不包括末尾结点，即可能是首结点或中间结点。

在示例里，要删除 3，需要知道 2 的地址，才能把 2 的 next 指向 4。所以要先遍历整个单向链表，找出 node 的前一个结点的地址。但我们只知道 3 的地址，不知道链表首结点的地址，无法遍历链表。所以要想其它办法。

我想到一个办法是，不删除 3，但把 3 val 用 4 的 val 覆盖掉，然后 4 的 val 用后一个结点的 val 覆盖掉，直到末尾元素。即：

          only access
              ↓
    1 -> 2 -> 3 -> 4 -> NULL

         override  override
              ↓     ↓
    1 -> 2 -> 4 -> NULL

因为 deleteNode 只删除一个元素，所以当到达末尾结点（即 node->next 为 NULL）时结束，node 置为 NULL。

伪代码如下：

```
void deleteNode(ListNode* node) {
    while (node->next != NULL) {
        // 用后一个结点的 val 覆盖 node 的 val
        node->val = node->next->val;

        // 往后移动 node
        node = node->next;
    }

    // 到达末尾元素，置为 NULL
    node = NULL;
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
    void deleteNode(ListNode* node) {
        while (node->next != NULL) {
            // 用后一个结点的 val 覆盖 node 的 val
            node->val = node->next->val;
    
            // 往后移动 node
            node = node->next;
        }

        // 到达末尾元素，置为 NULL
        node = NULL;
    }
};

void printSingleLinkedList(ListNode* head) {
    while (head != NULL) {
        cout << "address: " << head << endl;
        cout << "    val: " << head->val << endl;
        cout << "   next: " << head->next << endl;
        head = head->next;
    }
    cout << "-------------------" << endl;
}

int main() {
    ListNode node0(0);
    ListNode node1(1);

    node0.next = &node1;
    node1.next = NULL;

    // 输出原链表
    printSingleLinkedList(&node0);

    Solution sol;
    sol.deleteNode(&node0);

    // 输出结果链表
    printSingleLinkedList(&node0);
}

// 输出结果：
// address: 003CFE94
//     val: 0
//    next: 003CFE84
// address: 003CFE84
//     val: 1
//    next: 00000000
// -------------------
// address: 003CFE94
//     val: 1
//    next: 003CFE84
// address: 003CFE84
//     val: 1
//    next: 00000000
-------------------
```

从输出结果看到，Wrong Answer 了。

上面代码的问题是，23 行 node = NULL 时，只是把 node 这个指针的值置为 0x00000000（NULL）而已，而 node0->next 还是 node1，地址为 003CFE84。我们需要的结果是，node1 的地址是 0x00000000。

因为 deleteNode 函数是删除一个非末尾的结点，所以删除的范围是 [首结点, 倒数第二个结点]。我的思路还是不变，首结点地址一直到倒数第二个结点的地址都不变，删除 node 只是用 node 的后一个结点 val 覆盖。但需要把倒数第二个结点的 next 设为 NULL。

伪代码如下：

```
void deleteNode(ListNode* node) {
    if (node == NULL) {
        return;
    }

    while (node->next != NULL) {
        // 用后一个结点的 val 覆盖当前 node 的 val
        node->val = node->next->val;

        if (当前 node 是倒数第二个结点) {
            node->next = NULL;
            break;
        }

        // 往后移动当前 node
        node = node->next;
    }
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
    void deleteNode(ListNode* node) {
        if (node == NULL) {
            return;
        }

        while (node->next != NULL) {
            // 用后一个结点的 val 覆盖当前 node 的 val
            node->val = node->next->val;

            // 如果当前 node 是倒数第二个结点
            if (node->next->next == NULL) {
                node->next = NULL;
                break;
            }

            // 往后移动当前 node
            node = node->next;
        }
    }
};

void printSingleLinkedList(ListNode* head) {
    while (head != NULL) {
        cout << "address: " << head << endl;
        cout << "    val: " << head->val << endl;
        cout << "   next: " << head->next << endl;
        head = head->next;
    }
    cout << "-------------------" << endl;
}

int main() {
    ListNode node0(0);
    ListNode node1(1);
    ListNode node2(2);
    ListNode node3(3);
    ListNode node4(4);
    ListNode node5(5);

    node0.next = &node1;
    node1.next = &node2;
    node2.next = &node3;
    node3.next = &node4;
    node4.next = &node5;
    node5.next = NULL;

    // 输出原链表
    printSingleLinkedList(&node0);

    Solution sol;
    sol.deleteNode(&node0);

    // 输出结果链表
    printSingleLinkedList(&node0);
}

// 输出结果：
// address: 003AF8CC
//     val: 0
//    next: 003AF8BC
// address: 003AF8BC
//     val: 1
//    next: 003AF8AC
// address: 003AF8AC
//     val: 2
//    next: 003AF89C
// address: 003AF89C
//     val: 3
//    next: 003AF88C
// address: 003AF88C
//     val: 4
//    next: 003AF87C
// address: 003AF87C
//     val: 5
//    next: 00000000
// -------------------
// address: 003AF8CC
//     val: 1
//    next: 003AF8BC
// address: 003AF8BC
//     val: 2
//    next: 003AF8AC
// address: 003AF8AC
//     val: 3
//    next: 003AF89C
// address: 003AF89C
//     val: 4
//    next: 003AF88C
// address: 003AF88C
//     val: 5
//    next: 00000000
// -------------------
```

提交到 Leetcode，Accepted! :) 运行时间为 16ms。

我也明白了为什么题目要删除的结点是不包括末尾结点，因为末尾结点 tail->next == NULL，在调用上面代码 23 行

    if (node->next->next == NULL)

判断时会越界崩溃。
