title: Leetcode 题解 - 225. Implement Stack using Queues
date: 2016-11-10 17:00
categories: Leetcode
---

#题目

使用队列来实现下列的栈操作：

* push(x) -- 把元素 x 压栈。
* pop() -- 删除栈顶元素。
* top() -- 获取栈顶元素。
* empty() -- 返回栈是否为空。

<!-- more -->

**注意**：

* 你只能使用队列的标准操作，即只能使用「把元素压入队列尾」、「从队列头取出元素」、「求队列大小」、以及「队列是否为空」。
* 根据你使用的编程语言，队列可能没有被原生支持。你可以用 deque（双端队列 double-ended queue）来模拟队列，只要你只使用队列的标准操作即可。
* 你可以假设所有操作都合法（例如，在空栈不能调用 pop / top 操作）。

**Update (2015-06-11)**：

Java 函数的类名已从 Stack 更新为 MyStack。

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
class Stack {
public:
    // Push element x onto stack.
    void push(int x) {
        
    }

    // Removes the element on top of the stack.
    void pop() {
        
    }

    // Get the top element.
    int top() {
        
    }

    // Return whether the stack is empty.
    bool empty() {
        
    }
};
```

我们用队列头对应栈底，队列尾对应栈顶。下面是实现各个栈操作的思路：

* 栈的 push 操作，push(x) 就是把 x 添加到栈顶。所以 stack.push(x) 跟 queue.push_back(x) 一样。
* 栈的 pop 操作，需要在栈顶取出元素，即是从队列尾取出元素，但队列是先入先出，只能从队列头取出元素，所以需要先把除队列尾的所有元素从队列头按顺序取出，放到另一个队列 queue2 里，然后清空队列，再把 queue2 元素按元素放回 queue1 里。每次 pop() 前，先清空 queue2。另外，pop() 后要更新 topElement。
* 栈的 top 操作，可以使用一个 int topElement 来记录栈顶（队列尾）元素，执行 top() 时直接返回 topElement，只要每次 push(x) 和 pop() 时更新 topElement 即可。
* 栈的 empty 操作，跟 queue.empty() 一样。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Stack {
public:
    // Push element x onto stack.
    void push(int x) {
        queue.push_back(x);
        topElement = x;
    }

    // Removes the element on top of the stack.
    void pop() {
        // 记录除栈顶的其它元素
        popQueue.clear();
        for (int i = 0; i < queue.size() - 1; i++) {
            popQueue.push_back(queue[i]);
        }

        // 恢复除栈顶的其它元素
        queue.clear();
        for (int i = 0; i < popQueue.size(); i++) {
            queue.push_back(popQueue[i]);
        }
        
        // 更新 topElement
        if (queue.size() > 0) {
            topElement = queue[queue.size() - 1];
        }
        else {
            topElement = UNDEFINED;
        }
    }

    // Get the top element.
    int top() {
        return topElement;
    }

    // Return whether the stack is empty.
    bool empty() {
        return queue.empty();
    }

private:
    vector<int> queue;
    vector<int> popQueue;
    int topElement;
    const int UNDEFINED = -1;
};

int main() {
    Stack s;
    s.push(1);
    s.push(2);
    s.push(3);

    cout << s.top() << endl;
    s.pop();
    cout << s.top() << endl;
    s.pop();
    cout << s.top() << endl;
    s.pop();
    cout << s.empty() << endl;
}

// 输出结果：
// 3
// 2
// 1
// 1
```

把 Solution 提交到 Leetcode，Accepted! :) 运行时间为 0ms。
