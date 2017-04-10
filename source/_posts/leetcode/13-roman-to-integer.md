title: Leetcode 题解 - 13. Roman to Integer
date: 2016-07-09 17:30
categories: Leetcode
---

#题目

给定一个罗马数字，转为整型数字。

输入保证在 0～3999 之间。

<!-- more -->

**难度**：容易

**编程语言**：C++

---

#分析

程序框架为：

```
class Solution {
public:
    int romanToInt(string s) {
        
    }
};
```

我们先看看罗马数字，找出其中规律：

    I              1
    II             2
    III            3
    IV             4
    V              5
    VI             6
    VII            7
    VIII           8
    IX             9
    X             10

    XI            11
    XII           12
    XIII          13
    XIV           14
    XV            15
    XVI           16
    XVII          17
    XVIII         18
    XIX           19
    XX            20

    XXX           30
    XL            40
    L             50
    LX            60
    LXX           70
    LXXX          80
    XC            90
    C            100

    CC           200
    CCC          300
    CD           400
    D            500
    DC           600
    DCC          700
    DCCC         800
    CM           900
    M           1000

    MM          2000
    MMM         3000
    MMMCMXCIX   3999

0～3999 内 7 种不同的符号：

    I      1
    V      5
    X     10
    L     50
    C    100
    D    500
    M   1000

可以看到，从上到下，每种符号表示的数字越来越大。我们再看看 5 的 10 次幂的符号：

    V      5
    L     50
    D    500

* 对于 V = 5，则 IV = 5 - 1 = 4
* 对于 L = 50，则 XL = 50 - 10 = 40
* 对于 D = 500，则 CD = 500 - 100 = 400

romanToInt 我的思路是：

1. 把罗马数字分隔出表示个／十／百／千位的各个子字符串
2. 把各个子字符串转为对应的个／十／百／千位的整型数字
3. 把各个整型数字相加

##计算个位数

对于第一步，怎么判断哪些子串代表个位（以个位举例，十／百／千位同理）？

    I       1
    II      2
    III     3
    IV      4
    V       5
    VI      6
    VII     7
    VIII    8
    IX      9

可以看到：

* I 后面可以接 I／V／X
* V 后面可以接 I

所以，当检查到当前字符是 I／V，都、能是个位数的起始字符。那么：

* 如果当前字符是 I，那么一直到最后一个 I，或下一个 V／X，就是个位数的结束字符
* 如果当前字符是 V，那么一直到最后一个 I，就是个位数的结束字符

在保证了子串形成的肯定是个位数后，接下来我们计算这个个位数是多少：

* 如果起始字符是 I，如果结束字符是 I，那么把所有 I 相加
* 如果起始字符是 I，如果结束字符是 V／X，那么 V／X - I
* 如果起始字符是 V，那么结束字符是 I，那么把 V 跟所有 I 相加

那么我们先编写只能处理个位数的 romanToInt，伪代码如下：

```
// 输入范围为 1～9
int romanToInt(string s) {
    if (起始字符是 I) {
        if (结束字符是 I) {
            return 所有 I 相加;
        }
        else if (结束字符是 V) {
            return V - I;
        }
        else {    // 结束字符是 X
            return X - I;
        }
    }
    else {  // 起始字符是 V
        return V + 所有 I 相加;
    }
}
```

代码如下：

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Roman_Num {
    int I = 1;
    int V = 5;
    int X = 10;
    int L = 50;
    int C = 100;
    int D = 500;
    int M = 1000;
};

class Solution {
public:
    Roman_Num romanNum;

    int romanToInt(string s) {
        int len = s.length();
        char headCh = s[0];
        char tailCh = s[len - 1];

        if (headCh == 'I') {
            if (tailCh == 'I') {
                return len * romanNum.I;
            }
            else if (tailCh == 'V') {
                return romanNum.V - romanNum.I;
            }
            else {      // 结束字符是 X
                return romanNum.X - romanNum.I;
            }
        }
        else {      // 起始字符是 V
            return romanNum.V + (len - 1) * romanNum.I;
        }
    }
};

int main() {
    Solution sol;
    cout << sol.romanToInt("I") << endl;
    cout << sol.romanToInt("II") << endl;
    cout << sol.romanToInt("III") << endl;
    cout << sol.romanToInt("IV") << endl;
    cout << sol.romanToInt("V") << endl;
    cout << sol.romanToInt("VI") << endl;
    cout << sol.romanToInt("VII") << endl;
    cout << sol.romanToInt("VIII") << endl;
    cout << sol.romanToInt("IX") << endl;
}

// 输出结果：
// 1
// 2
// 3
// 4
// 5
// 6
// 7
// 8
// 9
```

So far so good! 继续添加到十位数的检测。

##计算十位数

伪代码先写出框架：

```
int romanToInt(string s) {
    int splitIdx = 0;
    string s10 = splitS10(s, splitIdx);    // 在执行完 splitS10 后，记录当前遍历到哪个 index，方便 splitS1 使用
    string s1 =  splitS1(s, splitIdx);

    int i10 = convertToInt(s10, 10);    // 第二个参数表示十位数
    int i1 = convertToInt(s1, 1);       // 第二个参数表示个位数

    return i10 + i1;
}
```

接下来就是 splitS10，十位数的起始字符是 X／L，结束字符为 X／L／C，我们把符合这些条件的 char 都保存起来。

```
// 把 romanNum 的十位数分离出来
// 输入：原罗马数字，index 引用（可理解为用于返回两个数据）
// 输出：组成十位数的子字符串
string splitS10(string romanNum, int& splitIdx) {
    string s10 = "";
    int start = splitIdx;
    for (; splitIdx < romanNum.size(); splitIdx++) {
        char c = romanNum[splitIdx];
        if (c == 'X' || c == 'L' || c == 'C') {
            if (romanNum[start] != 'C') {    // C 不能开头，可以结尾，要符合这种情况
                s10 += c;
            }
        }
        else {
            break;
        }
    }
    return s10;
}
```

接下来就是 splitS1，个位数的起始字符是 I／V，结束字符为 I／V／X，我们把符合这些条件的 char 都保存起来。

```
// 把 romanNum 的个位数分离出来
// 输入：原罗马数字，index 引用（可理解为用于返回两个数据）
// 输出：组成个位数的子字符串
string splitS1(string romanNum, int& splitIdx) {
    string s1 = "";
    int start = splitIdx;
    for (; splitIdx < romanNum.size(); splitIdx++) {
        char c = romanNum[splitIdx];
        if (c == 'I' || c == 'V' || c == 'X') {
            if (romanNum[start] != 'X') {    // X 不能开头，可以结尾，要符合这种情况
                s1 += c;
            }
        }
        else {
            break;
        }
    }
    return s1;
}
```

接下来就是 convertToInt：

```
// 把罗马数字转为整型数字
// 输入：罗马数字，进制
// 输出：整型数字
int convertToInt(string romanNum, int scale) {
    int len = romanNum.length();
    if (len == 0) {
        return 0;
    }

    char headCh = romanNum[0];
    char tailCh = romanNum[len - 1];

    if (scale == 10) {
        return convertToInt10(romanNum);
    }
    else if (scale == 1) {
        return convertToInt1(romanNum);
    }
    return 0;
}
```

convertToInt10：

```
int convertToInt10(string s) {
    int len = s.length();
    if (len == 0) {
        return 0;
    }

    char headCh = s[0];
    char tailCh = s[len - 1];

    if (headCh == 'X') {
        if (tailCh == 'X') {
            return len * romanNum.X;
        }
        else if (tailCh == 'L') {
            return romanNum.L - romanNum.X;
        }
        else {      // 结束字符是 C
            return romanNum.C - romanNum.X;
        }
    }
    else {      // 起始字符是 L
        return romanNum.L + (len - 1) * romanNum.X;
    }
}
```

convertToInt1：

```
int convertToInt1(string s) {
    int len = s.length();
    if (len == 0) {
        return 0;
    }

    char headCh = s[0];
    char tailCh = s[len - 1];

    if (headCh == 'I') {
        if (tailCh == 'I') {
            return len * romanNum.I;
        }
        else if (tailCh == 'V') {
            return romanNum.V - romanNum.I;
        }
        else {      // 结束字符是 X
            return romanNum.X - romanNum.I;
        }
    }
    else {      // 起始字符是 V
        return romanNum.V + (len - 1) * romanNum.I;
    }
}
```

代码如下：

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Roman_Num {
    int I = 1;
    int V = 5;
    int X = 10;
    int L = 50;
    int C = 100;
    int D = 500;
    int M = 1000;
};

class Solution {
public:
    Roman_Num romanNum;

    int romanToInt(string s) {
        int splitIdx = 0;
        string s10 = splitS10(s, splitIdx);    // 在执行完 splitS10 后，记录当前遍历到哪个 index，方便 splitS1 使用
        string s1 = splitS1(s, splitIdx);

        int i10 = convertToInt(s10, 10);     // 第二个参数表示十位数
        int i1 = convertToInt(s1, 1);       // 第二个参数表示个位数

        return i10 + i1;
    }

    // 把 romanNum 的十位数分离出来
    // 输入：原罗马数字，index 引用（可理解为用于返回两个数据）
    // 输出：组成十位数的子字符串
    string splitS10(string romanNum, int& splitIdx) {
        string s10 = "";
        int start = splitIdx;
        for (; splitIdx < romanNum.size(); splitIdx++) {
            char c = romanNum[splitIdx];
            if (c == 'X' || c == 'L' || c == 'C') {
                if (romanNum[start] != 'C') {   // C 不能开头，可以结尾，要符合这种情况
                    s10 += c;
                }
            }
            else {
                break;
            }
        }
        return s10;
    }

    // 把 romanNum 的个位数分离出来
    // 输入：原罗马数字，index 引用（可理解为用于返回两个数据）
    // 输出：组成个位数的子字符串
    string splitS1(string romanNum, int& splitIdx) {
        string s1 = "";
        int start = splitIdx;
        for (; splitIdx < romanNum.size(); splitIdx++) {
            char c = romanNum[splitIdx];
            if (c == 'I' || c == 'V' || c == 'X') {
                if (romanNum[start] != 'X') {    // X 不能开头，可以结尾，要符合这种情况
                    s1 += c;
                }
            }
            else {
                break;
            }
        }
        return s1;
    }

    // 把罗马数字转为整型数字
    // 输入：罗马数字，进制
    // 输出：整型数字
    int convertToInt(string romanNum, int scale) {
        int len = romanNum.length();
        if (len == 0) {
            return 0;
        }

        char headCh = romanNum[0];
        char tailCh = romanNum[len - 1];

        if (scale == 10) {
            return convertToInt10(romanNum);
        }
        else if (scale == 1) {
            return convertToInt1(romanNum);
        }
        return 0;
    }

    int convertToInt10(string s) {
        int len = s.length();
        if (len == 0) {
            return 0;
        }

        char headCh = s[0];
        char tailCh = s[len - 1];

        if (headCh == 'X') {
            if (tailCh == 'X') {
                return len * romanNum.X;
            }
            else if (tailCh == 'L') {
                return romanNum.L - romanNum.X;
            }
            else {      // 结束字符是 C
                return romanNum.C - romanNum.X;
            }
        }
        else {      // 起始字符是 L
            return romanNum.L + (len - 1) * romanNum.X;
        }
    }

    int convertToInt1(string s) {
        int len = s.length();
        if (len == 0) {
            return 0;
        }

        char headCh = s[0];
        char tailCh = s[len - 1];

        if (headCh == 'I') {
            if (tailCh == 'I') {
                return len * romanNum.I;
            }
            else if (tailCh == 'V') {
                return romanNum.V - romanNum.I;
            }
            else {      // 结束字符是 X
                return romanNum.X - romanNum.I;
            }
        }
        else {      // 起始字符是 V
            return romanNum.V + (len - 1) * romanNum.I;
        }
    }
};

int main() {
    Solution sol;

    // 1-10
    cout << sol.romanToInt("I") << endl;
    cout << sol.romanToInt("II") << endl;
    cout << sol.romanToInt("III") << endl;
    cout << sol.romanToInt("IV") << endl;
    cout << sol.romanToInt("V") << endl;
    cout << sol.romanToInt("VI") << endl;
    cout << sol.romanToInt("VII") << endl;
    cout << sol.romanToInt("VIII") << endl;
    cout << sol.romanToInt("IX") << endl;
    cout << sol.romanToInt("X") << endl;
    cout << endl;

    // 10-20
    cout << sol.romanToInt("XI") << endl;
    cout << sol.romanToInt("XII") << endl;
    cout << sol.romanToInt("XIII") << endl;
    cout << sol.romanToInt("XIV") << endl;
    cout << sol.romanToInt("XV") << endl;
    cout << sol.romanToInt("XVI") << endl;
    cout << sol.romanToInt("XVII") << endl;
    cout << sol.romanToInt("XVIII") << endl;
    cout << sol.romanToInt("XIX") << endl;
    cout << sol.romanToInt("XX") << endl;
    cout << endl;

    // 20-30
    cout << sol.romanToInt("XXI") << endl;
    cout << sol.romanToInt("XXII") << endl;
    cout << sol.romanToInt("XXIII") << endl;
    cout << sol.romanToInt("XXIV") << endl;
    cout << sol.romanToInt("XXV") << endl;
    cout << sol.romanToInt("XXVI") << endl;
    cout << sol.romanToInt("XXVII") << endl;
    cout << sol.romanToInt("XXVIII") << endl;
    cout << sol.romanToInt("XXIX") << endl;
    cout << sol.romanToInt("XXX") << endl;
    cout << endl;

    // 30-40
    cout << sol.romanToInt("XXXI") << endl;
    cout << sol.romanToInt("XXXII") << endl;
    cout << sol.romanToInt("XXXIII") << endl;
    cout << sol.romanToInt("XXXIV") << endl;
    cout << sol.romanToInt("XXXV") << endl;
    cout << sol.romanToInt("XXXVI") << endl;
    cout << sol.romanToInt("XXXVII") << endl;
    cout << sol.romanToInt("XXXVIII") << endl;
    cout << sol.romanToInt("XXXIX") << endl;
    cout << sol.romanToInt("XL") << endl;
    cout << endl;

    // 40-50
    cout << sol.romanToInt("XLI") << endl;
    cout << sol.romanToInt("XLII") << endl;
    cout << sol.romanToInt("XLIII") << endl;
    cout << sol.romanToInt("XLIV") << endl;
    cout << sol.romanToInt("XLV") << endl;
    cout << sol.romanToInt("XLVI") << endl;
    cout << sol.romanToInt("XLVII") << endl;
    cout << sol.romanToInt("XLVIII") << endl;
    cout << sol.romanToInt("XLIX") << endl;
    cout << sol.romanToInt("L") << endl;
    cout << endl;

    // 50-60
    cout << sol.romanToInt("LI") << endl;
    cout << sol.romanToInt("LII") << endl;
    cout << sol.romanToInt("LIII") << endl;
    cout << sol.romanToInt("LIV") << endl;
    cout << sol.romanToInt("LV") << endl;
    cout << sol.romanToInt("LVI") << endl;
    cout << sol.romanToInt("LVII") << endl;
    cout << sol.romanToInt("LVIII") << endl;
    cout << sol.romanToInt("LIX") << endl;
    cout << sol.romanToInt("L") << endl;
    cout << endl;

    // 60-70
    cout << sol.romanToInt("LXI") << endl;
    cout << sol.romanToInt("LXII") << endl;
    cout << sol.romanToInt("LXIII") << endl;
    cout << sol.romanToInt("LXIV") << endl;
    cout << sol.romanToInt("LXV") << endl;
    cout << sol.romanToInt("LXVI") << endl;
    cout << sol.romanToInt("LXVII") << endl;
    cout << sol.romanToInt("LXVIII") << endl;
    cout << sol.romanToInt("LXIX") << endl;
    cout << sol.romanToInt("LX") << endl;
    cout << endl;

    // 70-80
    cout << sol.romanToInt("LXXI") << endl;
    cout << sol.romanToInt("LXXII") << endl;
    cout << sol.romanToInt("LXXIII") << endl;
    cout << sol.romanToInt("LXXIV") << endl;
    cout << sol.romanToInt("LXXV") << endl;
    cout << sol.romanToInt("LXXVI") << endl;
    cout << sol.romanToInt("LXXVII") << endl;
    cout << sol.romanToInt("LXXVIII") << endl;
    cout << sol.romanToInt("LXXIX") << endl;
    cout << sol.romanToInt("LXX") << endl;
    cout << endl;

    // 80-90
    cout << sol.romanToInt("LXXXI") << endl;
    cout << sol.romanToInt("LXXXII") << endl;
    cout << sol.romanToInt("LXXXIII") << endl;
    cout << sol.romanToInt("LXXXIV") << endl;
    cout << sol.romanToInt("LXXXV") << endl;
    cout << sol.romanToInt("LXXXVI") << endl;
    cout << sol.romanToInt("LXXXVII") << endl;
    cout << sol.romanToInt("LXXXVIII") << endl;
    cout << sol.romanToInt("LXXXIX") << endl;
    cout << sol.romanToInt("LXXX") << endl;
    cout << endl;

    // 90-99
    cout << sol.romanToInt("XCI") << endl;
    cout << sol.romanToInt("XCII") << endl;
    cout << sol.romanToInt("XCIII") << endl;
    cout << sol.romanToInt("XCIV") << endl;
    cout << sol.romanToInt("XCV") << endl;
    cout << sol.romanToInt("XCVI") << endl;
    cout << sol.romanToInt("XCVII") << endl;
    cout << sol.romanToInt("XCVIII") << endl;
    cout << sol.romanToInt("XCIX") << endl;
    cout << endl;
}

// 输出结果：
// 1
// 2
// 3
// 4
// 5
// 6
// 7
// 8
// 9
// 10
// 
// 11
// 12
// 13
// 14
// 15
// 16
// 17
// 18
// 19
// 20
// 
// 21
// 22
// 23
// 24
// 25
// 26
// 27
// 28
// 29
// 30
// 
// 31
// 32
// 33
// 34
// 35
// 36
// 37
// 38
// 39
// 40
// 
// 41
// 42
// 43
// 44
// 45
// 46
// 47
// 48
// 49
// 50
// 
// 51
// 52
// 53
// 54
// 55
// 56
// 57
// 58
// 59
// 50
// 
// 61
// 62
// 63
// 64
// 65
// 66
// 67
// 68
// 69
// 60
// 
// 71
// 72
// 73
// 74
// 75
// 76
// 77
// 78
// 79
// 70
// 
// 81
// 82
// 83
// 84
// 85
// 86
// 87
// 88
// 89
// 80
// 
// 91
// 92
// 93
// 94
// 95
// 96
// 97
// 98
// 99
```

So far so good! 继续添加到百位数的检测。

##重构代码

在计算百位数前，我们先整理一下代码。可以看到，splitS10 和 splitS1 几乎一模一样，convertToInt10 和 convertToInt1 几乎一模一样。有没有办法把 splitS10 和 splitS1 合并为函数签名为 string splitRomanNum(string s, int scale, int& splitIdx) 的函数呢？

我们先观察一下 splitS10 和 splitS1 的区别：

```
// 把 romanNum 的十位数分离出来
// 输入：原罗马数字，index 引用（可理解为用于返回两个数据）
// 输出：组成十位数的子字符串
string splitS10(string romanNum, int& splitIdx) {
    string s10 = "";
    int start = splitIdx;
    for (; splitIdx < romanNum.size(); splitIdx++) {
        char c = romanNum[splitIdx];
        if (c == 'X' || c == 'L' || c == 'C') {
            if (romanNum[start] != 'C') {   // C 不能开头，可以结尾，要符合这种情况
                s10 += c;
            }
        }
        else {
            break;
        }
    }
    return s10;
}

// 把 romanNum 的个位数分离出来
// 输入：原罗马数字，index 引用（可理解为用于返回两个数据）
// 输出：组成个位数的子字符串
string splitS1(string romanNum, int& splitIdx) {
    string s1 = "";
    int start = splitIdx;
    for (; splitIdx < romanNum.size(); splitIdx++) {
        char c = romanNum[splitIdx];
        if (c == 'I' || c == 'V' || c == 'X') {
            if (romanNum[start] != 'X') {    // X 不能开头，可以结尾，要符合这种情况
                s1 += c;
            }
        }
        else {
            break;
        }
    }
```

区别仅仅是，splitS10 使用 X／L／C，splitS1 使用 I／V／X。那么很简单的思路是，根据位数的不同（十位数或个位数），来配置使用 struct Roman_Num 里不同的值。代码如下：

```
string splitRomanNum(string romanNum, int scale, int& splitIdx) {
    // 配置
    char one, five, ten;
    if (scale == 10) {
        one = 'X';
        five = 'L';
        ten = 'C';
    }
    else {      // 个位数
        one = 'I';
        five = 'V';
        ten = 'X';
    }

    // split
    string s1 = "";
    int start = splitIdx;
    for (; splitIdx < romanNum.size(); splitIdx++) {
        char c = romanNum[splitIdx];
        if (c == one || c == five || c == ten) {
            if (romanNum[start] != ten) {    // ten 不能开头，可以结尾，要符合这种情况
                s1 += c;
            }
        }
        else {
            break;
        }
    }
    return s1;
}
```

再次运行程序，输出结果仍为 1～99。

接下来使用同样的方法，把 convertToInt10 和 convertToInt1 合并为函数签名为 int convertToInt(string romanNum, int scale) 的函数。代码如下：

```
int convertToInt(string s, int scale) {
    int len = s.length();
    if (len == 0) {
        return 0;
    }

    // 配置
    char one, five, ten;
    int rOne, rFive, rTen;
    if (scale == 10) {
        one = 'X';
        five = 'L';
        ten = 'C';
        rOne = romanNum.X;
        rFive = romanNum.L;
        rTen = romanNum.C;
    }
    else {      // 个位数
        one = 'I';
        five = 'V';
        ten = 'X';
        rOne = romanNum.I;
        rFive = romanNum.V;
        rTen = romanNum.X;
    }

    // convert
    char headCh = s[0];
    char tailCh = s[len - 1];

    if (headCh == one) {
        if (tailCh == one) {
            return len * rOne;
        }
        else if (tailCh == five) {
            return rFive - rOne;
        }
        else {      // 结束字符是 ten
            return rTen - rOne;
        }
    }
    else {      // 起始字符是 five
        return rFive + (len - 1) * rOne;
    }
}
```

再次运行程序，输出结果仍为 1～99。

重构后的完整程序：

```cpp
#include <iostream>
#include <string>
using namespace std;

struct Roman_Num {
    int I = 1;
    int V = 5;
    int X = 10;
    int L = 50;
    int C = 100;
    int D = 500;
    int M = 1000;
};

class Solution {
public:
    Roman_Num structRomanNum;

    int romanToInt(string s) {
        int splitIdx = 0;
        string s10 = splitRomanNum(s, 10, splitIdx);    // 在执行完 splitS10 后，记录当前遍历到哪个 index，方便 splitS1 使用
        string s1 = splitRomanNum(s, 1, splitIdx);

        int i10 = convertToInt(s10, 10);     // 第二个参数表示十位数
        int i1 = convertToInt(s1, 1);       // 第二个参数表示个位数

        return i10 + i1;
    }

    // 把 romanNum 的某位数分离出来
    // 输入：原罗马数字，千/百/十/个位数，index 引用（可理解为用于返回两个数据）
    // 输出：组成对应位数的子字符串
    string splitRomanNum(string romanNum, int scale, int& splitIdx) {
        // 配置
        char one, five, ten;
        if (scale == 10) {
            one = 'X';
            five = 'L';
            ten = 'C';
        }
        else {      // 个位数
            one = 'I';
            five = 'V';
            ten = 'X';
        }

        // split
        string s1 = "";
        int start = splitIdx;
        for (; splitIdx < romanNum.size(); splitIdx++) {
            char c = romanNum[splitIdx];
            if (c == one || c == five || c == ten) {
                if (romanNum[start] != ten) {    // ten 不能开头，可以结尾，要符合这种情况
                    s1 += c;
                }
            }
            else {
                break;
            }
        }
        return s1;
    }

    // 把罗马数字转为整型数字
    // 输入：罗马数字，进制
    // 输出：整型数字
    int convertToInt(string s, int scale) {
        int len = s.length();
        if (len == 0) {
            return 0;
        }

        // 配置
        char one, five, ten;
        int rOne, rFive, rTen;
        if (scale == 10) {
            one = 'X';
            five = 'L';
            ten = 'C';
            rOne = structRomanNum.X;
            rFive = structRomanNum.L;
            rTen = structRomanNum.C;
        }
        else {      // 个位数
            one = 'I';
            five = 'V';
            ten = 'X';
            rOne = structRomanNum.I;
            rFive = structRomanNum.V;
            rTen = structRomanNum.X;
        }

        // convert
        char headCh = s[0];
        char tailCh = s[len - 1];

        if (headCh == one) {
            if (tailCh == one) {
                return len * rOne;
            }
            else if (tailCh == five) {
                return rFive - rOne;
            }
            else {      // 结束字符是 ten
                return rTen - rOne;
            }
        }
        else {      // 起始字符是 five
            return rFive + (len - 1) * rOne;
        }
    }
};
```

##计算百位数

要计算百位数，修改 romanToInt 为：

```
int romanToInt(string s) {
    int splitIdx = 0;

    string s100 = splitRomanNum(s, 100, splitIdx);    // 在执行完 splitS100 后，记录当前遍历到哪个 index，方便 splitS10 使用
    string s10 = splitRomanNum(s, 10, splitIdx);      // 在执行完 splitS10 后，记录当前遍历到哪个 index，方便 splitS1 使用
    string s1 = splitRomanNum(s, 1, splitIdx);

    int i100 = convertToInt(s100, 100);     // 第二个参数表示百位数
    int i10 = convertToInt(s10, 10);        // 第二个参数表示十位数
    int i1 = convertToInt(s1, 1);           // 第二个参数表示个位数

    return i100 + i10 + i1;
}
```

在 splitRomanNum 检测百位数：

```
// 把 romanNum 的某位数分离出来
// 输入：原罗马数字，千/百/十/个位数，index 引用（可理解为用于返回两个数据）
// 输出：组成对应位数的子字符串
string splitRomanNum(string romanNum, int scale, int& splitIdx) {
    // 配置
    char one, five, ten;
    if (scale == 100) {
        one = 'C';
        five = 'D';
        ten = 'M';
    }
    else if (scale == 10) {
        one = 'X';
        five = 'L';
        ten = 'C';
    }
    else {      // 个位数
        one = 'I';
        five = 'V';
        ten = 'X';
    }

    // split
    string scaleSplit = "";
    int start = splitIdx;
    for (; splitIdx < romanNum.size(); splitIdx++) {
        char c = romanNum[splitIdx];
        if (c == one || c == five || c == ten) {
            if (romanNum[start] != ten) {    // ten 不能开头，可以结尾，要符合这种情况
                scaleSplit += c;
            }
        }
        else {
            break;
        }
    }
    return scaleSplit;
}
```

在 convertToInt 转换百位数：

```
// 把罗马数字转为整型数字
// 输入：罗马数字，进制
// 输出：整型数字
int convertToInt(string s, int scale) {
    int len = s.length();
    if (len == 0) {
        return 0;
    }

    // 配置
    char one, five, ten;
    int rOne, rFive, rTen;
    if (scale == 100) {
        one = 'C';
        five = 'D';
        ten = 'M';
        rOne = structRomanNum.C;
        rFive = structRomanNum.D;
        rTen = structRomanNum.M;
    }
    else if (scale == 10) {
        one = 'X';
        five = 'L';
        ten = 'C';
        rOne = structRomanNum.X;
        rFive = structRomanNum.L;
        rTen = structRomanNum.C;
    }
    else {      // 个位数
        one = 'I';
        five = 'V';
        ten = 'X';
        rOne = structRomanNum.I;
        rFive = structRomanNum.V;
        rTen = structRomanNum.X;
    }

    // convert
    char headCh = s[0];
    char tailCh = s[len - 1];

    if (headCh == one) {
        if (tailCh == one) {
            return len * rOne;
        }
        else if (tailCh == five) {
            return rFive - rOne;
        }
        else {      // 结束字符是 ten
            return rTen - rOne;
        }
    }
    else {      // 起始字符是 five
        return rFive + (len - 1) * rOne;
    }
}
```

##计算千位数

我们使用 E ＝ 5000，F ＝ 10000。修改方式跟计算百位数时一样。

##完整程序

```
#include <iostream>
#include <string>
using namespace std;

struct Roman_Num {
    int I = 1;
    int V = 5;
    int X = 10;
    int L = 50;
    int C = 100;
    int D = 500;
    int M = 1000;
    int E = 5000;
    int F = 10000;
};

class Solution {
public:
    Roman_Num structRomanNum;

    int romanToInt(string s) {
        int splitIdx = 0;

        string s1000 = splitRomanNum(s, 1000, splitIdx);    // 在执行完 splitS1000 后，记录当前遍历到哪个 index，方便 splitS100 使用
        string s100 = splitRomanNum(s, 100, splitIdx);    // 在执行完 splitS100 后，记录当前遍历到哪个 index，方便 splitS10 使用
        string s10 = splitRomanNum(s, 10, splitIdx);      // 在执行完 splitS10 后，记录当前遍历到哪个 index，方便 splitS1 使用
        string s1 = splitRomanNum(s, 1, splitIdx);

        int i1000 = convertToInt(s1000, 1000);     // 第二个参数表示千位数
        int i100 = convertToInt(s100, 100);        // 第二个参数表示百位数
        int i10 = convertToInt(s10, 10);           // 第二个参数表示十位数
        int i1 = convertToInt(s1, 1);              // 第二个参数表示个位数

        return i1000 + i100 + i10 + i1;
    }

    // 把 romanNum 的某位数分离出来
    // 输入：原罗马数字，千/百/十/个位数，index 引用（可理解为用于返回两个数据）
    // 输出：组成对应位数的子字符串
    string splitRomanNum(string romanNum, int scale, int& splitIdx) {
        // 配置
        char one, five, ten;
        if (scale == 1000) {
            one = 'M';
            five = 'E';
            ten = 'F';
        }
        else if (scale == 100) {
            one = 'C';
            five = 'D';
            ten = 'M';
        }
        else if (scale == 10) {
            one = 'X';
            five = 'L';
            ten = 'C';
        }
        else {      // 个位数
            one = 'I';
            five = 'V';
            ten = 'X';
        }

        // split
        string scaleSplit = "";
        int start = splitIdx;
        for (; splitIdx < romanNum.size(); splitIdx++) {
            char c = romanNum[splitIdx];
            if (c == one || c == five || c == ten) {
                if (romanNum[start] != ten) {    // ten 不能开头，可以结尾，要符合这种情况
                    scaleSplit += c;
                }
            }
            else {
                break;
            }
        }
        return scaleSplit;
    }

    // 把罗马数字转为整型数字
    // 输入：罗马数字，进制
    // 输出：整型数字
    int convertToInt(string s, int scale) {
        int len = s.length();
        if (len == 0) {
            return 0;
        }

        // 配置
        char one, five, ten;
        int rOne, rFive, rTen;
        if (scale == 1000) {
            one = 'M';
            five = 'E';
            ten = 'F';
            rOne = structRomanNum.M;
            rFive = structRomanNum.E;
            rTen = structRomanNum.F;
        }
        else if (scale == 100) {
            one = 'C';
            five = 'D';
            ten = 'M';
            rOne = structRomanNum.C;
            rFive = structRomanNum.D;
            rTen = structRomanNum.M;
        }
        else if (scale == 10) {
            one = 'X';
            five = 'L';
            ten = 'C';
            rOne = structRomanNum.X;
            rFive = structRomanNum.L;
            rTen = structRomanNum.C;
        }
        else {      // 个位数
            one = 'I';
            five = 'V';
            ten = 'X';
            rOne = structRomanNum.I;
            rFive = structRomanNum.V;
            rTen = structRomanNum.X;
        }

        // convert
        char headCh = s[0];
        char tailCh = s[len - 1];

        if (headCh == one) {
            if (tailCh == one) {
                return len * rOne;
            }
            else if (tailCh == five) {
                return rFive - rOne;
            }
            else {      // 结束字符是 ten
                return rTen - rOne;
            }
        }
        else {      // 起始字符是 five
            return rFive + (len - 1) * rOne;
        }
    }
};

int main() {
    Solution sol;

    // 1-10
    cout << sol.romanToInt("I") << endl;
    cout << sol.romanToInt("II") << endl;
    cout << sol.romanToInt("III") << endl;
    cout << sol.romanToInt("IV") << endl;
    cout << sol.romanToInt("V") << endl;
    cout << sol.romanToInt("VI") << endl;
    cout << sol.romanToInt("VII") << endl;
    cout << sol.romanToInt("VIII") << endl;
    cout << sol.romanToInt("IX") << endl;
    cout << sol.romanToInt("X") << endl;
    cout << endl;

    // 10-20
    cout << sol.romanToInt("XI") << endl;
    cout << sol.romanToInt("XII") << endl;
    cout << sol.romanToInt("XIII") << endl;
    cout << sol.romanToInt("XIV") << endl;
    cout << sol.romanToInt("XV") << endl;
    cout << sol.romanToInt("XVI") << endl;
    cout << sol.romanToInt("XVII") << endl;
    cout << sol.romanToInt("XVIII") << endl;
    cout << sol.romanToInt("XIX") << endl;
    cout << sol.romanToInt("XX") << endl;
    cout << endl;

    // 20-30
    cout << sol.romanToInt("XXI") << endl;
    cout << sol.romanToInt("XXII") << endl;
    cout << sol.romanToInt("XXIII") << endl;
    cout << sol.romanToInt("XXIV") << endl;
    cout << sol.romanToInt("XXV") << endl;
    cout << sol.romanToInt("XXVI") << endl;
    cout << sol.romanToInt("XXVII") << endl;
    cout << sol.romanToInt("XXVIII") << endl;
    cout << sol.romanToInt("XXIX") << endl;
    cout << sol.romanToInt("XXX") << endl;
    cout << endl;

    // 30-40
    cout << sol.romanToInt("XXXI") << endl;
    cout << sol.romanToInt("XXXII") << endl;
    cout << sol.romanToInt("XXXIII") << endl;
    cout << sol.romanToInt("XXXIV") << endl;
    cout << sol.romanToInt("XXXV") << endl;
    cout << sol.romanToInt("XXXVI") << endl;
    cout << sol.romanToInt("XXXVII") << endl;
    cout << sol.romanToInt("XXXVIII") << endl;
    cout << sol.romanToInt("XXXIX") << endl;
    cout << sol.romanToInt("XL") << endl;
    cout << endl;

    // 40-50
    cout << sol.romanToInt("XLI") << endl;
    cout << sol.romanToInt("XLII") << endl;
    cout << sol.romanToInt("XLIII") << endl;
    cout << sol.romanToInt("XLIV") << endl;
    cout << sol.romanToInt("XLV") << endl;
    cout << sol.romanToInt("XLVI") << endl;
    cout << sol.romanToInt("XLVII") << endl;
    cout << sol.romanToInt("XLVIII") << endl;
    cout << sol.romanToInt("XLIX") << endl;
    cout << sol.romanToInt("L") << endl;
    cout << endl;

    // 50-60
    cout << sol.romanToInt("LI") << endl;
    cout << sol.romanToInt("LII") << endl;
    cout << sol.romanToInt("LIII") << endl;
    cout << sol.romanToInt("LIV") << endl;
    cout << sol.romanToInt("LV") << endl;
    cout << sol.romanToInt("LVI") << endl;
    cout << sol.romanToInt("LVII") << endl;
    cout << sol.romanToInt("LVIII") << endl;
    cout << sol.romanToInt("LIX") << endl;
    cout << sol.romanToInt("L") << endl;
    cout << endl;

    // 60-70
    cout << sol.romanToInt("LXI") << endl;
    cout << sol.romanToInt("LXII") << endl;
    cout << sol.romanToInt("LXIII") << endl;
    cout << sol.romanToInt("LXIV") << endl;
    cout << sol.romanToInt("LXV") << endl;
    cout << sol.romanToInt("LXVI") << endl;
    cout << sol.romanToInt("LXVII") << endl;
    cout << sol.romanToInt("LXVIII") << endl;
    cout << sol.romanToInt("LXIX") << endl;
    cout << sol.romanToInt("LX") << endl;
    cout << endl;

    // 70-80
    cout << sol.romanToInt("LXXI") << endl;
    cout << sol.romanToInt("LXXII") << endl;
    cout << sol.romanToInt("LXXIII") << endl;
    cout << sol.romanToInt("LXXIV") << endl;
    cout << sol.romanToInt("LXXV") << endl;
    cout << sol.romanToInt("LXXVI") << endl;
    cout << sol.romanToInt("LXXVII") << endl;
    cout << sol.romanToInt("LXXVIII") << endl;
    cout << sol.romanToInt("LXXIX") << endl;
    cout << sol.romanToInt("LXX") << endl;
    cout << endl;

    // 80-90
    cout << sol.romanToInt("LXXXI") << endl;
    cout << sol.romanToInt("LXXXII") << endl;
    cout << sol.romanToInt("LXXXIII") << endl;
    cout << sol.romanToInt("LXXXIV") << endl;
    cout << sol.romanToInt("LXXXV") << endl;
    cout << sol.romanToInt("LXXXVI") << endl;
    cout << sol.romanToInt("LXXXVII") << endl;
    cout << sol.romanToInt("LXXXVIII") << endl;
    cout << sol.romanToInt("LXXXIX") << endl;
    cout << sol.romanToInt("LXXX") << endl;
    cout << endl;

    // 90-99
    cout << sol.romanToInt("XCI") << endl;
    cout << sol.romanToInt("XCII") << endl;
    cout << sol.romanToInt("XCIII") << endl;
    cout << sol.romanToInt("XCIV") << endl;
    cout << sol.romanToInt("XCV") << endl;
    cout << sol.romanToInt("XCVI") << endl;
    cout << sol.romanToInt("XCVII") << endl;
    cout << sol.romanToInt("XCVIII") << endl;
    cout << sol.romanToInt("XCIX") << endl;
    cout << endl;
    
    cout << sol.romanToInt("C") << endl;        // 100
    cout << sol.romanToInt("CC") << endl;       // 200
    cout << sol.romanToInt("CCC") << endl;      // 300
    cout << sol.romanToInt("CD") << endl;       // 400
    cout << sol.romanToInt("D") << endl;        // 500
    cout << sol.romanToInt("DC") << endl;       // 600
    cout << sol.romanToInt("DCC") << endl;      // 700
    cout << sol.romanToInt("DCCC") << endl;     // 800
    cout << sol.romanToInt("CM") << endl;       // 900
    cout << sol.romanToInt("CMXCIX") << endl;   // 999
    cout << endl;
    
    cout << sol.romanToInt("M") << endl;            // 1000
    cout << sol.romanToInt("MM") << endl;           // 2000
    cout << sol.romanToInt("MMM") << endl;          // 3000
    cout << sol.romanToInt("MMMCMXCIX") << endl;    // 3999
    cout << endl;
}


// 输出结果：
// 1
// 2
// 3
// 4
// 5
// 6
// 7
// 8
// 9
// 10
// 
// 11
// 12
// 13
// 14
// 15
// 16
// 17
// 18
// 19
// 20
// 
// 21
// 22
// 23
// 24
// 25
// 26
// 27
// 28
// 29
// 30
// 
// 31
// 32
// 33
// 34
// 35
// 36
// 37
// 38
// 39
// 40
// 
// 41
// 42
// 43
// 44
// 45
// 46
// 47
// 48
// 49
// 50
// 
// 51
// 52
// 53
// 54
// 55
// 56
// 57
// 58
// 59
// 50
// 
// 61
// 62
// 63
// 64
// 65
// 66
// 67
// 68
// 69
// 60
// 
// 71
// 72
// 73
// 74
// 75
// 76
// 77
// 78
// 79
// 70
// 
// 81
// 82
// 83
// 84
// 85
// 86
// 87
// 88
// 89
// 80
// 
// 91
// 92
// 93
// 94
// 95
// 96
// 97
// 98
// 99
// 
// 100
// 200
// 300
// 400
// 500
// 600
// 700
// 800
// 900
// 999
// 
// 1000
// 2000
// 3000
// 3999
```

提交到 Leetcode，Accepted! :) 运行时间为 44ms。
