title: Leetcode 题解 - 205. Isomorphic Strings
date: 2016-10-29 18:10
categories: Leetcode
---

# 题目

给定两个字符串 s 和 t，判断它们是否同形。

同形字符串即是 s 中的字符可以替换成为 t。

所有出现的字符都必须替换为另外一个字符，且保留其顺序。两个字符不能映射到同一字符，但一个字符能映射到自身。

<!-- more -->

**示例**：

给定 "egg"，"add"，return true。

给定 "foo"，"bar"，return false。

给定 "paper"，"title"，return true。

**注意**：

你可以假设 s 和 t 长度相同。

**难度**：容易

**编程语言**：C++

---

# 分析

程序框架为：

```cpp
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        
    }
};
```

我对同形字符串的理解是，如果 s 的字符在一一映射为另一个字符（可以为本字符自身）后能变为 t，那么 s 和 t 同形。所以需要一个数据结构来记录 s 和 t 一一映射的关系，可以使用 `map<char, char>`。

思路是，遍历 s 字符 sc，如果 sc 首次出现，则记录到 map 里，key = sc，value = tc，注意 value 不能已被使用。如果 sc 非首次出现，判断 map.getValue(sc) 是否等于 tc，等于继续遍历，否则 return false。

简单来说，即是一个 sc 不能对应两个 tc，两个 sc 不能对应同一个 tc。

代码如下：

```cpp
#include <map>
#include <iostream>
using namespace std;

class Solution {
public:
    bool isIsomorphic(string s, string t) {
        const char *sArr = s.c_str();
        const char *tArr = t.c_str();
        int len = s.length();
        map<char, char> isomoMap;

        for (int i = 0; i < len; i++) {
            char sc = sArr[i];
            char tc = tArr[i];
            if (isomoMap.find(sc) == isomoMap.end()) {      // sc 不存在，判断新添加的 sc 的 tc 是否已被使用
                if (containsValueInMap(isomoMap, tc)) {
                    return false;
                }
                isomoMap.insert(pair<char, char>(sc, tc));
            }
            else {      // sc 已存在，判断同一个 sc 是否对应两个不同的 tc
                char old_tc = (isomoMap.find(sc))->second;
                if (tc != old_tc) {
                    return false;
                }
            }
        }
        return true;
    }

private:
    bool containsValueInMap(map<char, char>& mapp,
                            char val) {
        for (map<char, char>::iterator iter = mapp.begin(); iter != mapp.end(); iter++) {
            char oldVal = iter->second;
            if (val == oldVal) {
                return true;
            }
        }
        return false;
    }
};

int main() {
    Solution sol;
    cout << sol.isIsomorphic("", "") << endl;
    cout << sol.isIsomorphic("egg", "add") << endl;
    cout << sol.isIsomorphic("foo", "bar") << endl;
    cout << sol.isIsomorphic("paper", "title") << endl;
    cout << sol.isIsomorphic("ab", "aa") << endl;
}

// 输出结果：
// 1
// 1
// 0
// 1
// 0
```

提交到 Leetcode，Accepted! 运行时间为 46ms。
