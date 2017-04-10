title: Lua 学习 - 语句
date: 2017-01-15 19:15
categories: Lua
---

本文整理总结 Lua 中语句（Statements）的相关知识。

<!-- more -->

---

#注释

在 Lua 中，行注释的方式为 "--"：

```lua
code -- 本行代码被注释掉
```

块注释的方式为 "--[[ ]]"：

```lua
--[[
这几行
代码
都被注释掉
]]
```

---

#赋值

最简单就是给变量赋与一个值：

```lua
a = "hello"
b = 1
```

##多重赋值

Lua 允许多重赋值，即在一个语句里对多个变量进行赋值：

```lua
a, b = "hello", 1
print(a)
print(b)

输出：
hello
1
```

在多重赋值中，先对等号右边求值，再逐个赋值到左边的变量，所以我们可以用这个特性来交换两个变量的值：

```lua
a, b = "hello", 1
a, b = b, a
print(a)
print(b)

输出：
1
hello
```

Lua 总是会将等号右边的值的个数调整到与等号左边的变量个数一致，规则是：

* 变量个数 > 值个数：没有对应的值的变量默认赋值为 nil
* 变量个数 = 值个数：正常的多重赋值
* 变量个数 < 值个数：多余的值被丢弃

```lua
a, b, c = "hello", 1
print(a)
print(b)
print(c)

输出：
hello
1
nil
```

```lua
a, b = "hello", 1, 2
print(a)
print(b)

输出：
hello
1
```

多重赋值的一个常见用法是，对于具有多个返回值的函数，可以一次全部接收，如 a, b = f()

---

#控制结构

控制结构中的条件表达式 cond 可以是任何值，Lua 只会 false 和 nil 视为 false，其它值为 true。

##if

```lua
if cond then
end

if cond then
else
end

if cond then
elseif cond then
else
end
```

##while

```lua
while cond do
end
```

##repeat-until

```lua
repeat
until cond   -- 在这里仍可访问 repeat 里定义的局部变量
```

repeat 循环体里的代码至少会被执行一次。

##数字型 for

```lua
for i = start, end, step do
end
```

start 是循环的起始索引，end 是结束索引，step 是步长（可选，默认为 1），上面的代码等价于下面的 C++ 代码：

```cpp
for (int i = start; i <= end; i += step) {}
```

注意上面的 Lua for 循环中的 i 的作用域是 for。

##泛型 for

泛型 for 通过一个迭代器（iterator）函数来遍历所有值，如

```lua
-- 打印 table a 的所有值
for i, v in ipairs(a) do
    print(v)
end
```

###迭代器函数

ipairs 是一个用于遍历 table 的迭代器函数。在上面的代码中，每次循环里 i 会被赋予一个索引值，v 会被赋予一个元素值。

也可以编写自己的迭代器函数。

##break 与 return

break 语句可跳出本循环。

return 语句可结束本函数，并返回值（如果有的话）
