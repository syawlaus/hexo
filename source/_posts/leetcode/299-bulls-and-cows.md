title: Leetcode 题解 - 299. Bulls and Cows
date: 2016-10-28 23:40
categories: Leetcode
---

#题目

你正在与朋友玩「[公牛与母牛](https://en.wikipedia.org/wiki/Bulls_and_Cows)」的游戏，规则如下：你写下一个秘密数字让朋友猜。你的朋友每猜一次，你都提示他猜的数有多少数位的数字和位置都猜对了（即“公牛”），以及有多少数位的数字猜对，但位置不对（即“母牛”）。你的朋友不断修改猜测值，直到与你的秘密数字相同。

<!-- more -->

**示例**：

    你的秘密数字："1807"
    朋友猜的数字："7810"

提示：1 个公牛，3 个母牛（公牛是 8，母牛是 0、1、7）。

编写一个函数，根据你的秘密数字和朋友猜的数字来返回提示，使用 A 表示公牛，使用 B 表示母牛。在上面的示例中，你的函数应该返回 “1A3B”。

请注意，秘密数字和朋友猜的数字都有可能包含重复数位，例如：

    你的秘密数字："1123"
    朋友猜的数字："0111"

这种情况下，朋友猜的数字的第一个 1 是公牛，第二或第三个 1 是母牛，你的函数应该返回 "1A1B"。

你可以假设秘密数字和朋友猜的数字都只包含数字，并长度相等。

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```cpp
class Solution {
public:
    string getHint(string secret, string guess) {
        
    }
};
```

我的思路是，把 secret 和 guess 转化为 char secretArr[] 和 char guessArr[]，同时遍历。如果 guessArr[i] == secretArr[i]，说明 guessArr 的第 i 位是公牛，则记录到 int numA 里。如果 guessArr[i] != secretArr[i]，则把 guessArr[i] 和 secretArr[i] 分别保存到 `mapGuess<char, int>` 和 `mapSecret<char, int>` 中。遍历完成后，根据 mapGuess和 mapSecret 计算出 int numB。

伪代码如下：

```
string getHint(string secret, string guess) {
    char secretArr[] = secret.toCharArray();
    char guessArr[] = guess.toCharArray();
    int len = secret.length();

    int numA = 0;
    int numB = 0;
    map<char, int> mapGuess;
    map<char, int> mapSecret;

    // 遍历 guess 和 secret
    for (int i = 0; i < len; i++) {
        if (guessArr[i] == secretArr[i]) {  // guessArr[i] 是公牛
            numA++;
        }
        else {                              // guessArr[i] 是母牛
            addToMap(mapGuess, guessArr[i]);
            addToMap(mapSecret, secretArr[i]);
        }
    }

    // 遍历 mapGuess
    for (char guessChar : mapGuess.keys()) {
        for (char secretChar : mapSecret.keys()) {
            if (guessChar == secretChar) {
                numB += min(mapGuess.getValue(guessChar), mapSecret.getValue(secretChar));
            }
        }
    }

    return numA + "A" + numB + "B";
}
```

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

// 求两个数的较小值
#define MIN_TWO(a, b) (a < b ? a : b)

void incrementKeyCountInMap(map<char, int>& mapp,
                            char key) {
    if (key == NULL) {
        return;
    }

    map<char, int>::iterator iter = mapp.find(key);

    // 找不到 key，添加 <key, val>
    if (iter == mapp.end()) {
        mapp.insert(pair<char, int>(key, 1));
    }
    // 找到 key，val++
    else {
        (iter->second)++;
    }
}

class Solution {
public:
    string getHint(string secret, string guess) {
        const char *secretArr = secret.c_str();
        const char *guessArr = guess.c_str();
        int len = secret.length();

        int numA = 0;
        int numB = 0;
        map<char, int> mapGuess;
        map<char, int> mapSecret;

        // 遍历 guess 和 secret
        for (int i = 0; i < len; i++) {
            if (guessArr[i] == secretArr[i]) {  // guessArr[i] 是公牛
                numA++;
            }
            else {                              // guessArr[i] 是母牛
                incrementKeyCountInMap(mapGuess, guessArr[i]);
                incrementKeyCountInMap(mapSecret, secretArr[i]);
            }
        }

        // 遍历 mapGuess
        for (map<char, int>::iterator iterGuess = mapGuess.begin(); iterGuess != mapGuess.end(); iterGuess++) {
            for (map<char, int>::iterator iterSecret = mapSecret.begin(); iterSecret != mapSecret.end(); iterSecret++) {
                char guessChar = iterGuess->first;
                char secretChar = iterSecret->first;
                if (guessChar == secretChar) {
                    int guessCount = iterGuess->second;
                    int secretCount = iterSecret->second;
                    numB += MIN_TWO(guessCount, secretCount);
                }
            }
        }

        return to_string(numA) + "A" + to_string(numB) + "B";
    }
};

int main() {
    Solution sol;
    cout << sol.getHint("1807", "7810") << endl;
    cout << sol.getHint("1123", "0111") << endl;
}

// 输出结果：
// 1A3B
// 1A1B
```

提交到 Leetcode，Accepted! :) 运行时间为 19ms。
