title: Leetcode 题解 - 26. Remove Duplicates from Sorted Array
date: 2016-10-23 17:10
categories: Leetcode
---

# 题目

给定一个有序数组，原地删除重复的元素，使得数组里每个元素只出现一次，并返回新的长度。

不能分配额外的内存空间给其它数组，只能使用常数内存进行原地操作。

<!-- more -->

**示例**：

给定输入数组 nums = [1, 1, 2]，你的函数应该返回 length = 2，nums 的前两个元素分别为 [1, 2]，但 nums 即使乱序也没有关系。

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        
    }
};
```

思路是使用 vector 迭代器来遍历 vector，如果当前迭代器指向的元素跟上一个元素一样，那么删除该元素，迭代器指向被删除元素的下一个元素。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if (nums.size() == 0) {
            return 0;
        }
        for (vector<int>::iterator iter = nums.begin() + 1; iter != nums.end(); ) {
            if (*iter == *(iter - 1)) {
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
    int array[] = { 1, 1, 2, 2, 3, 3, 4, 5, 6 };
    vector<int> nums = convertArrayToVector(array, ARRAY_LENGTH(array));

    Solution sol;
    int newLength = sol.removeDuplicates(nums);
    printVector(nums);
    cout << "new length: " << newLength << endl;
}

// 输出结果：
// 1 2 3 4 5 6
// new length: 6
```

提交到 Leetcode，Accepted! :) 运行时间为 69ms。
