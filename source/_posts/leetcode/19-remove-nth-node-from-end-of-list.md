title: Leetcode 题解 - 19. Remove Nth Node From End of List
date: 2016-10-31 17:50
categories: Leetcode
---

#题目

给定一个链表，从链表尾移除第 n 个节点，并返回链表首节点。

<!-- more -->

**示例**：

    给定链表：1 -> 2 -> 3 -> 4 -> 5，和 n = 2。
    从链表尾移除第 2 个节点后，链表为 1 -> 2 -> 3 -> 5。

**注意**：

给定的 n 总是有效，尝试只使用一次遍历。

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode *head, int n) {
        
    }
};
```

一开始的思路是，先遍历链表，求出链表长度，算出需要移除哪个节点，再在第二次遍历时移除。如果只遍历一次，可以在第一次遍历时，把节点序号和指针记录到一个 `map<int, ListNode*>` 里，就能 O(1) 移除倒数第 n 个节点。

伪代码如下：

```
ListNode* removeNthFromEnd(ListNode *head, int n) {
    // 保存 head
    ListNode *saveHead = head;

    // 遍历链表，把序号和指针添加到 map 中
    map<int, ListNode*> orderMap;
    int order = 1;
    while (head != NULL) {
        addToMap(orderMap, order, head);
        order++;
        head = head->next;
    }
    int length = order - 1;

    // 获取需要移除的倒数第 n 个节点的指针
    int removeOrder = length - n + 1;
    ListNode *removeNode = getValue(orderMap, removeOrder);

    // 建立倒数第 n+1 个和倒数第 n-1 个节点的连接，即移除倒数第 n 个节点
    ListNode *prevRemoveNode = getValue(orderMap, removeOrder + 1);
    ListNode *nextRemoveNode = removeNode->next;
    prevRemoveNode->next = nextRemoveNode;
    removeNode = NULL;

    return saveHead;
}
```

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

// 功能：添加 <key, val> 到 map
// 参数：map, key, val
// 返回：void
void addToMap(map<int, ListNode*>& mapp,
              int key,
              ListNode* val) {
    if (key == NULL) {
        return;
    }

    map<int, ListNode*>::iterator iter = mapp.find(key);

    // 找不到 key，添加 <key, val>
    if (iter == mapp.end()) {
        mapp.insert(pair<int, ListNode*>(key, val));
    }
    // 找到 key，更新 val
    else {
        (iter->second) = val;
    }
}

class Solution {
public:
    ListNode* removeNthFromEnd(ListNode *head, int n) {
        if (head == NULL) {
            return NULL;
        }

        // 保存 head
        ListNode *saveHead = head;

        // 遍历链表，把序号和指针添加到 map 中
        map<int, ListNode*> orderMap;
        int order = 1;
        while (head != NULL) {
            addToMap(orderMap, order, head);
            order++;
            head = head->next;
        }
        int length = order - 1;

        // 获取需要移除的倒数第 n 个节点的指针
        int removeOrder = length - n + 1;
        ListNode *removeNode = orderMap[removeOrder];

        // 建立倒数第 n+1 个和倒数第 n-1 个节点的连接
        ListNode *prevRemoveNode = orderMap[removeOrder - 1];
        ListNode *nextRemoveNode = removeNode->next;
        if (prevRemoveNode != NULL) {
            prevRemoveNode->next = nextRemoveNode;
        }

        // 移除倒数第 n 个节点
        removeNode = NULL;

        return saveHead;
    }
};
```

提交到 Leetcode，Wrong Answer! 导致错误的输入是 [1] 1。即是链表为 [1]，移除倒数第 1 个节点。即是我的代码在移除 head 时出错。这是因为在上面代码第 50 行取 prevRemoveNode 时，removeOrder - 1 可能不存在，导致 prevRemoveNode 为 NULL。在 53 行建立与 nextRemoveNode 连接失败。

我们知道，链表中，除了 head，其它节点都有「上一个节点 prevNode」，这样在移除时，不得不对 head 作特殊判断。为了解决这个问题，统一 head 和其它节点的移除操作，我给 head 也设置一个 prevNode，即 prevHead。

代码如下：

```cpp
// 功能：添加 <key, val> 到 map
// 参数：map, key, val
// 返回：void
void addToMap(map<int, ListNode*>& mapp,
              int key,
              ListNode* val) {
    if (key == NULL) {
        return;
    }

    map<int, ListNode*>::iterator iter = mapp.find(key);

    // 找不到 key，添加 <key, val>
    if (iter == mapp.end()) {
        mapp.insert(pair<int, ListNode*>(key, val));
    }
    // 找到 key，更新 val
    else {
        (iter->second) = val;
    }
}

class Solution {
public:
    ListNode* removeNthFromEnd(ListNode *head, int n) {
        if (head == NULL) {
            return NULL;
        }

        // prevHead 用于记录 head 的入口，统一 head 和后面节点的操作，使得所有节点都有 prev 节点
        ListNode prevHeadNode(0);
        ListNode *prevHead = &prevHeadNode;
        prevHead->next = head;

        // 遍历链表，把序号和指针添加到 map 中
        map<int, ListNode*> orderMap;
        ListNode *tempNode = prevHead;
        int order = 1;
        while (tempNode != NULL) {
            addToMap(orderMap, order, tempNode);
            order++;
            tempNode = tempNode->next;
        }
        int length = order - 1;

        // 获取需要移除的倒数第 n 个节点的指针
        int removeOrder = length - n + 1;
        ListNode *removeNode = orderMap[removeOrder];

        // 建立倒数第 n+1 个和倒数第 n-1 个节点的连接
        ListNode *prevRemoveNode = orderMap[removeOrder - 1];
        ListNode *nextRemoveNode = removeNode->next;
        if (prevRemoveNode != NULL) {
            prevRemoveNode->next = nextRemoveNode;
        }

        // 移除倒数第 n 个节点
        removeNode = NULL;

        return prevHead->next;
    }
};

int main() {
    ListNode A(1);
    ListNode B(2);
    ListNode C(3);
    ListNode D(4);
    ListNode E(5);

    A.next = &B;
    B.next = &C;
    C.next = &D;
    D.next = &E;

    Solution sol;
    ListNode *head = sol.removeNthFromEnd(&A, 2);
    printList(head);
}

// 输出结果：
// 1 2 3 5
```

提交到 Leetcode，Accepted! :) 运行时间为 13ms。
