title: Leetcode 题解 - 232. Implement Queue using Stacks
date: 2016-10-23 18:10
categories: Leetcode
---

# 题目

使用栈（Stack）实现下面的队列（Queue）操作：

* push(x) -- 把元素 x 尾插入到队列
* pop() -- 从队列首删除元素
* peek() -- 获取首元素
* empty() -- 返回队列是否为空

<!-- more -->

**注意**：

* 你只能使用栈的标准操作，即：从栈顶 push 元素入栈，从栈顶 pop 元素出栈，栈大小 size，栈是否为空 isEmpty。
* 根据你使用的编程语言，栈可能没有被原生支持。你可以用 deque（双端队列 double-ended queue）来模拟栈，只要你只使用栈的标准操作即可。
* 你可以假设所有操作都合法（例如，在空队列不能调用 pop / peek 操作）。

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Queue {
public:
    // Push element x to the back of queue.
    void push(int x) {
        
    }

    // Removes the element from in front of queue.
    void pop(void) {
        
    }

    // Get the front element.
    int peek(void) {
        
    }

    // Return whether the queue is empty.
    bool empty(void) {
        
    }
};
```

题目的意思是使用“先入后出”的栈来实现“先入先出”的队列。我们用假设 `stack<int> s = { 1, 2, 3, 4, 5 }`，1 为栈底，5 为栈顶，见下图：

    stack               stack2
    栈顶 / 队列首         栈顶
    +---+               +---+
    | 5 |               | 2 |
    |   |               |   |
    | 4 |               | 3 |
    |   |               |   |
    | 3 |               | 4 |
    |   |               |   |
    | 2 |               | 5 |
    |   |               +---+
    | 1 |               栈底
    +---+
    栈底

下面是各队列操作的实现思路：

* push(x)：跟 stack.push(x) 操作一样。
* pop()：对上图左进行 queue.pop()，应该删除 1，所以需要调用 4 次 stack.pop()，依次把 5,4,3,2 取出来，保存到另一个 stack2（见上图右），然后再调用 stack.pop() 取出 1，再把 stack2 的 2,3,4,5 push 回到 stack 中（注意：每次调用 queue.pop() 时，要把 stack2 清空）。
* peek()：跟 queue.pop() 一样，返回栈底元素 1 而不删除。
* empty()：跟 stack.empty() 一样。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Queue {
public:
    // Push element x to the back of queue.
    void push(int x) {
        s.push(x);
    }

    // Removes the element from in front of queue.
    void pop(void) {
        // 清空 s2
        clearStack(s2);

        // 把非栈底元素 pop 出来，push 到 s2
        int size = s.size();
        for (int i = 0; i < size - 1; i++) {
            int element = s.top();
            s2.push(element);
            s.pop();
        }

        // 删除栈底元素
        s.pop();

        // 把 s2 元素 push 回 s
        int size2 = s2.size();
        for (int i = 0; i < size2; i++) {
            int element = s2.top();
            s.push(element);
            s2.pop();
        }
    }

    // Get the front element.
    int peek(void) {
        // 清空 s2
        clearStack(s2);

        // 把非栈底元素 pop 出来，push 到 s2
        int size = s.size();
        for (int i = 0; i < size - 1; i++) {
            int element = s.top();
            s2.push(element);
            s.pop();
        }

        // 返回栈底元素
        int frontElement = s.top();

        // 把 s2 元素 push 回 s
        int size2 = s2.size();
        for (int i = 0; i < size2; i++) {
            int element = s2.top();
            s.push(element);
            s2.pop();
        }

        return frontElement;
    }

    // Return whether the queue is empty.
    bool empty(void) {
        return s.empty();
    }

private:
    stack<int> s;
    stack<int> s2;

    void clearStack(stack<int>& s) {
        for (int i = 0; i < s.size(); i++) {
            s.pop();
        }
    }
};

int main() {
    Queue q;
    q.push(1);

    int i = q.peek();

    bool isEmpty = q.empty();
}
```

把 Solution 提交到 Leetcode，Accepted! :) 运行时间为 0ms。
