title: Lua 学习 - 变量
date: 2017-01-15 22:30
categories: Lua
---

本文整理总结 Lua 中变量（Statements）的相关知识。

<!-- more -->

# 变量定义

在 Lua 中，这样定义一个变量：

```lua
a = 1
```

变量名可以由任意字母、数字和下划线组成，但不能以数字开头。

注意：变量名避免使用下划线开头并跟着大写字母的标识符，如“_VERSION”，因为 Lua 将这类标识符保留作特殊用途。

以下是 Lua 的关键字，变量名不能跟关键字一样：

    and     break   do      else    elseif
    end     false   goto    for     function
    if      in      local   nil     not
    or      repeat  return  then    true
    until   while

---

# 变量初始值

变量的初始值为 nil，将变量赋值为 nil 表示删除它。

---

# 全局变量

变量默认都是全局变量，如果想删除，只要置为 nil 即可。

Lua 将全局变量存储在一个 table 里。

---

# 局部变量

创建局部变量的方式是：local a = 1，其作用域是创建时所在的程序块。程序块是一个控制结构的执行体，或者是一个函数的执行体。

注意：在 Lua 交互模式中，每行输入都是一个程序块，可以用 do-end 来限定一个程序块。
