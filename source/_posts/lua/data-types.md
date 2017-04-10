title: Lua 学习 - 数据类型
date: 2017-01-12 12:53
categories: Lua
---

#各种数据类型

在 Lua 中，有 8 种数据类型：nil、boolean、number、string、function、table、userdata、thread。

<!-- more -->

函数 type 可以返回变量的数据类型，用 string 表示：

```lua
print(type(nil))

print(type(false))

print(type(3.14))

print(type("Hello World"))

f = function() end
print(type(f))

t = {}
print(type(t))

输出：
nil
boolean
number
string
function
table
```

---

#nil

nil 类型只有一个值 nil。

---

#boolean

boolean 类型有两个值，分别是 true、false。在 Lua 中，**只把 false 和 nil 视为“假”，其它值视为“真”**。任何值都可以表示一个判断条件。

---

#number

number 类型表示实数，Lua 不区分整数和浮点数。

---

#string

Lua 的 string 类型是不可变值，可以用单/双引号定义 string 类型变量：

```lua
print('a')
print("a")
print("abc")

输出：
a
a
abc
```

string 变量中可以包含转义字符：

Lua 使用 " .. " 来连接字符串：

```lua
a = "abc"
b = "def"
print(a .. b)

输出：
abcdef
```

如果要连接数字，Lua 会自动把数字转换为 string。

在 Lua 中，使用 "#" 来获取字符串长度：

```lua
a = "abc"
b = "你好吗"
print(#a)
print(#b)

输出：
3
6
```

---

#table

在 Lua 中，table 是关联数组，可以通过 key 索引得到 value。key 和 value 可以为任意类型的数据（key 不能为 nil）。

table 是 Lua 中唯一的数据结构，可以利用 table 实现很多强大的功能。table 既不是值，也不是变量，而是对象。可以将一个 table 想象成一种动态分配的对象，程序仅持有其引用（或指针）。创建 table 的方式是：

```lua
t = {}
t[1] = "a"  -- 键为 1，值为 "a"
t[2] = "b"
t.k = "v"   -- 键为 "k"，值为 "v"，等价于 t["k"] = "v"
t = nil     -- 删除 t
```

当然，也可以不指定 key，把 table 当作一般数组使用：

```lua
t = {1, 2, 3, 4, 5}
```

table 永远是匿名的，一个持有 table 引用的变量与 table 对象自身之间没有固定的关联性。

不同于 C/C++/Java 等语言，Lua 的 table 默认使用 1 作为索引的起始值。

Lua 使用 "#" 返回 table 的**最后一个正整数索引值**：

```lua
t = {}
t[1] = "a"  -- 键为 1，值为 "a"
t[2] = "b"
t.k = "v"   -- 键为 "k"，值为 "v"，等价于 t["k"] = "v"
print(#t)

输出：
2
```

经过多次测试之后，我不太确定 # 的返回值，待定。

也可以这样构造 table：

```lua
a = (x = 10, y = 20)

等价于

a = {}
a.x = 10
a.y = 20

等价于

a = {}
a["x"] = 10
a["y"] = 20

等价于

a = {["x"] = 10, ["y"] = 20}
```

在 Lua 中，不建议使用 0 作为起始索引，因为大多数内建函数都假设 table 索引从 1 开始，如果遇到从 0 开始索引的数据，它们无法进行处理。

table 的最后一个元素后面，可以加上逗号“,”：

```lua
t = {
    "abc",
    "123",
    "+-*/",
}
```

table 里的逗号也可以用分号代替，增强可读性：

```lua
t = { x = 10, y = 45; "one", "two", "three" }
```

---

#userdata

userdata 类型可以将任意的 C 语言数据存储到 Lua 变量中。在 Lua 中，userdata 类型数据没有太多的预定义操作，只能进行赋值和相等性测试。userdata 用于表示一种由应用程序或 C 语言库所创建的新类型。

---

#thread
