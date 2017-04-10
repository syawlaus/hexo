title: Leetcode 题解 - 414. Third Maximum Number
date: 2016-11-12 16:45
categories: Leetcode
---

#题目

给定一个**非空**整数数组，返回数组里的**第三大**值。如果不存在，返回最大值。时间复杂度必须为 O(n)。

<!-- more -->

**示例**：

示例一：

    输入：[3, 2, 1]
    输出：1
    解释：第三大值为 1。

示例二：

    输入：[1, 2]
    输出：2
    解释：第三大值不存在，所以返回最大值 2。

示例三：

    输入：[2, 2, 3, 1]
    输出：1
    解释：注意第三大值表示第三大的独立的值，两个 2 都认为是第二大值。

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
class Solution {
public:
    int thirdMax(vector<int>& nums) {
        
    }
};
```

思路是：

1. 使用三个 int，分别是 max, max2, max3 表示最大值、第二大值、第三大值，初始值是 int 型数据的最小值。
2. 每当遍历到一个数据 curr 时：
    2.1. 如果 max < curr，那么 max3 = max2，max2 = max，max = curr。
    2.2. 如果 max2 < curr < max，那么 max3 = max2，max2 = curr。
    2.3. 如果 max3 < curr < max2，那么 max3 = curr。
    2.4. 否则（即 curr == max 或 curr == max2 或 curr == max3 或 curr < max3），无需处理。
3. 遍历数组完成后，如果 max3 仍是初始值，那么返回 max，否则返回 max3。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    int thirdMax(vector<int>& nums) {
        int max = INT_MIN;
        int max2 = INT_MIN;
        int max3 = INT_MIN;

        for (int curr : nums) {
            if (max < curr) {
                max3 = max2;
                max2 = max;
                max = curr;
            }
            else if (max2 < curr && curr < max) {
                max3 = max2;
                max2 = curr;
            }
            else if (max3 < curr && curr < max2) {
                max3 = curr;
            }
            else {  // curr == max 或 curr == max2 或 curr == max3 或 curr < max3
                // 无需处理
            }
        }

        return (max3 == INT_MIN) ? max : max3;
    }
};

int main() {
    int arr[] = { 2, 2, 3, 1 };
    vector<int> nums = convertArrayToVector(arr, ARRAY_LENGTH(arr));

    Solution sol;
    cout << sol.thirdMax(nums) << endl;
}

// 输出结果：
// 1
```

提交到 Leetcode，Wrong Answer! 导致错误的输入为 [1, 2, -2147483648]，我的程序输出 2，正确应输出 -2147483648。-2147483648 刚好是 `INT_MIN` 的值。这是因为 `return (max3 == INT_MIN) ? max : max3;`，为了解决这个问题，需要区分 max3 的值为 INT_MIN 时，是赋值而来，还是仍然是初始化的值，所以再加一个 bool 区分即可。

