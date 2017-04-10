title: Leetcode 题解 - 169. Majority Element
date: 2016-07-05 14:15
categories: Leetcode
---

# 题目

给定一个数组，大小为 n，找出“最多数”（majority element）。“最多数”即出现次数多于 ⌊n/2⌋ 次的元素。

你可以假定数组非空，而且“最多数”总是存在。

<!-- more -->

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        
    }
};
```

我们先来分析一下“最多数”：

* 如果数组有 9 个元素，那么 ⌊n/2⌋ = 4，即“最多数”要至少出现 5 次
* 如果数组有 10 个元素，那么 ⌊n/2⌋ = 5，即“最多数”要至少出现 6 次

即“最多数”至少出现 (n/2 + 1) 次，其中 n/2 为 int 除法。

最简单的当然是使用 map<int, int> 了，记录每个元素出现的次数。最后遍历找出 value 最大值即可。因为“最多数”的出现次数肯定比 n/2 多，所以肯定是所有 value 的最大值。

伪代码如下：

```
int majorityElement(vector<int>& nums) {

    // 遍历数组，添加到 mapp
    map<int, int> mapp;
    for (each i in nums) {
        addToMap(i, mapp);
    }

    // 遍历 mapp，找出 majority element
    int majorityEle;
    int max = 0;
    for (each key in mapp) {
        if (value > max) {
            max = value;
            majorityEle = key;
        }
    }

    return majorityEle;
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
    int majorityElement(vector<int>& nums) {
        map<int, int> mapp;

        // 遍历数组，添加到 mapp
        for (int i : nums) {
            addToMap(i, mapp);
        }

        // 遍历 mapp，找出 majority element
        int majorityEle;
        int max = 0;
        for (map<int, int>::iterator iter = mapp.begin(); iter != mapp.end(); iter++) {
            int key = iter->first;
            int val = iter->second;

            if (val > max) {
                max = val;
                majorityEle = key;
            }
        }

        return majorityEle;
    }

    void addToMap(int key, map<int, int>& mapp) {
        map<int, int>::iterator iter = mapp.find(key);

        // 找到 key
        if (iter != mapp.end()) {
            (iter->second)++;
        }
        // 找不到 key
        else {
            mapp[key] = 1;
        }
    }
};

int main() {
    vector<int> nums;
    nums.push_back(1);
    nums.push_back(2);
    nums.push_back(4);
    nums.push_back(4);
    nums.push_back(4);

    Solution sol;
    cout << sol.majorityElement(nums) << endl;
}
```

把 Solution 提交到 Leetcode，Accepted! :) 运行时间为 32ms。
