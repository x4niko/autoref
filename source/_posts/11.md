---
title: swift学习笔记1：常量、变量和数据类型 (Constants and Variables)
tags:
  - iOS
  - swift
  - 变量
  - 常量
  - 数据类型
id: 4
categories:
  - iOS
  - swift学习笔记
date: 2015-11-18 14:38:39
---

按照常理，应该先打印一句“Hello World”。
<pre>print("Hello, swift!")
</pre>
对swift来说，这就是一个完整的程序，不需要为了输入输出或者字符串处理导入一个单独的库，也不需要main函数，全局作用域中的代码会被自动当做程序的入口。一条语句后面也不需要分号，加上也不影响，不过当一行写多条语句的时候就要用分号分开。
<pre>print("Hello, swift!"); print("Hello, world!")
</pre>
print在打印一行后会默认换行，要不换行的话，设置另一个参数terminator为""。
<pre>print("Hello, swift!", terminator:"") //不换行
</pre>
常量和变量不需要明确地指定类型，但在使用前需要先声明，常量声明用let，变量声明用var。可以一行声明多个常量或变量。
<pre>var myVariable = 0
myVariable = 1
let myConstant = 200

var a = 0.0, b = 1, c = 2.0
</pre>
关于常量或变量的命名，可以包含Unicode字符，不能包含空格、数学符号、箭头、制表符，也不能以数字开头。严格上不能用关键字作为名字，如果不得不这么做，可以给关键字名字加上反引号，但不建议。
<pre>let π = 3.14159 //正确
let 你好 = "你好世界" //正确
let 4digit //错误

var `let` = 0 // let为关键字，加上反引号作为变量名
`let` = 1
print(`let`) // 打印：1
</pre>
一般声明变量时需要给它赋值，以此来推断出它的类型。如果声明时不想赋值，可以先给变量提供一种类型，等需要的时候再赋值。
<pre>var welcomeMessage: String
welcomeMessage = "Hello" //赋值
</pre>
也可以一行定义多个相同类型的常量或变量，用逗号分开
<pre>var red, green, blue: Double
</pre>
swift包含类型有：整数Int，浮点数Double和Float，布尔值Bool和文本String，以及集合类型Array、Set和Dictionary。
整型分为8位、16位、 32位和64位，像UInt8为8位无符号整型，Int32为32位有符号整型。多数情况下不需要指定特定的整型，可以直接使用Int，在32位平台，Int的大小跟Int32相等，在64位平台，Int的大小跟Int64的相等。
浮点数有Double和Float两种，Double相当于一个64位浮点数，Float相当于一个32位浮点数。
swift是类型安全的语言，会根据赋的值推断出类型。
<pre>let pi = 3.14159 //会推断为Double，而不是Float类型
let anotherPi = 3 + 0.14159 //整数和浮点数相加会推断为Double
</pre>
整型的数值表示：
<pre>let decimalInteger = 18
let binaryInteger = 0b10010       // 二进制
let octalInteger = 0o22           // 八进制
let hexadecimalInteger = 0x12     // 十六进制
</pre>
浮点型的十进制数值不一定要有指数，底数用大写或者小写的e表示：
1.25e^2表示1.25 x 10^2或125.0
1.25e^-2表示1.25 x 10^-2或0.0125。
十六进制数值必须要有指数，用大写或小写的p做底数：
0xFp2表示15 x 2^2或60.0
0xFp-2表示15 x 2^-2或3.75
<pre>let decimalDouble = 12.1875
let exponentDouble = 1.21875e1     // 指数表示
let hexadecimalDouble = 0xC.3p0    // 十六进制
</pre>
上面的值都是12.1875。
想要方便地看出数值表示多少，可以在适当的位置给整数或者浮点数加0和下划线
<pre>
let paddedDouble = 000123.456
let oneMillion = 1_000_000
let justOverOneMillion = 1_000_000.000_000_1
</pre>

整型转换：Int8的存储范围为-128~127，UInt8的存储范围为0~255，如果存储的值超出范围就会报错
<pre>
let cannotBeNegative: UInt8 = -1 //错误
let tooBig: Int8 = Int8.max + 1  //错误
</pre>
下面的例子中，UInt8和UInt16是不同的类型，需要调用UInt16(one)转换为可以相加的类型
<pre>
let twoThousand: UInt16 = 2_000
let one: UInt8 = 1
let twoThousandAndOneError = twoThousand + one       //错误
let twoThousandAndOne = twoThousand + UInt16(one)    //正确
</pre>
整型和浮点型的转换要明确地表示
<pre>
// 整型转浮点型
let three = 3
let pointOneFourOneFiveNine = 0.14159
let pi = Double(three) + pointOneFourOneFiveNine

// 浮点型转整型，小数点后面的数会被截掉
let integerPi = Int(pi)  // 打印：3

// 这句是正确的，因为3和0.14159还没有指定明确的类型
let anotherPi = 3 + 0.14159
</pre>
当现有类型的名字不太适合当前上下文定义时，可以给类型取一个别名
<pre>
typealias AnotherInt16 = Int16
var minAnotherInt16 = AnotherInt16.min
</pre>
稍微记录一下布尔值Bool
<pre>
let orangesAreOrange = true
let turnipsAreDelicious = false
</pre>

swift的元组组合多个值作为一个复合值，元组中的各个值可以是任何类型，每个值的类型也不用一样。元组中的值可以单独获取使用。
<pre>
let http404Error = (404, "Not Found")  // http404Error的类型是(Int, String)

let (statusCode, statusMessage) = http404Error
print(statusCode)         // 打印：404
print("statusMessage")    // 打印：Not Found

// 通过索引来获取，索引值从0开始
print(http404Error.0)
print(http404Error.1)

// 如果只需要部分值，其它不需要的用下划线_代替
let (justTheStatusCode, _) = http404Error   

// 也可以给元组值命名，方便读取
let http200Status = (statusCode: 200, description: "OK")
print(http200Status.statusCode)
print(http200Status.description)
</pre>

有时候复制可能会失败，比如：
<pre>
let possibleNumber = "123"
let convertedNumber = Int(possibleNumber)
// swift推断possibleNumber的类型为Int?，或者是可选的Int
</pre>
上面尝试将字符串possibleNumber转换成Int型，但是possibleNumber的值可能是"Hello, swift!"，所以它返回一个可选的类型Int?，表示它要么是Int型，要么什么都不是，也不可能是Bool或者String。

swift中得nil不是一个指针，它表示缺少一个类型的值，任何类型的可选值都可以设置为nil，但不能用于不可选的常量或变量。
<pre>
var serverResponseCode: Int? = 404
serverResponseCode = nil   // 现在serverResponseCode不包含值

var surveyAnswer: String?  // surveyAnswer没有默认值，自动设置为nil
</pre>
! 放在类型后面，表示强制的隐式转换。和?放在类型后面的行为比较类似，都是一个类型声明的语法。?声明的是Optional，而!声明的是一个Implicitly Unwrapped Optional类型，使得我们在确信返回值或者要传递的值不是空的时候，可以很方便的不需要做任何转换，直接使用。
<pre>
let possibleString: String? = "An optional string."
let forcedString: String = possibleString! // 需要感叹号

let assumedString: String! = "An implicitly unwrapped optional string."
let implicitString: String = assumedString // 不需要感叹号
</pre>