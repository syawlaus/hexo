title: Leetcode 题解 - 121. Best Time to Buy and Sell Stock
date: 2016-07-24 23:30
categories: Leetcode
---

#题目

你有一个数组，第 i 个元素表示给定的股票在第 i 天价格。

如果你只能最多完成一次交易（买入或卖出一份股票），设计一个算法计算出最大利润。

<!-- more -->

**示例**：

示例一：

> 输入：[7, 1, 5, 3, 6, 4]
> 输出：5
> 利润最大值是 6 - 1 = 5（不是 7 - 1 = 6，因为卖出价格要大于买入价格）

示例二：

> 输入：[7, 6, 4, 3, 1]
> 输出：0
> 这种情况下，不能完成交易，因为最大利润 = 0。

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```
class Solution {
public:
    int maxProfit(vector<int>& prices) {

    }
};
```

##思路一（Time Limit Exceeded）

我们知道，交易股票的利润 = 卖出价格 - 买入价格。数组给出的第 i 天的股票价格。想要利润最大，即是在某天 m 以最低价格买入，在某天 n 以最高价格卖出。

那么代码的思路是，遍历数组，遍历到第 i 个元素时，用 i 分别减去前 i - 1 个元素，记录最大值。在遍历数组完成后，最大值即是最大利润。

伪代码如下：

```
int maxProfit(vector<int>& prices) {
    int max = 0;
    for (int i = 0; i < prices.size(); i++) {   // 卖出价格
        for (int j = 0; j < i; j++) {           // 买入价格
            int profit = prices[i] - prices[j];
            if (profit > max) {
                max = profit;
            }
        }
    }
    return max;
}
```

提交到 Leetcode，Time Limit Exceeded! 对于输入 [10000, 9999, 9998, 9997, 9996, ... , 2, 1] 超时了。上面的算法的时间复杂是 O(n<sup>2</sup>)。

##思路二（Wrong Answer）

我们注意两个 for 的 i, j，i 总是比 j 大。这是当然的，对于题目来说，我们只能第 i 天买入股票，在第 i + x 天卖出股票（x > 0），卖出的时间只能比买入的时间晚。

我想到了一个 O(n) 的思路：

* 先遍历一次数组，找出最小值 minBuyPrice 及位置 minBuyIndex
* 再遍历数组的 [minBuyIndex + 1, prices.size() - 1]，找出最大值的位置 maxSellPrice
* 最大利润 = maxSellPrice - minBuyPrice

这个思路也是不对的，对于 [7, 100, 200, 1, 5, 3, 6, 4]，我的算法会输出 5，但正确的应该是 193。

##思路三（Accepted）

第三种思路，遍历一次数组，每遍历到一个元素，如果比 minBuyPrice 小，更新 minBuyPrice，然后当前元素值 - minBuyPrice。

伪代码如下：

```
int maxProfit(vector<int>& prices) {
    int minBuyPrice = LONG_MAX;     // int 的最大值，保证所有元素都不大于 minBuyPrice
    int max_profit = 0;
    for (int i = 0; i < prices.size(); i++) {
        // 更新 minBuyPrice
        if (prices[i] < minBuyPrice) {
            minBuyPrice = prices[i];
        }
        int profit = prices[i] - minBuyPrice;
        if (profit > max_profit) {
            max_profit = profit;
        }
    }
    return max_profit;
}
```

代码如下：

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    //int maxProfit(vector<int>& prices) {    // too slow
    //    int max = 0;
    //    for (int i = 0; i < prices.size(); i++) {   // 卖出价格
    //        for (int j = 0; j < i; j++) {           // 买入价格
    //            int profit = prices[i] - prices[j];
    //            if (profit > max) {
    //                max = profit;
    //            }
    //        }
    //    }
    //    return max;
    //}

    //int maxProfit2(vector<int>& prices) {   // wrong answer
    //    int size = prices.size();
    //    int minBuyIndex = -1;
    //    int minBuyPrice = LONG_MAX;
    //    int maxSellPrice = 0;

    //    // 找出最小买入价格
    //    for (int i = 0; i < size; i++) {
    //        if (prices[i] < minBuyPrice) {
    //            minBuyIndex = i;
    //            minBuyPrice = prices[i];
    //        }
    //    }

    //    // 找出最大卖出价格
    //    for (int i = minBuyIndex + 1; i < size; i++) {
    //        if (prices[i] > maxSellPrice) {
    //            maxSellPrice = prices[i];
    //        }
    //    }
    //    
    //    // 计算利润
    //    int max_profit = maxSellPrice - minBuyPrice;
    //    return (max_profit > 0 ? max_profit : 0);
    //}

    int maxProfit3(vector<int>& prices) {   // Accepted
        int minBuyPrice = LONG_MAX;         // int 的最大值，保证所有元素都不大于 minBuyPrice
        int max_profit = 0;
        for (int i = 0; i < prices.size(); i++) {
            int currPrice = prices[i];
            // 更新 minBuyPrice
            if (currPrice < minBuyPrice) {
                minBuyPrice = currPrice;
            }
            int profit = currPrice - minBuyPrice;
            if (profit > max_profit) {
                max_profit = profit;
            }
        }
        return max_profit;
    }
};

int main() {
    vector<int> prices;
    prices.push_back(7);
    prices.push_back(100);
    prices.push_back(200);
    prices.push_back(1);
    prices.push_back(5);
    prices.push_back(3);
    prices.push_back(6);
    prices.push_back(4);

    vector<int> prices2;
    prices2.push_back(7);
    prices2.push_back(6);
    prices2.push_back(4);
    prices2.push_back(3);
    prices2.push_back(1);

    Solution sol;
    cout << sol.maxProfit3(prices) << endl;
    cout << sol.maxProfit3(prices2) << endl;
}

// 输出结果：
// 193
// 0
```

提交到 Leetcode，Accepted! :) 运行时间为 8ms。
