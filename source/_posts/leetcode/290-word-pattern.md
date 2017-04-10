title: Leetcode 题解 - 290. Word Pattern
date: 2016-11-03 23:50
categories: Leetcode
---

# 题目

给定一个模式 pattern 和一个字符串 str，判断 str 是否与 pattern 的模式一致。

这里的**模式一致**表示完全匹配，即 pattern 里的一个字符与 str 里的一个非空单词存在双向映射关系。

<!-- more -->

**示例**：

1. pattern = "abba", str = "dog cat cat dog" 应返回 true。
2. pattern = "abba", str = "dog cat cat fish" 应返回 false。
3. pattern = "aaaa", str = "dog cat cat dog" 应返回 false。
4. pattern = "abba", str = "dog dog dog dog" 应返回 false。

**注意**：

你可以假设 pattern 仅包含小写字母，str 的单词也仅包含小写字母且用空格分隔各单词。

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    bool wordPattern(string pattern, string str) {
        
    }
};
```

思路是使用两个 map 作双向映射，分别是 `map<char, string> mapA` 和 `map<string, char> mapB`。程序执行逻辑为：

* 每当遍历到 pattern 的一个字符 c 和 str 中的一个单词 word 时，如果 c 不存在于 mapA 且 word 不存在于 mapB，则各自添加。
* 如果 c 存在于 mapA 且 word 存在于 mapB 且对应关系相同，则继续遍历，否则返回 false。

注意，因为题目没有说明 c 的数量与 word 数量相等，所以代码里判断到数量不同，会返回 false。

代码如下：

```cpp
#include <frequently-used-code-snippets.h>

class Solution {
public:
    // 思路：
    //     1. 首先把 str 分割出一个个单词，保存到一个 vector<string> 里。
    //     2. 如果 string[] 长度跟 pattern 长度不同，return false
    //     3. 如果长度相同，开始遍历比较
    //        如果 mapA 不存在 c 且 mapB 不存在 word，则分别添加进去
    //        如果 mapA 存在 c 且对应的 value == word，且 mapB 存在 word 且对应的 value == c，则继续遍历下一个 c 和 word
    //        否则 return false
    bool wordPattern(string pattern, string str) {
        // 第 1 步
        vector<string> words = splitString(str);

        // 第 2 步
        if (words.size() != pattern.length()) {
            return false;
        }

        // 第 3 步
        map<char, string> mapA;
        map<string, char> mapB;
        for (int i = 0; i < words.size(); i++) {
            char c = pattern[i];
            string word = words[i];
            int result = matchBijection(mapA, mapB, c, word);
            if (result == 1) {
                continue;
            }
            if (result == 0) {
                addBijection(mapA, mapB, c, word);
            }
            else {
                return false;
            }
        }
        return true;
    }

private:
    // 把 str 分隔为 vector<string>，分隔符为空格
    vector<string> splitString(string str) {
        str += " ";     // 为了把最后一次单词 push_back 到 vector
        vector<string> words;
        char split = ' ';
        string word = "";
        for (int i = 0; i < str.length(); i++) {
            char c = str[i];
            if (c != split) {
                word += c;
            }
            else {
                if (word != "") {
                    words.push_back(word);
                    word = "";
                }
            }
        }
        return words;
    }

    // 返回值：
    //     0：两者不存在双射
    //     1：两者存在双射
    //    -1：两者存在一对多映射
    int matchBijection(map<char, string>& mapA,
                       map<string, char>& mapB,
                       char c,
                       string word) {
        if (! containsKeyInMap(mapA, c) &&
            ! containsKeyInMap(mapB, word)) {
            return 0;
        }

        map<char, string>::iterator iterA = mapA.find(c);
        map<string, char>::iterator iterB = mapB.find(word);
        if (iterA != mapA.end() && iterA->second == word &&
            iterB != mapB.end() && iterB->second == c) {
            return 1;
        }
        return -1;
    }

    // 把 <c, word> 添加到 mapA
    // 把 <word, c> 添加到 mapB
    void addBijection(map<char, string>& mapA,
                      map<string, char>& mapB,
                      char c,
                      string word) {
        mapA[c] = word;
        mapB[word] = c;
    }
};

int main() {
    Solution sol;
    cout << sol.wordPattern("abba", "dog cat cat dog") << endl;
    cout << sol.wordPattern("abba", "dog cat cat fish") << endl;
    cout << sol.wordPattern("aaaa", "dog cat cat dog") << endl;
    cout << sol.wordPattern("abba", "dog dog dog dog") << endl;
}

// 输出结果：
// 1
// 0
// 0
// 0
```

提交到 Leetcode，Accepted! :) 运行时间为 3ms。
