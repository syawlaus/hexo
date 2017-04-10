title: Leetcode 题解 - 198. House Robber
date: 2016-07-31 23:00
categories: Leetcode
---

# 题目

你是一个专业的强盗，计划抢劫街上的房子。每个房子里都藏有一些钱，阻止你抢劫的唯一因素是，相邻的房子连接了安全系统，如果相邻的两个房子在同一晚被抢劫，系统会自动报警。

给定一个非负整数列表，每个非负整数代表每个房子藏有的钱。计算出你在不惊动警方的情况下，能抢劫的金额最大值。

<!-- more -->

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```
class Solution {
public:
    int rob(vector<int>& nums) {

    }
};
```

我们假设 nums = { 1, 2, 3, 4, 5 }：

* 如果先抢 1，那么不能抢 2，抢 3，不能抢 4，抢 5。总抢劫金额 sum = 1 + 3 + 5 = 9。
* 如果先抢 2，那么不能抢 1、3，抢 4，不能抢 5。总抢劫金额 sum = 2 + 4 = 6。

当然不止上述两个抢劫方案。我对整个题目的理解是：**对于一个整数列表，找出不相邻的数字之和的最大值**。

我想到的一个方案是递归，假设现在要求 rob(1, 2, 3, 4, 5)，那么：

    对于 rob(1, 2, 3, 4, 5)
	    抢 1，因为 2 是相邻所以不能抢，那么就剩下 rob(3, 4, 5)
            对于 rob(3, 4, 5)
                抢 3，因为 4 是相邻所以不能抢，那么就剩下 rob(5)
                    对于 rob(5)
                        抢 5
                抢 4，因为 5 是相邻所以不能抢
                所以 rob(3, 4, 5) = 3 + 5 = 8
                或者 rob(3, 4, 5) = 4
                取 8
            所以 rob(1, 2, 3, 4, 5) = 1 + rob(3, 4, 5) = 1 + 8 = 9
        抢 2，因为 3 是相邻所以不能抢，那么就剩下 rob(4, 5)
            对于 rob(4, 5)
                抢 4
                抢 5
                所以 rob(4, 5) = 4
                或者 rob(4, 5) = 5
                取 5
            所以 rob(1, 2, 3, 4, 5) = 2 + 5 = 7
        取 9

代码的思路是：

* rob(a)，取 a
* rob(a, b)，取 max(a, b)
* rob(a, b, c, d, ...)
    * sum1 = a + rob(c, d, ...)
    * sum2 = b + rob(d, e, ...)
    * 取 max(sum1, sum2)

代码如下：

```cpp
#include <vector>
#include <frequently-used-code-snippets.h>
using namespace std;

class Solution {
public:
    int rob(vector<int>& nums) {
        return robRecur(nums, nums.size(), 0);
    }

private:
    int robRecur(vector<int>& nums,
                 int numsSize,
                 int index) {
        int size = numsSize - index;

        // 没有元素
        if (size <= 0) {
            return 0;
        }

        // 一个元素
        if (size == 1) {
            return nums[index];
        }

        // 两个元素
        int first = nums[index];
        int second = nums[index + 1];
        if (size == 2) {
            return MAX_TWO(first, second);
        }

        // 多个元素
        int sum1 = first + robRecur(nums, numsSize, index + 2);
        int sum2 = second + robRecur(nums, numsSize, index + 3);
        return MAX_TWO(sum1, sum2);
    }
};

int main() {
    vector<int> v;
    v.push_back(1);
    v.push_back(2);
    v.push_back(3);
    v.push_back(4);
    v.push_back(5);

    Solution sol;
    cout << sol.rob(v) << endl;
}

// 输出结果：
// 9
```

提交到 Leetcode，Time Limit Exceeded! 测试用例为：

    [226, 174, 214, 16, 218, 48, 153, 131, 128, 17, 157, 142, 
     88, 43, 37, 157, 43, 221, 191, 68, 206, 23, 225, 82, 54, 
     118, 111, 46, 80, 49, 245, 63, 25, 194, 72, 80, 143, 55, 
     209, 18, 55, 122, 65, 66, 177, 101, 63, 201, 172, 130, 
     103, 225, 142, 46, 86, 185, 62, 138, 212, 192, 125, 77, 
     223, 188, 99, 228, 90, 25, 193, 211, 84, 239, 119, 234, 
     85, 83, 123, 120, 131, 203, 219, 10, 82, 35, 120, 180, 
     249, 106, 37, 169, 225, 54, 103, 55, 166, 124]

---

# 时间复杂度

我们分析一下上面代码的时间复杂度，rob(a, b, c, d, …) = max(a + rob(c, d, …), b + rob(d, e, …))。我们把上面算法对于有 n 个数据时的运行时间设为 T(n)，那么可得出计算时间复杂度的递推方程 T(n) = max(T(n-2), T(n-3))，即 T(n) = T(n-2) + T(n-3) + O(1)。

    T(n) = T(n-2) + T(n-3) + O(1)
         = T(n-4) + T(n-5) + O(1) +     // T(n-2)
           T(n-5) + T(n-6) + O(1) +     // T(n-3)
           O(1)
         = T(n-6) + T(n-7) + O(1) +     // T(n-4)
           T(n-7) + T(n-8) + O(1) +     // T(n-5)
           T(n-7) + T(n-8) + O(1) +     // T(n-5)
           T(n-8) + T(n-9) + O(1) +     // T(n-6)
           O(1)
         = ....

可以看到，随着 T(n) 的展开，产生大量的重复计算。为了方便计算时间复杂度，我把递推方程简化为：

* T(n) = 2*T(n-2) + O(1)   // 复杂度上限
* T(n) = 2*T(n-3) + O(1)   // 复杂度下限

对于上限方程：

    T(n) = 2*T(n-2) + O(1)
         = 2*(2*T(n-4) + O(1)) + O(1)   = 4*T(n-4) + 3*O(1)
         = 4*(2*T(n-6) + O(1)) + 3*O(1) = 8*T(n-6) + 7*O(1)
         = 8*(2*T(n-8) + O(1)) + 7*O(1) = 16*T(n-8) + 15*O(1)
         = ...
         = 2^(n/2)*T(1) + (2^(n/2)-1)*O(1)

所以 T(n) = 2*T(n-2) + O(1) 的时间复杂度为 O(2^(n/2))。

对于下限方程：

    T(n) = 2*T(n-3) + O(1)
         = 2*(2*T(n-6) + O(1)) + O(1) = 4*T(n-6) + 3*O(1)
         = 4*(2*T(n-9) + O(1)) + 3*O(1) = 8*T(n-9) + 7*O(1)
         = 8*(2*T(n-12) + O(1)) + 7*O(1) = 16*T(n-12) + 15*O(1)
         = ...
         = 2^(n/3)*T(1) + (2^(n/3)-1)*O(1)

所以 T(n) = 2*T(n-3) + O(1) 的时间复杂度为 O(2^(n/3))。

所以 T(n) = T(n-2) + T(n-3) + O(1) 的时间复杂度可简化为 O(2^n)。

---

# 改进

改进的思路很简单，就是把各个计算结果记录下来，那么下次就能直接使用无需重复计算。以 rob(1, 2, 3, 4, 5) 为例。

以 rob(1, 2, 3, 4, 5) 为例，用 R(n) 表示 rob(1, 2, ..., n)，那么：

* R(1) = 1
* R(2) = max(1, 2) = 2
* R(3) = max(3 + R(1), R(2)) = max(4, 2) = 4
* R(4) = max(4 + R(2), R(3)) = max(6, 4) = 6
* R(5) = max(5 + R(3), R(4)) = max(9, 6) = 9

每记算出一个 R(n)，保存下来，下次直接使用，减少重复计算。

伪代码如下：

```
int rob(vector<int>& nums) {
    int len = nums.length;
    if (len == 0) {
        return 0;
    }
    if (len == 1) {
        return nums[0];
    }
    if (len == 2) {
        return MAX_TWO(nums[0], nums[1]);
    }

    // 新建数组保存 R(n)
    int R[] = new int[len];
    R[0] = nums[0];
    R[1] = MAX_TWO(nums[0], nums[1]);
    for (int i = 2; i < len; i++) {
        R[i] = MAX_TWO(nums[i] + R[i - 2], R[i - 1]);
    }

    return R[len - 1];
}
```

代码如下：

```cpp
#include <vector>
#include <frequently-used-code-snippets.h>
using namespace std;

class Solution {
public:
    int rob2(vector<int>& nums) {
        int len = nums.size();
        if (len == 0) {
            return 0;
        }
        if (len == 1) {
            return nums[0];
        }
        if (len == 2) {
            return MAX_TWO(nums[0], nums[1]);
        }

        // 新建数组保存 R(n)
        int *R = new int[len];
        R[0] = nums[0];
        R[1] = MAX_TWO(nums[0], nums[1]);
        for (int i = 2; i < len; i++) {
            R[i] = MAX_TWO(nums[i] + R[i - 2], R[i - 1]);
        }

        return R[len - 1];
    }
};

int main() {
    int array[] = {
        226, 174, 214, 16, 218, 48, 153, 131, 128, 17, 157, 142,
        88, 43, 37, 157, 43, 221, 191, 68, 206, 23, 225, 82, 54,
        118, 111, 46, 80, 49, 245, 63, 25, 194, 72, 80, 143, 55,
        209, 18, 55, 122, 65, 66, 177, 101, 63, 201, 172, 130,
        103, 225, 142, 46, 86, 185, 62, 138, 212, 192, 125, 77,
        223, 188, 99, 228, 90, 25, 193, 211, 84, 239, 119, 234,
        85, 83, 123, 120, 131, 203, 219, 10, 82, 35, 120, 180,
        249, 106, 37, 169, 225, 54, 103, 55, 166, 124
    };

    vector<int> v = convertArrayToVector(array, ARRAY_LENGTH(array));

    Solution sol;
    cout << sol.rob2(v) << endl;
}

// 输出结果：
// 7102
```

提交到 Leetcode，Accepted! :)
