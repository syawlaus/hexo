title: Leetcode 题解 - 219. Contains Duplicate II
date: 2016-11-07 16:30
categories: Leetcode
---

# 题目

给定一个整数数组 nums 和一个整数 k，判断是否存在两个不同的索引 i, j，使得 nums[i] = nums[j]，且 i, j 之差最多为 k。

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {
        
    }
};
```

我的思路是：

1. 取 i = 0，j = k，判断是否 nums[i] = nums[j]，是则 return true，否则 i, j 分别 +1，重复本步骤直到 j = nums.size()-1。
2. 来到第 2 步，说明没有找到两个不同的索引 i, j，使得 nums[i] = nums[j]，且 i, j 之差为 k，所以 k -= 1，继续第 1 步，直到 k = 1。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {
        for (; k > 0; k--) {
            for (int i = 0, j = k; j < nums.size(); i++, j++) {
                if (nums[i] == nums[j]) {
                    return true;
                }
            }
        }
        return false;
    }
};

int main() {
    int array[] = { 1, 2, 3, 4, 2, 1 };
    vector<int> nums = convertArrayToVector(array, ARRAY_LENGTH(array));

    Solution sol;
    cout << sol.containsNearbyDuplicate(nums, 2);
}

// 输出结果：
// 1
```

提交到 Leetcode，Time Limit Exceeded! 导致超时的输入为 nums = { 0, 1, 2, 3, ..., 29999 }，k = 15000。上面代码的时间复杂度为 O(n^2)。

那就换个思路，每遍历 nums 一个元素，放到 `map<int, int>` 里，key 为元素值，value 为元素索引。如果当前元素不存在于 map，那么 添加到 map。如果已存在于 map，判断两个索引之差是否 <= k，如果是 retur true，否则更新 value 值。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {
        for (; k > 0; k--) {
            for (int i = 0, j = k; j < nums.size(); i++, j++) {
                if (nums[i] == nums[j]) {
                    return true;
                }
            }
        }
        return false;
    }

    bool containsNearbyDuplicate2(vector<int>& nums, int k) {
        map<int, int> m;
        for (int i = 0; i < nums.size(); i++) {
            int key = nums[i];
            int val = i;
            map<int, int>::iterator iter = m.find(key);
            if (iter != m.end()) {   // 找到重复元素
                if (val - iter->second <= k) {
                    return true;
                }
                else {
                    iter->second = val;
                }
            }
            else {      // 没找到重复元素
                m[key] = val;
            }
        }
        return false;
    }
};

int main() {
    int array[] = { 1, 2, 1 };
    vector<int> nums = convertArrayToVector(array, ARRAY_LENGTH(array));

    Solution sol;
    cout << sol.containsNearbyDuplicate2(nums, 0);
}

// 输出结果：
// 0
```

提交到 Leetcode，Accepted! :) 运行时间为 79ms。

注意上面程序的第 21 行，map.find 的时间复杂度为 O(logN)，所以整个程序的时间复杂度为 O(NlogN)。

---

# 参考资料

* [C++ STL map: is access time O(1)?](http://stackoverflow.com/questions/16068151/c-stl-map-is-access-time-o1)
* [Time complexity of find() in std::map?](http://stackoverflow.com/questions/9961742/time-complexity-of-find-in-stdmap)
