title: Leetcode 题解 - 350. Intersection of Two Arrays II
date: 2016-07-18 20:45
categories: Leetcode
---

# 题目

给定两个数组，编写一个函数，计算它们的交集。

<!-- more -->

**示例**：

给定 nums1 = [1, 2, 2, 1]，nums2 = [2, 2]，返回 [2, 2]。

**注意**：

* 交集里每个元素的出现次数为该元素同时出现在 nums1, nums2 的次数。
* 交集里的元素可以为任意顺序。

**后续**：

* 如果 nums1, nums2 都有序，你如何优化你的算法？
* 如果 nums1 的大小比 nums2 小？哪个算法更好？
* 如果 nums2 存储在磁盘上，且内存不足，无法把 nums2 全部元素一次性加载到内存，该怎么办？

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        
    }
};
```

最直观的思路是，对于 nums2 的每个元素 ele2，遍历 nums1 的每个元素 ele1，如果 ele2 == ele1，添加到 vector，并标记该 ele1。下次遍历 nums1 时，跳过已标记的 ele1。

伪代码如下：

```
vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
    vector<int> intersec;
    for (int ele2 : nums2) {
        for (int ele1 : nums1) {
            if (ele1 is unmarked && ele1 == ele2) {
                mark ele1;
                intersec.push_back(ele1);
            }
        }    
    }
    return intersec;
}
```

那么如何标记 ele1 呢？我有两个思路：

1. 新建一个长度与 nums 相同的 bool 数组。
2. 构造一个 struct MarkedNum，有两个字段，int 表示 ele1 的值，bool 表示 ele1 是否标记。再构造一个 `vector<MarkedNum> marks`。

下面分别为两种思路构建程序。

## 思路一

> 新建一个长度与 nums 相同的 bool 数组。

代码如下：

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        bool* marks = markNums(nums1);
        vector<int> intersec;
        for (int ele2 : nums2) {
            for (int i1 = 0; i1 < nums1.size(); i1++) {
                int ele1 = nums1[i1];
                if (! marks[i1] && ele1 == ele2) {
                    marks[i1] = true;
                    intersec.push_back(ele1);
                    break;
                }
            }
        }
        return intersec;
    }

private:
    bool* markNums(vector<int>& nums) {
        // new
        int size = nums.size();
        bool* marks = new bool[size];

        // init
        for (int i = 0; i < size; i++) {
            marks[i] = false;
        }
        return marks;
    }
};

void printVector(vector<int>& v) {
    for (int ele : v) {
        cout << ele << ' ';
    }
    cout << endl;
}

int main() {
    // 初始化 nums1
    vector<int> nums1;
    nums1.push_back(1);
    nums1.push_back(2);
    nums1.push_back(1);
    nums1.push_back(3);

    // 初始化 nums2
    vector<int> nums2;
    nums2.push_back(2);
    nums2.push_back(3);

    // 计算 nums1, num2 交集
    Solution sol;
    vector<int> intersec = sol.intersect(nums1, nums2);
    printVector(intersec);
}

// 输出结果：
// 2 3
```

提交到 Leetcode，Accepted! :) 运行时间为 40ms。

## 思路二

> 构造一个 struct MarkedNum，有两个字段，int 表示 ele1 的值，bool 表示 ele1 是否标记。再构造一个 `vector<MarkedNum> marks`。

代码如下：

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        vector<MarkedNum> markedNums1 = markNums(nums1);
        vector<int> intersec;
        for (int ele2 : nums2) {
            for (MarkedNum mn1 : markedNums1) {             // 有问题
                if (! mn1.isMarked && mn1.num == ele2) {
                    mn1.isMarked = true;                    // 有问题
                    intersec.push_back(mn1.num);
                    break;
                }
            }
        }
        return intersec;
    }

private:
    struct MarkedNum {
        int num;
        bool isMarked;
    };

    vector<MarkedNum> markNums(vector<int>& nums) {
        int size = nums.size();
        vector<MarkedNum> markedNums(size);
        for (int i = 0; i < nums.size(); i++) {
            markedNums[i].num = nums[i];
            markedNums[i].isMarked = false;
        }
        return markedNums;
    }
};

void printVector(vector<int>& v) {
    for (int ele : v) {
        cout << ele << ' ';
    }
    cout << endl;
}

int main() {
    // 初始化 nums1
    int a[] = { 1, 2, 1, 3 };
    int len1 = sizeof(a) / sizeof(a[0]);
    vector<int> nums1(a, a + len1);

    // 初始化 nums2
    int b[] = { 2, 2, 3 };
    int len2 = sizeof(b) / sizeof(b[0]);
    vector<int> nums2(b, b + len2);

    // 计算 nums1, num2 交集
    Solution sol;
    vector<int> intersec = sol.intersect(nums1, nums2);
    printVector(intersec);
}

// 输出结果：
// 2 2 3
```

为什么输出是 2 2 3？应该是 2 3 才对。我单步调试了一下，在遍历 nums2 第一个元素 2 和 nums1 第二个元素 2 时，在程序的第 13 行执行后，mn1.isMarked = true，但查看 markedNums1 的 vector 结构，却仍是 false，见下图。所以导致 markedNums1 里的元素没有标记成功，输出结果为 2 2 3。

![](/images/leetcode/350-intersection-of-two-arrays-ii/mn1-bug.png)

为什么会这样？

我们修改一下程序，并把 mn1 和 markedNums1[i1] 的地址打印出来看看：

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        vector<MarkedNum> markedNums1 = markNums(nums1);
        vector<int> intersec;
        for (int ele2 : nums2) {
            for (int i1 = 0; i1 < markedNums1.size(); i1++) {
                MarkedNum mn1 = markedNums1[i1];
                if (! mn1.isMarked && mn1.num == ele2) {
                    mn1.isMarked = true;
                    intersec.push_back(mn1.num);
                    cout << "            mn1 地址：" << &mn1 << endl;
                    cout << "markedNums1[i1] 地址：" << &markedNums1[i1] << endl << endl;
                    break;
                }
            }
        }
        return intersec;
    }

private:
    struct MarkedNum {
        int num;
        bool isMarked;
    };

    vector<MarkedNum> markNums(vector<int>& nums) {
        int size = nums.size();
        vector<MarkedNum> markedNums(size);
        for (int i = 0; i < nums.size(); i++) {
            markedNums[i].num = nums[i];
            markedNums[i].isMarked = false;
        }
        return markedNums;
    }
};

void printVector(vector<int>& v) {
    for (int ele : v) {
        cout << ele << ' ';
    }
    cout << endl;
}

int main() {
    // 初始化 nums1
    vector<int> nums1;
    nums1.push_back(1);
    nums1.push_back(2);
    nums1.push_back(1);
    nums1.push_back(3);
    
    // 初始化 nums2
    vector<int> nums2;
    nums2.push_back(2);
    nums2.push_back(2);
    nums2.push_back(3);

    // 计算 nums1, num2 交集
    Solution sol;
    vector<int> intersec = sol.intersect(nums1, nums2);
    printVector(intersec);
}

// 输出结果：
//             mn1 地址：0028FC54
// markedNums1[i1] 地址：005DBCD0
// 
//             mn1 地址：0028FC54
// markedNums1[i1] 地址：005DBCD0
// 
//             mn1 地址：0028FC54
// markedNums1[i1] 地址：005DBCE0
// 
// 2 2 3
```

<a name="local-variables-address-in-a-loop"></a>

可以看到，mn1 的三个地址都是相同的 0028FC54（我在[这篇文章](http://syawlaus.github.io/blog/c++/local-variables-address-in-a-loop/)详细分析这个问题），而 markedNums1[i1] 的前两个地址都是 005DBCD0，说明该 markedNums1[i1] 没有标记上。第三个地址是 005DBCE0，说明我们使用 mn1 操作的不是 markedNums1 本身的元素。所以代码应该去掉 mn1，而直接操作 markedNums1[i1]：

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        vector<MarkedNum> markedNums1 = markNums(nums1);
        vector<int> intersec;
        for (int ele2 : nums2) {
            for (int i1 = 0; i1 < markedNums1.size(); i1++) {
                if (! markedNums1[i1].isMarked && markedNums1[i1].num == ele2) {
                    markedNums1[i1].isMarked = true;
                    intersec.push_back(markedNums1[i1].num);
                    cout << "markedNums1[i1] 地址：" << &markedNums1[i1] << endl;
                    break;
                }
            }
        }
        return intersec;
    }

private:
    struct MarkedNum {
        int num;
        bool isMarked;
    };

    vector<MarkedNum> markNums(vector<int>& nums) {
        int size = nums.size();
        vector<MarkedNum> markedNums(size);
        for (int i = 0; i < nums.size(); i++) {
            markedNums[i].num = nums[i];
            markedNums[i].isMarked = false;
        }
        return markedNums;
    }
};

void printVector(vector<int>& v) {
    for (int ele : v) {
        cout << ele << ' ';
    }
    cout << endl;
}

int main() {
    // 初始化 nums1
    vector<int> nums1;
    nums1.push_back(1);
    nums1.push_back(2);
    nums1.push_back(1);
    nums1.push_back(3);
    
    // 初始化 nums2
    vector<int> nums2;
    nums2.push_back(2);
    nums2.push_back(2);
    nums2.push_back(3);

    // 计算 nums1, num2 交集
    Solution sol;
    vector<int> intersec = sol.intersect(nums1, nums2);
    printVector(intersec);
}

// 输出结果：
// markedNums1[i1] 地址：003EBCD0
// markedNums1[i1] 地址：003EBCE0
// 2 3
```

提交到 Leetcode，Accepted! :) 运行时间为 52ms。

## 小结

我们把所有代码都整理在一起，方便对比和以后回顾思路二中发现的问题：

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        bool* marks = markNumsArr(nums1);
        vector<int> intersec;
        for (int ele2 : nums2) {
            for (int i1 = 0; i1 < nums1.size(); i1++) {
                int ele1 = nums1[i1];
                if (! marks[i1] && ele1 == ele2) {
                    marks[i1] = true;
                    intersec.push_back(ele1);
                    break;
                }
            }
        }
        return intersec;
    }

    vector<int> intersect2(vector<int>& nums1, vector<int>& nums2) {
        vector<MarkedNum> markedNums1 = markNumsVec(nums1);
        vector<int> intersec;
        for (int ele2 : nums2) {
            // 方式一
            //for (MarkedNum mn1 : markedNums1) {             // 有问题
            //    if (! mn1.isMarked && mn1.num == ele2) {
            //        mn1.isMarked = true;                    // 有问题
            //        intersec.push_back(mn1.num);
            //        break;
            //    }
            //}

            // 方式二
            //for (int i1 = 0; i1 < markedNums1.size(); i1++) {
            //    MarkedNum mn1 = markedNums1[i1];            // 有问题
            //    if (! mn1.isMarked && mn1.num == ele2) {
            //        mn1.isMarked = true;                    // 有问题
            //        intersec.push_back(mn1.num);
            //        cout << "            mn1 地址：" << &mn1 << endl;
            //        cout << "markedNums1[i1] 地址：" << &markedNums1[i1] << endl << endl;
            //        break;
            //    }
            //}

            // 方式三
            for (int i1 = 0; i1 < markedNums1.size(); i1++) {
                if (! markedNums1[i1].isMarked && markedNums1[i1].num == ele2) {
                    markedNums1[i1].isMarked = true;        // 正确
                    intersec.push_back(markedNums1[i1].num);
                    cout << "markedNums1[i1] 地址：" << &markedNums1[i1] << endl;
                    break;
                }
            }
        }
        return intersec;
    }

private:
    // intersect 使用
    bool* markNumsArr(vector<int>& nums) {
        // new
        int size = nums.size();
        bool* marks = new bool[size];

        // init
        for (int i = 0; i < size; i++) {
            marks[i] = false;
        }
        return marks;
    }

    // intersect2 使用
    struct MarkedNum {
        int num;
        bool isMarked;
    };

    vector<MarkedNum> markNumsVec(vector<int>& nums) {
        int size = nums.size();
        vector<MarkedNum> markedNums(size);
        for (int i = 0; i < nums.size(); i++) {
            markedNums[i].num = nums[i];
            markedNums[i].isMarked = false;
        }
        return markedNums;
    }
};

void printVector(vector<int>& v) {
    for (int ele : v) {
        cout << ele << ' ';
    }
    cout << endl;
}

int main() {
    // 初始化 nums1
    vector<int> nums1;
    nums1.push_back(1);
    nums1.push_back(2);
    nums1.push_back(1);
    nums1.push_back(3);
    
    // 初始化 nums2
    vector<int> nums2;
    nums2.push_back(2);
    nums2.push_back(2);
    nums2.push_back(3);

    // 计算 nums1, num2 交集
    Solution sol;
    vector<int> intersec = sol.intersect2(nums1, nums2);
    printVector(intersec);
}
```
