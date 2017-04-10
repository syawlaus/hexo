title: Lua 学习 - 函数
date: 2017-03-12 12:40
categories: Lua
---

本文整理 Lua 中函数相关的知识。
 
<!-- more -->

---

# 函数定义

在 Lua 中，这样定义一个函数：

```lua
function foo(n)
    do something
    return xxx -- 需要返回值时
end
```

或者这样：

```lua
foo = function(n)
    do something
    return xxx -- 需要返回值时
end
```

---

# 函数调用

函数调用的方式很简单：

```lua
foo(1)
print "Hello World"

t = {1, 2, 3}
print(t)
```

见第 2、5 行代码，当只有一个参数，且参数为 string 或 table 构造式时，函数调用的小括号可以省略。

## 冒号操作符

当我们使用 Lua 面向对象编程时，可以使用冒号来调用函数：

```lua
obj:foo(args)

等价于

obj.foo(obj, args)
```

冒号操作符将 obj 自身隐含地当作第一个参数。

## 关于实参个数

函数调用时传入的实参个数，可以跟函数定义时的形参个数不同。跟多重赋值一样，可分为三种情况：

* 形参个数 < 实参个数，丢弃多余的实参。
* 形参个数 = 实参个数，一一对应赋值。
* 形参个数 > 实参个数，没有实参赋值的形参初始化为 nil。

---

# 多个返回值

函数可返回多个值：

```lua
function foo()
    return 1, 2
end
```

如果函数调用不是表达式中的最后一个元素，那么只返回第一个值，如：

```lua
x, y = foo(), 20  -- 即 x = 1, y = 20
```

函数调用作为 table 非最后一个元素同理：

```lua
function printTable(t)
    for _, v in pairs(t) do 
        print(v)
    end
    print("-----")
end

function foo()
    return 1, 2
end

t = {foo()}
printTable(t)

t = {foo(), 3}
printTable(t)

输出：
1
2
-----
1
3
-----
```

对函数调用使用小括号括起来，可强制函数只返回第一个值：

```lua
function foo()
    return 1, 2
end

a, b = foo()
print(a .. ", " .. b)

a, b = (foo())
print(tostring(a) .. ", " .. tostring(b))

输出：
1, 2
1, nil
```

unpack 函数，接收一个数组作为参数，并从索引 1 开始，返回数组所有元素：

```lua
t = {10, 20, 30}
print(unpack(t))

输出：
10 20 30
```

unpack 的一项重要用途在“泛型调用（generic call）”机制中。泛型调用机制可以动态地以任何实参来调用任何函数。举个例子，如果想调用任意函数 f，而所有的参数都在数组 a 中，那么可以这么写：

```lua
f(unpack(a))
```

unpack 将返回 a 中所有的值，这些值作为 f 的实参。

---

# 变长参数

Lua 中的函数可以接收不同数量的实参。如：

```lua
function add(...)
    local sum = 0
    local t = {...}
    for _, v in ipairs(t) do
        sum = sum + v
    end
    return sum
end

print(add(1, 2, 3, 4, 5))

输出：
15
```

参数列表中的（...）表示该函数可接受不同数量的实参。函数要访问它的变长参数时，仍需要用到（...）。表达式“...”的行为类似于一个具有多重返回值的函数，它返回的是当前函数的所有变长参数。如：

```lua
local a, b = ...
```

---

# 第一类值

在 Lua 中，函数是作为第一类值，可以把函数像一般数据一样赋值：

```lua
foo = function (n)
    print(n)
end

foo(3)
foo(4)
foo(5)

输出：
3
4
5
```

这样表示函数可以存储在变量中，可以通过参数传递给其它函数，还可以作为其它函数的返回值。这种特性使 Lua 具有极大的灵活性。为了给一个函数添加新功能，程序可以重新定义该函数。而在运行一些不受信任的代码时，可以先删除某些函数，从而创建一个安全的运行环境。

Lua 既可以调用 Lua 编写的函数，也可以调用 C 编写的函数。Lua 所有的标准库都由 C 编写的，包括字符串操作、table 操作、I/O、数学函数等。

在 Lua 中，函数与其它所有值一样是没有名字的，我们讨论的某个函数 f，只是持有函数的某个变量 f。所以我们可以像操作其它变量一样操作这个持有函数的变量 f。

我们平时习惯使用的函数定义方式 `function f() end`，只不过是 `f = function() end` 的语法糖而已。所以，函数定义实际是一个赋值语句，这条语句创建了一种类型为“函数”的值，并赋值给一个变量，就像 table 构造式 {} 一样。

## 闭包

将函数 outer 内嵌套函数 inner，在 inner 内可使用 outer 局部变量，这项特性称为 *词法域（Lexical Scoping）*。例如：

```lua
function newCounter()
    local i = 0
    return function()      -- 匿名函数
               i = i + 1
               return i
           end
end

f = newCounter()
print(f())
print(f())
print(f())

输出：
1
2
3
```

在匿名函数中，使用其外部的函数 newCounter 里的局部变量 i。在匿名函数中，i 称为 *非局部变量*，或 *upvalue*。

可以看到，每次调用 f，i 都能在上次的值上自加 1。简单来说，一个 *闭包（closure）* 就是一个函数加上该函数所需访问的所有 upvalue。如果再次调用 newCounter，那么它会创建一个新的局部变量 i，从而得到一个新的闭包。如：

```lua
f2 = newCounter()
print(f2())
print(f2())

print("---")

f3 = newCounter()
print(f3())
print(f3())
print(f3())
print(f3())

输出：
1
2
---
1
2
3
4
```

f, f2, f3 分别是三个独立的闭包，它们各自拥有 upvalue i 的实例。再深入一点思考，其实函数本身就是闭包的特例，函数只是“没有 upvalue 的闭包”而已。

## 非全局的函数

将函数存储在一个局部变量中，就得到一个局部函数。Lua 将每个程序块（chunk）作为一个函数处理，那么在程序块中声明的函数就是局部函数，只能在该程序块中可见。

如果局部函数是递归定义，如：

```lua
-- 局部函数定义方式一
local fact = function(n)
    if n == 0 then
        return 1
    else
        return n * fact(n-1)    -- 调用的是全局函数 fact
    end
end
```

方式一的问题是，在代码第 5 行，因为 local fact 还没定义完毕，所以调用的是全局函数 fact。解决的办法是：

```lua
-- 局部函数定义方式二
local fact
fact = function(n)
    if n == 0 then
        return 1
    else
        return n * fact(n-1)    -- 调用的是全局函数 fact
    end
end
```

Lua 对于局部函数语法糖 `local function f() end` 也是按方式二展开，而不是方式一。但是对于间接的递归定义，必须使用方式二的前向声明：

```lua
local f, g  -- 前向声明

function g()
    f()
end

function f()
    g()
end
```

我的理解是，如果定义写为：

```lua
local function g()
    f()
end

local function f()
    g()
end
```

Lua 会展开为：

```lua
local g
g = function()
    f()
end

local f
f = function()
    g()
end
```

那么代码第 2 行调用的是全局函数 f，因为局部函数 f 还没定义。在第 6 行调用的则是局部函数 g。

## 正确的尾调用

下面是代码对 g 的调用是一个 *尾调用（tail call）*：

```lua
function f(x)
    return g(x)
end

function f(x)
    g(x)
end
```

因为调用 g(x) 是 f 里的最后一个动作，之后便再没有任何工作。用 g 的调用栈替换 f 的调用栈，这样就是 *尾调用消除*。满足尾调用消除的函数调用链，不管嵌套层次多深，都不会栈溢出。

---

# 一些常用的函数 API

* 把 string 转换为 number：tonumber
* 把其它类型的值转换为 string：tostring，或 val .. ""
* 对实数 n 下取整：math.floor(n)
* 对实数 n 取绝对值：math.abs(n)
* 取 pi 值：math.pi