title: Leetcode 题解 - 217. Contains Duplicate
date: 2016-07-05 18:30
categories: Leetcode
---

# 题目

给定一个整数数组，判断是否有重复的整数。如果任何一个整数出现至少两次，你的函数返回 true。如果每个整数都唯一，则返回 false。

<!-- more -->

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {

    }
};
```

最简单的思路，使用 map<int, int>。遍历每个元素，放到 map 中，如果发现 map 已存在这个 key，说明元素重复，直接返回 true。在遍历完成后返回 false。

伪代码如下：

```
bool containsDuplicate(vector<int>& nums) {
    map<int, int> mapp;
    for (each i in nums) {
        if (containsKey(mapp, i)) {
            return true;
        }
        else {
            mapp[i] = 1;
        }
    }
    return false;
}

bool containsKey(map<int, int>& mapp, int key) {
    map<int, int>::iterator iter = mapp.find(key);
    return iter != mapp.end();
}
```

代码如下：

```cpp
#include <iostream>
#include <vector>
#include <map>
using namespace std;

class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        map<int, int> mapp;
        for (int i : nums) {
            if (containsKey(mapp, i)) {
                return true;
            }
            else {
                mapp[i] = 1;
            }
        }
        return false;
    }

    bool containsKey(map<int, int>& mapp, int key) {
        map<int, int>::iterator iter = mapp.find(key);
        return iter != mapp.end();
    }
};

int main() {
    int a[] = { 1, 2, 3, 4, 5, 6, 7, 7};
    int len = sizeof(a) / sizeof(a[0]);
    vector<int> v(a, a + len);

    Solution sol;
    bool b = sol.containsDuplicate(v);
    cout << ((b == true) ? "true" : "false");
}

// 输出结果：
// true
```

提交到 Leetcode，Accepted! :) 运行时间为 100ms。
