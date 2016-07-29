---
title: swift学习笔记2：基本操作符 (Basic Operators)
tags:
  - iOS
  - swift
  - 操作符
id: 43
categories:
  - iOS
  - swift学习笔记
date: 2015-11-19 16:33:55
---

操作符分为一元、二元和三元操作符。
一元：-a，!b，i++等
二元：+，-，*，/等
三元：a ? b : c

赋值操作符(=)：
<pre>let b = 10
var a = 5
a = b
// a等于10

let (x, y) = (1, 2)
// x等于1, y等于2
</pre>
算术操作符(+，-，*，/)：
<pre>1 + 2       // 3
5 - 3       // 2
2 * 3       // 6
10.0 / 2.5  // 4.0

"hello, " + "world"  // hello, world
</pre>
取余操作符(%)：
<pre>9 % 4    // 1
-9 % 4   // -1
8 % 2.5  // 0.5
</pre>
自增自减操作符(++，--)：
<pre>var i = 0
++i      // i等于1

var a = 0
let b = ++a  // a和b都等于1

let c = a++  // a等于2，c等于1
</pre>
一元加减运算符：
<pre>let minusSix = -6
let alsoMinusSix = +minusSix  // alsoMinusSix = -6

let three = 3
let minusThree = -three       // minusThree = -3
let plusThree = -minusThree   // plusThree = 3
</pre>
复合赋值运算符：
<pre>var a = 1
a += 2   // a = 3
</pre>
比较运算符：
<pre>1 == 1   // true
2 != 1   // true
2 > 1    // true
1 < 2    // true
1 >= 1   // true
2 <= 1   // false
</pre>
swift还有(=== and !==)运算符，来判断两个对象的引用是否也相同

三元操作符(a ? b : c)：
<pre>let contentHeight = 40
let hasHeader = true
let rowHeight = contentHeight + (hasHeader ? 50 : 20)
// rowHeight等于90
</pre>
Nil Coalescing操作符(a ?? b):
这个运算符有两个条件：
1、a必须是Optional类型的
2、b的类型必须要和a解包后的值类型一致
<pre>let c = a ?? b 
</pre>
对于上面的表达式，c的值是a或b中一个的值，当a解包后的值不为nil时，c就等于a，否则c等于b

范围操作符分为闭合范围和半开范围操作符：
<pre>for index in 1...5 {}

let names = ["Anna", "Alex", "Brian", "Jack"]
let count = names.count
for i in 0..<count {}
</pre>
逻辑操作符：逻辑非 (!a)，逻辑且 (a && b)，逻辑或 (a || b)
<pre>let allowedEntry = false
!allowedEntry  // true

let enteredDoorCode = true
let passedRetinaScan = false
enteredDoorCode && passedRetinaScan  // false

let hasDoorKey = false
let knowsOverridePassword = true
hasDoorKey || knowsOverridePassword  // true

// 混合使用
if enteredDoorCode && passedRetinaScan || hasDoorKey || knowsOverridePassword {}
// 插入圆括号
if (enteredDoorCode && passedRetinaScan) || hasDoorKey || knowsOverridePassword {} 
</pre>