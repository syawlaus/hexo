title: Leetcode 题解 - 283. Move Zeroes
date: 2016-06-28 18:00
categories: Leetcode
---

#题目

给定一个数组 nums，编写一个函数，移动所有 0 到数组末尾，同时保持其它非 0 元素的相对顺序。

<!-- more -->

**示例**：

给定 nums = [0, 1, 0, 3, 12]，在调用你的函数后，nums 应该为 [1, 3, 12, 0, 0]。

**注意**：

1. 你的操作必须是原地的（in-place），不允许复制数组。
2. 最小化移动操作的数量。

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        
    }
};
```

我的思路是执行过程是：

* index = 0，index 用于在遍历 nums 时记录当前非 0 元素需要往前覆盖的位置
* nums[0] = 0，下一个非 0 元素将覆盖 nums[index]
* nums[1] = 1，把 1 覆盖 nums[index]，即 nums[0] = 1，index + 1 = 1
* nums[2] = 0，下一个非 0 元素将覆盖 nums[index]
* nums[3] = 3，把 3 覆盖 nums[index]，即 nums[1] = 3，index + 1 = 2
* nums[4] = 12，把 12 覆盖 nums[index]，即 nums[2] = 12，index + 1 = 3
* 从 index = 3 开始到数据末尾，全部用 0 覆盖

伪代码如下：

```
void moveZeroes(vector<int>& nums) {
    int index = 0;
    int size = nums.size();
    for (int i = 0; i < size; i++) {
        // 记录当前非 0 元素需要往前覆盖的位置
        if (nums[i] != 0) {
            nums[index] = nums[i];
            index++;
        }
    }

    // 从 index 开始到数据末尾，全部用 0 覆盖
    for (int j = index; j < size; j++) {
        nums[j] = 0;
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
    void moveZeroes(vector<int>& nums) {
        int index = 0;
        int size = nums.size();
        for (int i = 0; i < size; i++) {
            // 记录当前非 0 元素需要往前覆盖的位置
            if (nums[i] != 0) {
                nums[index] = nums[i];
                index++;
            }
        }

        // 从 index 开始到数据末尾，全部用 0 覆盖
        for (int j = index; j < size; j++) {
            nums[j] = 0;
        }
    }
};

int main() {
    vector<int> nums;
    nums.push_back(0);
    nums.push_back(1);
    nums.push_back(0);
    nums.push_back(3);
    nums.push_back(12);

    Solution sol;
    sol.moveZeroes(nums);
    
    for (int ele : nums) {
        cout << ele << ' ';
    }
}

// 输出结果：
// 1 3 12 0 0 
```

提交到 Leetcode，Accepted! :) 运行时间为 20ms。
