title: Leetcode 题解 - 206. Reverse Linked List
date: 2016-07-06 17:00
categories: Leetcode
---

#题目

反转一个单向链表。

<!-- more -->

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
    ListNode* reverseList(ListNode* head) {

    }
};
```

我们先把问题具体化，比如现在有一个单向链表 1 -> 2 -> 3 -> 4 -> 5，要反转为 5 -> 4 -> 3 -> 2 -> 1。

单向链表在内存中的存储见下图：

                    +---+-----+    +---+-----+    +---+-----+    +---+-----+    +---+------+
    value / *next   | 1 | 102 |    | 2 | 103 |    | 3 | 104 |    | 4 | 105 |    | 5 | NULL |
                    +---+-----+    +---+-----+    +---+-----+    +---+-----+    +---+------+
    adress             101            102            103            104            105

反转为 5 -> 4 -> 3 -> 2 -> 1 后，在内存中的存储见下图：

                    +---+------+    +---+-----+    +---+-----+    +---+-----+    +---+-----+
    value / *next   | 1 | NULL |    | 2 | 101 |    | 3 | 102 |    | 4 | 103 |    | 5 | 104 |
                    +---+------+    +---+-----+    +---+-----+    +---+-----+    +---+-----+
    adress             101             102            103            104            105

也可以是：

                    +---+-----+    +---+-----+    +---+-----+    +---+-----+    +---+------+
    value / *next   | 5 | 102 |    | 4 | 103 |    | 3 | 104 |    | 2 | 105 |    | 1 | NULL |
                    +---+-----+    +---+-----+    +---+-----+    +---+-----+    +---+------+
    adress             101            102            103            104            105

所以我们分开两个方法写，一是反转指针，二是反转值。

##方法一：反转指针

反转指针，需要用一个 vector 保存所有元素的地址。用上图的示例，即是 vector 保存 [NULL, 101, 102, 103, 104]，然后把：

* 105 的 *next 设为 104
* 104 的 *next 设为 103
* 103 的 *next 设为 102
* 102 的 *next 设为 101
* 101 的 *next 设为 NULL

伪代码如下：

```
ListNode* reverseList(ListNode* head) {
    
    if (head == NULL) {
        return NULL;
    }

    // 把各个节点地址添加到 vector
    vector<ListNode*> nodes;
    nodes.push_back(NULL);  // 作为首元素在反转后的 *next
    while (head->next != NULL) {
        nodes.push_back(head);
        head = head->next;
    }

    // 此时 head->next == NULL，即是到达末尾元素
    ListNode* tail = head;  // 用来返回的
    for (int i = nodes.size() - 1; i >=0; i--) {
        head->next = nodes[i];
        head = head->next;
    }

    return tail;
}
```

代码如下：

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(NULL) {}
};

void printLinkedlist(ListNode* head) {
    while (head != NULL) {
        cout << head->val << " ";
        head = head->next;
    }
    cout << endl;
}

class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (head == NULL) {
            return head;
        }

        // 把各个节点地址添加到 vector
        vector<ListNode*> nodes;
        nodes.push_back(NULL);  // 作为首元素在反转后的 *next
        while (head->next != NULL) {
            nodes.push_back(head);
            head = head->next;
        }

        // 此时 head->next == NULL，即是到达末尾元素
        ListNode* tail = head;  // 用来返回的
        for (int i = nodes.size() - 1; i >= 0; i--) {
            head->next = nodes[i];
            head = head->next;
        }

        return tail;
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

    printLinkedlist(&n1);

    Solution sol;
    ListNode* head = sol.reverseList(&n1);
    printLinkedlist(head);
}

// 输出结果：
// 1 2 3 4 5
// 5 4 3 2 1
```

提交到 Leetcode，Accepted! :) 运行时间为 8ms。

##方法二：反转值

反转值的思路是，不改变指针的指向，只反转各个节点的 val。

* 先遍历整个 Linkedlist，把各个节点的 val 保存到 vector。
* 再遍历一次 Linkedlist，把 vector 的 val 倒序赋值到各个节点就可以了。

伪代码如下：

```
ListNode* reverseList(ListNode* head) {
    if (head == NULL) {
        return NULL;
    }

    // 遍历整个 Linkedlist，把各个节点的 val 保存到 vector
    vector<int> nodeVals;
    ListNode* tempHead = head;
    while (tempHead != NULL) {
        nodeVals.push_back(tempHead->val);
        tempHead = tempHead->next;
    }

    // 遍历一次 Linkedlist，把 vector 的 val 倒序赋值到各个节点
    tempHead = head;
    int lastIdx = nodeVals.size() - 1;
    while (tempHead != NULL) {
        tempHead->val = nodeVals[lastIdx];
        lastIdx--;
        tempHead = tempHead->next;
    }

    return head;
}
```

代码如下：

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(NULL) {}
};

void printLinkedlist(ListNode* head) {
    while (head != NULL) {
        cout << head->val << " ";
        head = head->next;
    }
    cout << endl;
}

class Solution {
public:
    // 方法一：反转指针
    //ListNode* reverseList(ListNode* head) {
    //    if (head == NULL) {
    //        return NULL;
    //    }

    //    // 把各个节点地址添加到 vector
    //    vector<ListNode*> nodes;
    //    nodes.push_back(NULL);  // 作为首元素在反转后的 *next
    //    while (head->next != NULL) {
    //        nodes.push_back(head);
    //        head = head->next;
    //    }

    //    // 此时 head->next == NULL，即是到达末尾元素
    //    ListNode* tail = head;  // 用来返回的
    //    for (int i = nodes.size() - 1; i >= 0; i--) {
    //        head->next = nodes[i];
    //        head = head->next;
    //    }

    //    return tail;
    //}

    // 方法二：反转值
    ListNode* reverseList2(ListNode* head) {
        if (head == NULL) {
            return NULL;
        }

        // 遍历整个 Linkedlist，把各个节点的 val 保存到 vector
        vector<int> nodeVals;
        ListNode* tempHead = head;
        while (tempHead != NULL) {
            nodeVals.push_back(tempHead->val);
            tempHead = tempHead->next;
        }

        // 遍历一次 Linkedlist，把 vector 的 val 倒序赋值到各个节点
        tempHead = head;
        int lastIdx = nodeVals.size() - 1;
        while (tempHead != NULL) {
            tempHead->val = nodeVals[lastIdx];
            lastIdx--;
            tempHead = tempHead->next;
        }

        return head;
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

    printLinkedlist(&n1);

    Solution sol;
    ListNode* head;

    //head = sol.reverseList(&n1);
    //printLinkedlist(head);

    head = sol.reverseList2(&n1);
    printLinkedlist(head);
}

// 输出结果：
// 1 2 3 4 5
// 5 4 3 2 1
```

提交到 Leetcode，Accepted! :) 运行时间为 8ms。
