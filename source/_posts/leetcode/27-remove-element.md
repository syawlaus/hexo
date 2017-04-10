title: Leetcode 题解 - 27. Remove Element
date: 2016-10-19 17:10
categories: Leetcode
---

#题目

给定一个数组和一个值，原地（in place）删除该值在数组中的所有实例，并返回新的长度。

不要分配额外的内存空间给其它数组，必须使用常数内存进行原地操作。

元素的顺序可以改变，只要返回的新长度正确即可。

<!-- more -->

**示例**：

给定输入数组 nums = [3, 2, 2, 3]，val = 3，你的函数应该返回 length = 2，nums 的前两个元素为 2。

**难度**：容易

**编程语言**：C++

---

#分析

这道题目很简单，直接调用 vector.erase 即可，vector.erase 返回被删除元素的下一个元素的迭代器：

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        for (vector<int>::iterator iter = nums.begin(); iter != nums.end(); ) {
            if (*iter == val) {
                iter = nums.erase(iter);
            }
            else {
                iter++;
            }
        }
        return nums.size();
    }
};

int main() {
    vector<int> nums = { 3, 2, 2, 3 };
    int val = 3;

    Solution sol;
    cout << sol.removeElement(nums, val) << endl;
}
```

提交到 Leetcode，Accepted! :) 运行时间为 3ms。
