title: Leetcode 题解 - 234. Palindrome Linked List
date: 2016-11-08 13:00
categories: Leetcode
---

#题目

给定一个单向链表，判断它是否回文链表。

<!-- more -->

**后续**：

你的算法可以达到 O(n) 时间复杂度和 O(1) 空间复杂度吗？

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
    bool isPalindrome(ListNode *head) {
        
    }
};
```

思路：遍历链表元素，添加到 `vector<int> v` 里，再对 v 计算是否回文，这样的复杂度是 O(n)-time 和 O(n)-space。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    bool isPalindrome(ListNode *head) {
        // 保存链表元素值到 vector
        vector<int> v;
        while (head != NULL) {
            v.push_back(head->val);
            head = head->next;
        }

        // 判断 vector 是否回文
        for (int start = 0, end = v.size() - 1; start < end; start++, end--) {
            if (v[start] != v[end]) {
                return false;
            }
        }
        return true;
    }
};

int main() {
    ListNode A(1);
    ListNode B(2);
    ListNode C(3);
    ListNode D(2);
    ListNode E(1);

    A.next = &B;
    B.next = &C;
    C.next = &D;
    D.next = &E;

    Solution sol;
    cout << sol.isPalindrome(&A) << endl;
}

// 输出结果：
// 1
```

提交到 Leetcode，Accepted! :) 运行时间为 29ms。

---

#后续

如果要达到 O(1)-space，那么就不能额外保存 nums 元素。如果 nums 首尾元素两两比较，是 O(n^2)-time，因为移动到链表尾需要 O(n)-time。

如何达到 O(n)-time 和 O(1)-space？回文的数列有没有什么特性可以利用？有没有办法以 O(1)-time 来访问链表元素？

实在想不到什么办法，在参考了[这篇文章](https://skyyen999.gitbooks.io/-leetcode-with-javascript/content/questions/19md.html)后，借鉴作者的解法。具体思路是：

1. 求出链表的中间节点。
2. 把中间节点到末尾节点的后半段链表反转，把反转链表的首节点保存到一个额外的 ListNode* 里，以此达到 O(1)-space。
3. 同时遍历前后两半段链表，返回是否相同。

需要注意的是，如果原链表的节点数量为偶数，那么前后两半段的子链表节点数量相同。如果原链表节点数量为奇数，那么我把中间节点放到前半段子链表中。伪代码如下：

```
bool isPalindrome2(ListNode *head) {
    // 计算中间节点
    ListNode *mid = findMiddleNode(head);

    // 反转后半段链表
    ListNode *reverseHead = reverseList(mid->next);
    
    // 比较前后两个半段链表
    // 注意：后半段链表节点个数 <= 前半段链表节点个数
    while (reverseHead != NULL) {
        if (head->val != reverseHead->val) {
            return false;
        }
        head = head->next;
        reverseHead = reverseHead->next;
    }
    return true;
}

ListNode* findMiddleNode(ListNode *head) {
}

ListNode* reverseList(ListNode *head) {
}
```

findMiddleNode 的思路是使用快慢指针 `ListNode *fast` 和 `ListNode *slow`，slow 每次走一步，fast 每次走两步，当 fast 到达链表末尾时，slow 刚好就在中间了。伪代码如下：

```
// 当链表节点数量为偶数 n 时，返回 mid 为 n/2，而不是 n/2 + 1
ListNode* findMiddleNode(ListNode *head) {
    ListNode *fast = head;
    ListNode *slow = head;
    while (fast != NULL &&
           fast->next != NULL &&
           fast->next->next != NULL) {
        slow = slow->next;
        fast = fast->next->next;
    }
    return slow;
}
```

reverseList 的思路是，reverseHead = null，每遍历一个 node，设置 node->next = reverseHead，reverseHead = node，直到遍历完所有 node，返回 reverseHead。伪代码如下：

```
ListNode* reverseList(ListNode *head) {
    ListNode *reverseHead = NULL;
    ListNode *node = NULL;
    while (head != NULL) {
        if (head->next == NULL) {   // 末尾节点 = 反转链表的首节点
            reverseHead = head;
        }
        head->next = node;
        node = head;
        head = head->next;
    }
    return reverseHead;
}
```

统一所有代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    bool isPalindrome(ListNode *head) {
        // 保存链表元素值到 vector
        vector<int> v;
        while (head != NULL) {
            v.push_back(head->val);
            head = head->next;
        }

        // 判断 vector 是否回文
        for (int start = 0, end = v.size() - 1; start < end; start++, end--) {
            if (v[start] != v[end]) {
                return false;
            }
        }
        return true;
    }

    bool isPalindrome2(ListNode *head) {
        if (head == NULL) {
            return true;
        }

        // 计算中间节点
        ListNode *mid = findMiddleNode(head);

        // 反转后半段链表
        ListNode *reverseHead = reverseList(mid->next);

        // 连接前后两半段链表
        mid->next = reverseHead;

        // 比较前后两个半段链表
        // 注意：后半段链表节点个数 <= 前半段链表节点个数
        bool isPal = true;
        while (reverseHead != NULL) {
            if (head->val != reverseHead->val) {
                isPal = false;
            }
            head = head->next;
            reverseHead = reverseHead->next;
        }

        // 恢复原链表
        reverseHead = reverseList(mid->next);
        mid->next = reverseHead;

        return isPal;
    }

private:
    // 当链表节点数量为偶数 n 时，返回 mid 为 n/2，而不是 n/2 + 1
    ListNode* findMiddleNode(ListNode *head) {
        ListNode *fast = head;
        ListNode *slow = head;
        while (fast != NULL &&
            fast->next != NULL &&
            fast->next->next != NULL) {
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;
    }

    ListNode* reverseList(ListNode *head) {
        ListNode *reverseHead = NULL;
        ListNode *node = NULL;
        while (head != NULL) {
            ListNode *next = head->next;
            if (next == NULL) {   // 末尾节点 = 反转链表的首节点
                reverseHead = head;
            }
            head->next = node;
            node = head;
            head = next;
        }
        return reverseHead;
    }
};

int main() {
    ListNode A(1);
    ListNode B(2);
    ListNode C(3);
    ListNode D(4);
    ListNode E(4);
    ListNode F(3);
    ListNode G(2);
    ListNode H(1);

    A.next = &B;
    B.next = &C;
    C.next = &D;
    D.next = &E;
    E.next = &F;
    F.next = &G;
    G.next = &H;

    Solution sol;
    cout << sol.isPalindrome2(&A) << endl;
}

// 输出结果：
// 1
```

提交到 Leetcode，Accepted! 运行时间为 23ms。

我们再重新检查一下 isPalindrome2 是否满足 O(n)-time 和 O(1)-space：

1. 代码第 28 行 findMiddleNode 用了两个指针 `ListNode *fast` 和 `ListNode* slow`，遍历半个链表，满足 O(n)-time 和 O(1)-space。
2. 代码第 31 行 reverseList 用了两个指针 `ListNode *reverseHead` 和 `ListNode *node`，遍历半个链表，满足 O(n)-time 和 O(1)-space。
3. 代码第 39～45 行 while 循环，用了一个 bool 变量 isPal，遍历半个链表，满足 O(n)-time 和 O(1)-space。
4. 代码第 48 行，与第 2 步相同，满足 O(n)-time 和 O(1)-space。

综上所述，改进后的 isPalindrome2 满足 O(n)-time 和 O(1)-space。
