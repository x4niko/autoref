---
title: swift学习笔记3：字符和字符串 (Strings and Characters)
tags:
  - iOS
  - swift
  - 字符
  - 字符串
id: 53
categories:
  - iOS
  - swift学习笔记
date: 2015-11-20 20:25:20
---

两种方式初始化一个空字符串：
<pre>
var emptyString = ""
var anotherEmptyString = String() 

emptyString.isEmpty  // 判断字符串是否为空
</pre>

可变字符串：
<pre>
var variableString = "Horse"
variableString += " and carriage"
print(variableString)  // 打印：Horse and carriage
</pre>

swift的字符串类型是值类型，当创建一个新的字符串类型，传递给一个函数或者方法，给变量或常量赋值时，只是拷贝了一份字符串的值，而不是传递原始值。

字符类型：
<pre>
let exclamationMark: Character = "!"
</pre>
字符串也可以由字符数组构造而成：
<pre>
let catCharacters: [Character] = ["H", "e", "l", "l", "o"]
let catString = String(catCharacters)
print(catString) //打印：Hello
</pre>
可以给字符串附加字符，但不能给字符附加字符或者字符串：
<pre>
var aha = "aha"

let exclamationMark: Character = "!"
aha.append(exclamationMark)
print(aha)  // 打印：aha!
</pre>

使用\()在字符串中插入值，括号内部的表达式不能包含未转义的双引号、反斜杠，也不能包含回车、换行符。：
<pre>
let multiplier = 3
let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"
</pre>

字符串特殊字符：\0 (null), \\ (反斜线), \t (水平制表符), \n (换行), \r (回车), \" (双引号) and \' (单引号)
<pre>
let dollarSign = "\u{24}"        // $,  Unicode：U+0024
</pre>
扩展字元簇（Extended Grapheme Clusters）
Grapheme是指某种自然语言中的最小单元，它并不一定有实际意义，但是它们组合成这种语言的单个字符。Swift语言的每个字符(Character)都是一个独立的扩展字元簇，一个扩展字元簇就是由一个或多个Unicode scalar(Unicode纯量)的有序排列从而组合成可读的字符。
比如：字母é可以是一个单独的Unicode scalar：U+00E9，也可以是多个纯量的组合：U+0065U+0301 （其中U+0065就是字母e）。在Swift中，这两种情况都认为是一个字符，因此获取字符串长度的时候(用全局函数count())，返回的值是相同的，这意味着字符串的改变并不一定意味着其长度发生变化。
注意区分这和OC中NSString的length的区别，NSString的长度是基于字符串在UTF-16编码表示时其中包含的16位的单元的个数，而不是Unicode扩展字元簇的数目。为了反映这个区别，NSString的length属性在Swift中用utf16Count函数来代替。
<pre>
var word = "cafe"
print("the number of characters in \(word) is \(word.characters.count)")
// 打印：the number of characters in cafe is 4

word += "\u{301}"
print("the number of characters in \(word) is \(word.characters.count)")
// 打印：the number of characters in café is 4
</pre>

字符串索引：
每一个字符串都有一个关联的索引(index)类型，String.index，它对应着字符串中的每一个字符的位置。
不同的字符可能会占用不同的内存空间数量，所以要知道字符的确定位置，就必须从字符串开头遍历每一个 Unicode 标量到字符串结尾。因此，Swift 的字符串不能用整数(integer)做索引。
使用startIndex属性可以获取字符串的第一个字符。使用endIndex属性可以获取最后的位置（其实endIndex在值上与字符串的长度相等）。如果字符串是空值，startIndex和endIndex是相等的。
通过调用String.Index的predecessor()方法，可以立即得到前面一个索引，调用successor()方法可以立即得到后面一个索引。
<pre>
let greeting = "Guten Tag!"
greeting[greeting.startIndex]                       // G
greeting[greeting.endIndex.predecessor()]           // !
greeting[greeting.startIndex.successor()]           // u
let index = greeting.startIndex.advancedBy(7)
greeting[index]                                     // a
</pre>

插入和移除字符串：
<pre>
var welcome = "hello"
welcome.insert("!", atIndex: welcome.endIndex)
// hello!

welcome.insertContentsOf(" there".characters, at: welcome.endIndex.predecessor())
// hello there!

welcome.removeAtIndex(welcome.endIndex.predecessor())
// hello there

let range = welcome.endIndex.advancedBy(-6)..<welcome.endIndex
welcome.removeRange(range)
// hello
</pre>

字符串对比：
<pre>
let quotation = "We're a lot alike, you and I."
let sameQuotation = "We're a lot alike, you and I."
quotation == sameQuotation  // true
</pre>

使用hasPrefix(_:)和hasSuffix(_:)判断一个字符串是否有某个前缀或后缀