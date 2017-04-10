title: Lua 学习 - 迭代器与泛型 for
date: 2017-03-15 13:00
categories: Lua
---

本文整理 Lua 中迭代器与泛型 for 相关的知识。
 
<!-- more -->

# 创建简单的迭代器

我们知道，在 Lua 中，遍历一个 table 的方式是：

```lua
t = {"ABC", 123, true}

for k, v in pairs(t) do
    print(v)
end

输出：
ABC
123
true
```

那么为什么 `for k, v in pairs(t)` 能正确遍历 table 的每个元素呢？我直观的理解是，pairs(t) 在执行本次循环体前，给 for 返回“上次循环元素的下一个元素”，for 判断是否为 nil，是则结束循环，否则继续循环。 那么 pairs(t) 是如何返回“上次循环元素的下一个元素”呢？这里用闭包来实现就会非常合适，因为闭包会保存内部函数及 upvalue，pairs(t) 可以返回一个内部函数，而 upvalue 则为“上次循环的元素的索引”，代码如下：

```lua
t = {"ABC", 123, true}

function mypairs(t)
    local i = 0
    return function()
               i = i + 1
               return i, t[i]
           end
end

iter = mypairs(t)
print(iter())
print(iter())
print(iter())

输出：
1   ABC
2   123
3   true
```

那么

```lua
for k, v in pairs(t)
    print(k .. "  " .. tostring(v))
end

输出：
1   ABC
2   123
3   true
```

可展开为：

```lua
iter = mypairs(t)
while true do
    local idx, nextVal = iter()
    if nextVal then
        print(idx .. "  " .. tostring(nextVal))
    else
        break
    end
end

输出：
1   ABC
2   123
3   true
```

每次运行 for 会创建并保存一个新的闭包作为迭代器，在本次循环中，for 内部运行一次闭包，并记录闭包返回的 idx 和 nextVal，在本次循环体中使用。

上面的 mypairs 有一个问题，就是每次执行闭包时，都会返回 t[i]，即是 table 作为数组使用时才有效。那么如果 table 作为 hash 来使用的时候，要怎么定义 mypairs？

---

# 泛型 for

泛型 for 的语法如下：

```lua
for <var-list> in <exp-list> do
    <body>
end
```

`<var-list>` 实际上有三个值：迭代器函数（即闭包）、恒定状态、控制变量。for 首先会对 `<exp-list>` 求值，把返回的三个值多重赋值给 `<var-list>`。控制变量即是闭包返回的元素 t[i]，如果 t[i] == nil，则终止循环，否则继续执行，所以在 for 里的 t[i] 都不会为 nil。

在第二次执行循环体前，再次调用闭包，参数是恒定状态和控制变量。以代码来说明会更清晰：

```lua
for var1, ..., varN in <exp-list> do
    <block>
end
```

等价于：

```lua
do
    local closure, s, var = <exp-list>   -- 恒定状态 s，控制变量初始值 var
    while true do
        local var1, ..., varN = closure(s, var)
        var = var1
        if var then
            <block>
        else
            break
        end
    end
end
```