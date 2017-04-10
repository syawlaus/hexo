title: Leetcode 题解 - 24. Swap Nodes in Pairs
date: 2016-07-27 18:10
categories: Leetcode
---

#题目

给定一个链表，交换每两个相邻节点，并返回链表首节点。

<!-- more -->

**示例**：

给定 1 -> 2 -> 3 -> 4，你的函数应返回 2 -> 1 -> 4 ->3。

你的算法应该只用常数（即 O(1)）空间。你不能修改链表里的节点值，只能操作节点。

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
    ListNode* swapPairs(ListNode* head) {

    }
};
```

首先画出原链表和 swap-pairs 后的链表：

原链表：

     val  next
    +---------+    +---------+    +---------+    +----------+
    | 1 | 200 |    | 2 | 300 |    | 3 | 400 |    | 4 | NULL |
    +---------+    +---------+    +---------+    +----------+
    |   100   |    |   200   |    |   300   |    |   400    |
    +---------+    +---------+    +---------+    +----------+
      address

swap-pairs 后的链表：

     val  next
    +---------+    +---------+    +---------+    +----------+
    | 2 | 100 |    | 1 | 400 |    | 4 | 300 |    | 3 | NULL |
    +---------+    +---------+    +---------+    +----------+
    |   200   |    |   100   |    |   400   |    |   300    |
    +---------+    +---------+    +---------+    +----------+
      address

接下来列出 swap-pair 的具体执行过程：

* head = 200
* 200->next = 100

上面两步完成了第一次 swap-pair。

下面是 swap 1, 2 后的图：

     val  next
    +---------+    +---------+    +---------+    +----------+
    | 2 | 100 |    | 1 | 300 |    | 3 | 400 |    | 4 | NULL |
    +---------+    +---------+    +---------+    +----------+
    |   200   |    |   100   |    |   300   |    |   400    |
    +---------+    +---------+    +---------+    +----------+
      address

接下来我们 swap 后面两个节点：

* 400->next = 300
* 300->next = NULL

上面两步完成了第二次 swap-pair。

下面是 swap 3, 4 后的图：

     val  next
    +---------+    +---------+    +---------+    +----------+
    | 2 | 100 |    | 1 | 300 |    | 4 | 300 |    | 3 | NULL |
    +---------+    +---------+    +---------+    +----------+
    |   200   |    |   100   |    |   400   |    |   300    |
    +---------+    +---------+    +---------+    +----------+
      address

可以看到，现在链表变为 2 -> 1 -> 3 -> NULL。

所以我们应该在 swap 1, 2 后，记录第二个节点（即 1）为 tempHead，用于下一次 swap-pair 时建立连接。

我们进一步补充整个 swap-pairs 的执行过程：

* head = 200
* 200->next = 100
* tempHead = 100    // tempHead 作为下一次 swap-pair 的第一个节点的 head
* 400->next = 300
* tempHead = 400
* 300->next = NULL

把上面的过程放到一个 while 循环里，每次循环要完成的工作是 swap first, second 这两个节点，而且保存 first 作为下一次 swap 的第一个节点的 head。

伪代码如下：

```
// 记录两个地址
ListNode* head = second

// swap first, second
second->next = first

// 保存 first 作为下一次 swap 的第一个节点的 head
ListNode* tempHead = first

// head 后移两步
head = head->next->next
```

现在再放到 while 循环里：

```
traverseHead->next = head;  // traverseHead 是用来遍历链表里，作为 swap a, b 的第一个节点的 head
while (head != NULL) {
    ListNode* first = head;
    ListNode* second = head->next;
    ListNode* third = NULL;
    if (second != NULL) {
        third = second->next;

        // 原来：traverseHead -> first -> second -> third
        // 任务 ：traverseHead -> second -> first -> third
        traverseHead->next = second;
        second->next = first;
        first->next = third;

        // second -> first (= traverseHead)
        traverseHead = first;

        // head 后移一位（因为 first = head，现在 first 后移了一位，所以 head 只需要再后移一位即可）
        head = head->next;
    }
    else {
        break;
    }
}
```

整个程序的代码如下：

```cpp
#include <iostream>
#include <vector>
using namespace std;

#define ARRAY_LENGTH(a) (sizeof(a) / sizeof(a[0]))

// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(NULL) {}
};

class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (head == NULL) {
            return NULL;
        }

        // 在 head 前面加一个 traverseHead，用来循环时更新 head 的前一个节点
        ListNode tempNode(INIT_VAL);
        ListNode* traverseHead = &tempNode;
        traverseHead->next = head;

        // 用来保存最终结果链表的首元素
        ListNode* retHead = traverseHead;

        while (head != NULL) {
            ListNode* first = head;
            ListNode* second = head->next;
            ListNode* third = NULL;
            if (second != NULL) {
                third = second->next;

                // 原来：traverseHead -> first -> second -> third
                // 任务 ：traverseHead -> second -> first -> third
                traverseHead->next = second;
                second->next = first;
                first->next = third;

                // second -> first (= traverseHead)
                traverseHead = first;

                // head 后移一位（因为 first = head，现在 first 后移了一位，所以 head 只需要再后移一位即可）
                head = head->next;
            }
            else {      // 到达末节点
                break;
            }
        }

        // 返回 head
        return retHead->next;
    }

private:
    const int INIT_VAL = -1;
};

void printList(ListNode* head) {
    while (head != NULL) {
        cout << head->val << ' ';
        head = head->next;
    }
    cout << endl;
}

ListNode* convertArrayToList(int* arr, int len) {
    // 用 vector 保存所有指针
    vector<ListNode*> nodes;
    for (int i = 0; i < len; i++) {
        ListNode* node = new ListNode(arr[i]);      // 新建 ListNode*
        nodes.push_back(node);
    }

    // 对 vector 所有指针建立 ->next 关系
    for (int i = 0; i < nodes.size() - 1; i++) {
        nodes[i]->next = nodes[i + 1];
    }

    return nodes[0];
}

int main() {
    // 测试一
    Solution sol;
    int arr1[] = { 1 };
    ListNode* list1 = convertArrayToList(arr1, ARRAY_LENGTH(arr1));
    printList(sol.swapPairs(list1));

    // 测试二
    int arr2[] = { 1, 2, 3, 4 };
    ListNode* list2 = convertArrayToList(arr2, ARRAY_LENGTH(arr2));
    printList(sol.swapPairs(list2));

    // 测试三
    int arr3[] = { 1, 2, 3, 4, 5 };
    ListNode* list3 = convertArrayToList(arr3, ARRAY_LENGTH(arr3));
    printList(sol.swapPairs(list3));
}

// 输出结果：
// 1
// 2 1 4 3
// 2 1 4 3 5
```

把 Solution 提交到 Leetcode，Accepted! :) 运行时间为 4ms。
