---
title: swift学习笔记6：函数 (Functions)
tags:
  - iOS
  - swift
  - 函数
id: 72
categories:
  - iOS
  - swift学习笔记
date: 2015-11-25 19:55:12
---

函数sayHello(_:)，一个String类型的参数personName，返回一个String类型的值：
<pre>
func sayHello(personName: String) -> String {
    let greeting = "Hello, " + personName + "!"
    return greeting
}

print(sayHello("Anna"))
// Hello, Anna!
</pre>

函数参数值和返回类型：
<pre>
// 没有参数；返回一个String类型值
func sayHelloWorld() -> String {
    return "hello, world"
}
// hello, world

// 多个参数，用逗号分隔，调用时，第二个参数开始需要写标签；返回一个String类型值
func sayHello(personName: String, alreadyGreeted: Bool) -> String {
    if alreadyGreeted {
        return sayHelloAgain(personName)
    } else {
        return sayHello(personName)
    }
}
print(sayHello("Tim", alreadyGreeted: true))
// Hello again, Tim!

// 没有返回值
func sayGoodbye(personName: String) {
    print("Goodbye, \(personName)!")
}
sayGoodbye("Dave")
// Goodbye, Dave!
// 严格的说，即使没有定义返回值，函数仍然会返回一个特殊值Void，这是一个简单的空元组，可以写成()

// 多个返回值
func minMax(array: [Int]) -> (min: Int, max: Int) {
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
let bounds = minMax([8, -6, 2, 109, 3, 71])
print("min is \(bounds.min) and max is \(bounds.max)")
// min is -6 and max is 109
</pre>

如果函数返回一个没有值的元组类型，可以返回一个可选的元组，说明整个元组可能为nil，如返回(Int, Int)? 或 (String, Int, Bool)?。
一个可选的元组类型(Int, Int)?跟一个元组包含可选类型(Int?, Int?)不同，可选的元组类型(Int, Int)?是整个元组可选，不是在元组内的每个值可选。
<pre>
func minMax(array: [Int]) -> (min: Int, max: Int)? {
    if array.isEmpty { return nil }
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
if let bounds = minMax([8, -6, 2, 109, 3, 71]) {
    print("min is \(bounds.min) and max is \(bounds.max)")
}
// min is -6 and max is 109
</pre>

函数参数名：函数参数包括外用参数名和本地参数名，外用参数名用来标记参数传递给函数调用，本地参数名在函数内使用。
<pre>
func someFunction(firstParameterName: Int, secondParameterName: Int) {
}
someFunction(1, secondParameterName: 2)
// 第一个参数默认删除外用参数名，第二个参数开始用本地参数名作为外部参数名

// 指定外用参数名：如果提供了一个外用参数名，在调用函数的时候一定要使用
func sayHello(to person: String, and anotherPerson: String) -> String {
    return "Hello \(person) and \(anotherPerson)!"
}
print(sayHello(to: "Bill", and: "Ted"))

// 第二个参数开始如果不想写外部参数名，可以用下划线代替
func someFunction(firstParameterName: Int, _ secondParameterName: Int) {
}
someFunction(1, 2)

// 默认参数值
func someFunction(parameterWithDefault: Int = 12) {
    print(parameterWithDefault)
}
someFunction(6) // 6
someFunction()  // 12

// 可变参数
func arithmeticMean(numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)       // 3.0
arithmeticMean(3, 8.25, 18.75)      // 10.0

// 常量和变量参数：函数的参数默认是常量，也可以在参数前面加var，来定义一个变量参数
func alignRight(var string: String, totalLength: Int, pad: Character) -> String {
    let amountToPad = totalLength - string.characters.count
    if amountToPad < 1 {
        return string
    }
    let padString = String(pad)
    for _ in 1...amountToPad {
        string = padString + string
    }
    return string
}
let originalString = "hello"
let paddedString = alignRight(originalString, totalLength: 10, pad: "-")
// paddedString = "-----hello"
// originalString = "hello"

// In-Out参数：变量参数只能在函数里面改变，如果需要参数在函数里改变后，在外面保持修改后的值，可以把参数定义为In-Out。In-out参数不能有默认值，可变参数也不能标记为inout，如果标记一个参数为In-Out，就不能同时标记为var或者let。
func swapTwoInts(inout a: Int, inout _ b: Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
// 传递参数时需要在前面加&
var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
// someInt is now 107, and anotherInt is now 3
</pre>

函数类型：
<pre>
func addTwoInts(a: Int, _ b: Int) -> Int {
    return a + b
}
// 定义一个函数类型的常量或变量，然后给变量赋个合适的函数
var mathFunction: (Int, Int) -> Int = addTwoInts
print("Result: \(mathFunction(2, 3))")
// Result: 5

// 也可以省略类型
let anotherMathFunction = addTwoInts
// anotherMathFunction推断类型为(Int, Int) -> Int

// 函数类型作为参数类型
func printMathResult(mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
    print("Result: \(mathFunction(a, b))")
}
printMathResult(addTwoInts, 3, 5)
// Result: 8

// 函数类型作为返回类型
func stepForward(input: Int) -> Int {
    return input + 1
}
func stepBackward(input: Int) -> Int {
    return input - 1
}
func chooseStepFunction(backwards: Bool) -> (Int) -> Int {
    return backwards ? stepBackward : stepForward
}
var currentValue = 3
let moveNearerToZero = chooseStepFunction(currentValue > 0)
// moveNearerToZero推断为stepBackward()函数
print("Counting to zero:")
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// 3...
// 2...
// 1...
// zero!

// 内嵌函数：可以帮上面两个函数stepForward和stepBackward嵌到chooseStepFunction里面去
func chooseStepFunction(backwards: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return backwards ? stepBackward : stepForward
}
</pre>