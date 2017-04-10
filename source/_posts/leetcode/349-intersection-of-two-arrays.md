title: Leetcode 题解 - 349. Intersection of Two Arrays
date: 2016-06-29 12:20
categories: Leetcode
---

#题目

给定两个数组，编写一个函数，计算它们的交集。

<!-- more -->

**示例**：

给定 nums1 = [1, 2, 2, 1]，nums2 = [2, 2]，返回 [2]。

**注意**：

1. 交集数组里的每个元素都唯一。
2. 交集数组里的元素允许任意顺序。

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        
    }
};
```

最简单的思路是，对于 nums1 的每个元素，遍历 nums2 的每个元素（即嵌套循环），发现相同的元素后，添加到交集数组 intersec 中（添加前要先判断没有跟已有元素重复）。

伪代码如下：

```
vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
    vector<int> intersec
    for (nums1 每个元素 ele1) {
        for (nums2 每个元素 ele2) {
            if (ele2 == ele1) {
                addToIntersec(ele2, intersec)
            }
        }
    }
    return intersec
}

void addToIntersec(int addEle, vector<int>& intersec) {
    // 添加前要先判断没有跟已有元素重复
    bool exist = false;
    for (intersec 每个元素 ele) {
        if (addEle == ele) {
            exist = true;
            break;
        }
    }

    // 添加到 intersec
    if (! exist) {
        intersec.add(addEle);
    }
}
```

代码如下：

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        vector<int> intersec;
        for (int ele1 : nums1) {
            for (int ele2 : nums2) {
                if (ele2 == ele1) {
                    addToIntersec(ele2, intersec);
                }
            }
        }

        return intersec;
    }

    void addToIntersec(int addEle, vector<int>& intersec) {
        // 添加前要先判断没有跟已有元素重复
        bool exist = false;
        for (int ele: intersec) {
            if (addEle == ele) {
                exist = true;
                break;
            }
        }

        // 添加到 intersec
        if (! exist) {
            intersec.push_back(addEle);
        }
    }
};

int main() {
    vector<int> nums1;
    nums1.push_back(1);
    nums1.push_back(2);
    nums1.push_back(2);
    nums1.push_back(1);

    vector<int> nums2;
    nums2.push_back(2);
    nums2.push_back(2);

    Solution sol;
    vector<int> intersec = sol.intersection(nums1, nums2);
    for (int ele : intersec) {
        cout << ele << ' ';
    }
}

// 输出结果：
// 2
```

提交到 Leetcode，Accepted! :) 运行时间为 60ms。
