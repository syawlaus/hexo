title: Leetcode 题解 - 88. Merge Sorted Array
date: 2016-11-07 23:30
categories: Leetcode
---

#题目

给定两个有序的整数数组 nums1 和 nums2，把 num2 合并到 nums1 使之成为一个有序数组。

<!-- more -->

**注意**：

你可以假设 nums1 有足够的空间（数组大小 >= m + n）保存 nums2 的元素，nums1 和 nums2 里初始化的元素个数分别为 m 和 n。

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        
    }
};
```

思路：

1. 把 nums1 复制到 backup1
2. 清空 nums1
3. 把 backup1 和 nums2 合并到 nums1
    3.1. 使用 i1 和 i2 表示遍历 backup1 和 nums2 元素的索引
    3.2. 如果 backup1[i1] < nums2[i2]，nums1.push_back(backup1[i1++])，否则 nums1.push_back(nums2[i2++])
    3.3. 重复第 3.2 步，直到 i1 到达 backup1 末尾，或 i2 到达 nums2 末尾
    3.4. 把第 3.3 步中剩下的其中一个数组，按顺序 push_back 到 nums1

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

// 功能：复制 vector
// 参数：vector
// 返回：vector
template <class T> vector<T> copyVector(vector<T>& v) {
    vector<T> copyV;
    for (T e : v) {
        copyV.push_back(e);
    }
    return copyV;
}

class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        vector<int> backup1 = copyVector(nums1);
        nums1.clear();

        int i1 = 0;
        int i2 = 0;
        while (i1 < backup1.size() && i2 < nums2.size()) {
            if (backup1[i1] < nums2[i2]) {
                nums1.push_back(backup1[i1]);
                i1++;
            }
            else {
                nums1.push_back(nums2[i2]);
                i2++;
            }
        }
        if (i1 >= backup1.size()) {     // backup1 先遍历完，把 nums2 剩下的元素添加到 nums1
            for (; i2 < nums2.size(); i2++) {
                nums1.push_back(nums2[i2]);
            }
        }
        if (i2 >= nums2.size()) {       // nums2 先遍历完，把 backup1 剩下的元素添加到 nums1
            for (; i1 < backup1.size(); i1++) {
                nums1.push_back(backup1[i1]);
            }
        }
    }
};

int main() {
    int arr1[] = { 1, 3, 5, 7, 9 };
    vector<int> nums1 = convertArrayToVector(arr1, ARRAY_LENGTH(arr1));

    int arr2[] = { 0, 2, 4, 6, 8 };
    vector<int> nums2 = convertArrayToVector(arr2, ARRAY_LENGTH(arr2));

    Solution sol;
    sol.merge(nums1, nums1.size(), nums2, nums2.size());
    printVector(nums1);
}

// 输出结果：
// 0 1 2 3 4 5 6 7 8 9 
```

提交到 Leetcode，Wrong Answer! 导致错误的输入是：[0], 0, [1], 1，我的输出是 [0,1]，正确答案应输出 [1]。为什么 [0] 没有 merge 到 nums1？按照题意，应该是只 merge nums1 前 m 个已初始化的元素，和 nums2 前 n 个已初始化的元素。修改后的代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        vector<int> backup1 = copyInitVector(nums1, m);
        nums1.clear();

        int i1 = 0;
        int i2 = 0;
        while (i1 < backup1.size() && i2 < n) {
            if (backup1[i1] < nums2[i2]) {
                nums1.push_back(backup1[i1]);
                i1++;
            }
            else {
                nums1.push_back(nums2[i2]);
                i2++;
            }
        }
        if (i1 >= backup1.size()) {     // backup1 先遍历完，把 nums2 剩下的元素添加到 nums1
            for (; i2 < n; i2++) {
                nums1.push_back(nums2[i2]);
            }
        }
        if (i2 >= n) {       // nums2 先遍历完，把 backup1 剩下的元素添加到 nums1
            for (; i1 < backup1.size(); i1++) {
                nums1.push_back(backup1[i1]);
            }
        }
    }

private:
    vector<int> copyInitVector(vector<int>& v, int initNum) {
        vector<int> copyV;
        for (int i = 0; i < initNum; i++) {
            copyV.push_back(v[i]);
        }
        return copyV;
    }
};

int main() {
    int arr1[] = { 0 };
    vector<int> nums1 = convertArrayToVector(arr1, ARRAY_LENGTH(arr1));

    int arr2[] = { 1 };
    vector<int> nums2 = convertArrayToVector(arr2, ARRAY_LENGTH(arr2));

    Solution sol;
    sol.merge(nums1, 0, nums2, 1);
    printVector(nums1);
}

// 输出结果：
// 1
```

提交到 Leetcode，Accepted! :) 运行时间为 6ms。
