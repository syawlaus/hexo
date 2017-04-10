title: C++ map
date: 2016-06-30 16:50
categories: C++
---

本文意在整理 C++ map 的用法，和需要注意的地方。

<!--- more -->

# include

在 C++ 程序中，使用 map 要 `#include <map>`。

---

# 定义 map

map 是一对一映射的数据结构。

    map<KeyType, ValueType> mapp;

定义 map，key 类型为 KeyType，value 类型为 ValueType。

![](/images/c++/map/map-structure.png)

<center>（[图片来源](http://mropengate.blogspot.jp/2015/12/cc-map-stl.html)）</center>

---

# 添加元素

```
// 第一种添加方式
mapp.insert(pair<KeyType, ValueType>(key, value));

// 第二种添加方式
mapp[key] = value;
```

---

# 删除元素

```
// 第一种删除方式
mapp.erase(key);

// 第二种删除方式
map<KeyType, ValueType>::iterator iter = mapp.find(key)
if (iter != mapp.end()) {   // 找到 key
    mapp.erase(iter)
}
```

---

# 查找元素

```
map<KeyType, ValueType>::iterator iter = mapp.find(key);

// 找到 key
if (iter != mapp.end()) {

}
// 找不到 key
else {

}
```

![](/images/c++/map/map-iter.png)

<center>（[图片来源](http://mropengate.blogspot.jp/2015/12/cc-map-stl.html)）</center>

---

# 遍历元素

```
for (map<KeyType, ValueType>::iterator iter = mapp.begin(); iter != mapp.end(); iter++) {
    KeyType key = iter->first;
    ValueType val = iter->second;
}
```

---

# 示例代码

```cpp
#include <iostream>
#include <map>
using namespace std;

void printMap(map<char, int>& mapp) {
    for (map<char, int>::iterator iter = mapp.begin(); iter != mapp.end(); iter++) {
        char key = iter->first;
        int val = iter->second;
        cout << key << " : " << val << endl;
    }
    cout << "--------------" << endl;
}

void findElement(map<char, int>& mapp, char key) {
    map<char, int>::iterator iter = mapp.find(key);

    // 找到 key
    if (iter != mapp.end()) {
        cout << "找到 " << key << endl;
    }
    // 找不到 key
    else {
        cout << "找不到 " << key << endl;
    }
}

int main() {
    map<char, int> mapp;

    // 添加元素
    mapp.insert(pair<char, int>('a', 1));   // 第一种添加方式
    mapp['b'] = 2;                          // 第二种添加方式
    mapp['c'] = 3;
    mapp['d'] = 4;
    mapp['e'] = 5;
    mapp['f'] = 6;
    mapp['g'] = 7;
    mapp.insert(pair<char, int>('a', 2));   // key = 'a' 已存在，添加失败，不会覆盖
   
    printMap(mapp);

    // 删除元素
    mapp.erase('a');
    mapp.erase('c');
    mapp.erase('f');
    mapp.erase('x');    // 删除不存在的元素
    
    printMap(mapp);     // mapp 剩下 bdeg
    
    // 查找元素
    findElement(mapp, 'b');
    findElement(mapp, 'd');
    findElement(mapp, 'x');

    // 遍历全部元素
    printMap(mapp);
}
```

---

# 参考资料

* [C/C++ - Map (STL) 用法與心得完全攻略](http://mropengate.blogspot.jp/2015/12/cc-map-stl.html)
