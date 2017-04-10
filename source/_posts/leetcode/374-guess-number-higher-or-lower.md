title: Leetcode 题解 - 374. Guess Number Higher or Lower
date: 2016-10-28 14:00
categories: Leetcode
---

# 题目

我们正在玩一个猜数游戏，游戏规则如下：

我从 1 到 n 选择一个数，你猜我选择了哪个数。

每次你猜错了，我会告诉你你猜的数是大了还是小了。

你可以调用预定义 API guess(int num)，它有三个不同的返回值：

    -1：我选择的数比你猜的小，即是你猜大了
     1：我选择的数比你猜的大，即是你猜小了
     0：恭喜，你猜中了！

<!-- more -->

**示例**：

n = 10，我选择 6，你的函数应该返回 6。

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
// Forward declaration of guess API.
// @param num, your guess
// @return -1 if my number is lower, 1 if my number is higher, otherwise return 0
int guess(int num);

class Solution {
public:
    int guessNumber(int n) {

    }
};
```

我的思路是，low = 1，high = n，每次猜错就减小一半的范围。每次猜的数是 [low, high] 的中间值 mid。如果 mid 小了，则下次需要往大猜，low = mid，mid = (low + high) / 2。如果 mid 大了，则下次需要往小猜，high = (low + high) / 2，mid = (low + high) / 2。猜到最后会剩下两个相邻的数，一奇一偶。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

#define GUESS_LOWER_NEXT_TIME -1
#define GUESS_HIGHER_NEXT_TIME 1
#define GOT_IT 0

#define RANGE 2147483647
#define PICK  2147483647

// Forward declaration of guess API.
// @param num, your guess
// @return -1 if my number is lower, 1 if my number is higher, otherwise return 0
int guess(int num) {
    if (PICK < num) {
        return -1;
    }
    if (PICK > num) {
        return 1;
    }
    return 0;
}

class Solution {
public:
    int guessNumber(int n) {
        int low = 1;
        int high = n;
        int myGuess = getAverage(low, high);

        bool twoNumsLeft = false;   // 猜到最后会剩下两个相邻的数，一奇一偶
        int result = guess(myGuess);
        while (result != GOT_IT) {
            if (result == GUESS_LOWER_NEXT_TIME) {  // 下次往低猜
                high = getAverage(low, high);
            }
            else {                                  // 下次往高猜
                if (twoNumsLeft) {
                    myGuess = high;
                    break;
                }
                else {
                    low = myGuess;
                }
            }

            if (high - low <= 1) {
                twoNumsLeft = true;
            }

            // 继续猜
            myGuess = getAverage(low, high);
            result = guess(myGuess);
        }
        return myGuess;
    }

private:
    int getAverage(int low, int high) {
        //return (low + high) / 2;    // 这样会 int 溢出
        return low + (high - low) / 2;
    }
};

int main() {
    Solution sol;
    cout << sol.guessNumber(RANGE) << endl;
}
```

提交到 Leetcode，Accepted! :)，运行时间为 0ms。
