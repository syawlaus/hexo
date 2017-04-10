title: 学习 JSON
date: 2015-12-06 17:00
categories: JSON
---

#JSON 简介

JSON 全称 JavaScript Object Notation（JavaScript 对象表示法），是一种常用的轻量级的数据交换格式。JSON 基于文本的格式，独立于任何编程语言，人们易读易写，机器也容易封装和解析，是理想的数据交换格式。

<!-- more -->

---

#JSON 结构

JSON 数据有两种结构：

1. *值的有序列表*，即其它编程语言里的*数组*、*链表*、*序列*等
2. *名称/值对的集合*，即其它编程语言里的*对象*、*字典*、*哈希表*、*关联数组*等

上面两种结构是通用的数据结构，几乎所有编程语言都支持。所以 JSON 十分适合用于交换数据。

---

#JSON 形式

##数组

*数组*是值的有序列表，用方括号（`[]`）表示。值之间用逗号（`,`）分隔。如一个班里有4个学生，张三、李四、王五、陈六。用 JSON 表示就是 `["张三", "李四", "王五", "陈六"]`。见下图：

![](/images/json/learning-JSON/JSON-array.png)

##对象

*对象*是名称/值对的无序集合，用大括号（`{}`）表示。名称/值对之间用逗号（`,`）分隔，名称和值之间用冒号（`:`）表示映射关系。如一个班里学生考试成绩分别是张三60分、李四70分、王五80分、陈六90分。用 JSON 对象表示就是 `{"张三":60, "李四":70, "王五":80, "陈六":90}`。见下图：

![](/images/json/learning-JSON/JSON-object.png)

对象的名称可以为：

* 字符串

对象的值和数组的值均可以为：

![](/images/json/learning-JSON/JSON-value.png)

其中*字符串*即是包含0个或多个 Unicode 字符的序列，包含在双引号（`""`）里，使用反斜杠（\）作为转义字符，见下图：

![](/images/json/learning-JSON/JSON-string.png)

其中*数值*跟 C 或 Java 语言十分相似，除了不使用八进制和十六进制，见下图：

![](/images/json/learning-JSON/JSON-number.png)

---

#参考资料

* [Introducing JSON](http://www.json.org/)
