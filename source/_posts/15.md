---
title: swift学习笔记5：控制流 (Control Flow)
tags:
  - iOS
  - swift
  - 控制流
id: 69
categories:
  - iOS
  - swift学习笔记
date: 2015-11-22 18:19:51
---

For-In和For循环语句:
<pre>
for index in 1...5 {
    print("\(index) times 5 is \(index * 5)")
}
// 1 times 5 is 5
// 2 times 5 is 10
// 3 times 5 is 15
// 4 times 5 is 20
// 5 times 5 is 25

// 如果不需要一个系列里地每个值，可以用下划线_代替变量名
let base = 3
let power = 10
var answer = 1
for _ in 1...power {
    answer *= base
}
print("\(base) to the power of \(power) is \(answer)")
// 3 to the power of 10 is 59049

// 遍历数组
let names = ["Anna", "Alex", "Brian", "Jack"]
for name in names {
    print("Hello, \(name)!")
}
// Hello, Anna!
// Hello, Alex!
// Hello, Brian!
// Hello, Jack!

// 遍历字典
let numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
for (animalName, legCount) in numberOfLegs {
    print("\(animalName)s have \(legCount) legs")
}
// ants have 6 legs
// cats have 4 legs
// spiders have 8 legs

// for循环
for var index = 0; index < 3; ++index {
    print("index is \(index)")
}
// index is 0
// index is 1
// index is 2
</pre>

While循环：
<pre>
let finalSquare = 25
var square = 0
while square < finalSquare {
    print(square)
}

repeat {
    print(square)
} while square < finalSquare
</pre>

if条件语句：
<pre>
var temperatureInFahrenheit = 30
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
}
// It's very cold. Consider wearing a scarf.

temperatureInFahrenheit = 40
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else {
    print("It's not that cold. Wear a t-shirt.")
}
// It's not that cold. Wear a t-shirt.

temperatureInFahrenheit = 90
if temperatureInFahrenheit <= 32 {
    print("It's very cold. Consider wearing a scarf.")
} else if temperatureInFahrenheit >= 86 {
    print("It's really warm. Don't forget to wear sunscreen.")
} else {
    print("It's not that cold. Wear a t-shirt.")
}
// It's really warm. Don't forget to wear sunscreen.
</pre>

switch条件语句：
<pre>
// 简单小写字母
let someCharacter: Character = "e"
switch someCharacter {
case "a", "e", "i", "o", "u":
    print("\(someCharacter) is a vowel")
case "b", "c", "d", "f", "g", "h", "j", "k", "l", "m",
"n", "p", "q", "r", "s", "t", "v", "w", "x", "y", "z":
    print("\(someCharacter) is a consonant")
default:
    print("\(someCharacter) is not a vowel or a consonant")
}
// e is a vowel

// swift的switch语句的每个case中必须包含至少一个语句，下面的例子是错误的：
let anotherCharacter: Character = "a"
switch anotherCharacter {
case "a":
case "A":
    print("The letter A")
default:
    print("Not the letter A")
}
// 执行case "a"时会报错，该条件下没有可以执行的语句

// 间隔匹配
let approximateCount = 62
let countedThings = "moons orbiting Saturn"
var naturalCount: String
switch approximateCount {
case 0:
    naturalCount = "no"
case 1..<5:
    naturalCount = "a few"
case 5..<12:
    naturalCount = "several"
case 12..<100:
    naturalCount = "dozens of"
case 100..<1000:
    naturalCount = "hundreds of"
default:
    naturalCount = "many"
}
print("There are \(naturalCount) \(countedThings).")
// There are dozens of moons orbiting Saturn.

// 元组
let somePoint = (1, 1)
switch somePoint {
case (0, 0):
    print("(0, 0) is at the origin")
case (_, 0):
    print("(\(somePoint.0), 0) is on the x-axis")
case (0, _):
    print("(0, \(somePoint.1)) is on the y-axis")
case (-2...2, -2...2):
    print("(\(somePoint.0), \(somePoint.1)) is inside the box")
default:
    print("(\(somePoint.0), \(somePoint.1)) is outside of the box")
}
// (1, 1) is inside the box

// 值绑定，value临时绑定到case中的变量或常量
let anotherPoint = (2, 0)
switch anotherPoint {
case (let x, 0):
    print("on the x-axis with an x value of \(x)")
case (0, let y):
    print("on the y-axis with a y value of \(y)")
case let (x, y):
    print("somewhere else at (\(x), \(y))")
}
// on the x-axis with an x value of 2

// 使用Where来查验另外的条件
let yetAnotherPoint = (1, -1)
switch yetAnotherPoint {
case let (x, y) where x == y:
    print("(\(x), \(y)) is on the line x == y")
case let (x, y) where x == -y:
    print("(\(x), \(y)) is on the line x == -y")
case let (x, y):
    print("(\(x), \(y)) is just some arbitrary point")
}
// (1, -1) is on the line x == -y
</pre>

控制跳转语句：
continue
break
fallthrough
return
throw
<pre>
// continue
let puzzleInput = "great minds think alike"
var puzzleOutput = ""
for character in puzzleInput.characters {
    switch character {
    case "a", "e", "i", "o", "u", " ":
        continue
    default:
        puzzleOutput.append(character)
    }
}
print(puzzleOutput)
// grtmndsthnklk

// break
let numberSymbol: Character = "三"  // Simplified Chinese for the number 3
var possibleIntegerValue: Int?
switch numberSymbol {
case "1", "١", "一", "๑":
    possibleIntegerValue = 1
case "2", "٢", "二", "๒":
    possibleIntegerValue = 2
case "3", "٣", "三", "๓":
    possibleIntegerValue = 3
case "4", "٤", "四", "๔":
    possibleIntegerValue = 4
default:
    break
}
if let integerValue = possibleIntegerValue {
    print("The integer value of \(numberSymbol) is \(integerValue).")
} else {
    print("An integer value could not be found for \(numberSymbol).")
}
// The integer value of 三 is 3.

// Swift语言中的switch不会从上一个case分支跳转到下一个case分支中，只要第一个匹配到的case分支并执行完了语句，整个switch代码块就执行完了。如果需要跳转到下一个case，可以使用Fallthrough
let integerToDescribe = 5
var description = "The number \(integerToDescribe) is"
switch integerToDescribe {
case 2, 3, 5, 7, 11, 13, 17, 19:
    description += " a prime number, and also"
    fallthrough
default:
    description += " an integer."
}
print(description)
// The number 5 is a prime number, and also an integer.
</pre>

在swift中可以在其他循环和条件语句中内嵌循环和条件语句，labeled语句通过在冒号前放一个标签充当关键字，当使用break或continue时，可以跳转到指定关键字
<pre>
gameLoop: while square != finalSquare {
    if ++diceRoll == 7 { diceRoll = 1 }
    switch square + diceRoll {
    case finalSquare:
        // diceRoll will move us to the final square, so the game is over
        break gameLoop
    case let newSquare where newSquare > finalSquare:
        // diceRoll will move us beyond the final square, so roll again
        continue gameLoop
    default:
        // this is a valid move, so find out its effect
        square += diceRoll
        square += board[square]
    }
}
</pre>

guard语句，跟if语句一样，都需要一个布尔值来判定，但guard语句一定有else分句，当条件不是true的时候执行else。
<pre>
func greet(person: [String: String]) {
    guard let name = person["name"] else {
        return
    }

    print("Hello \(name)!")

    guard let location = person["location"] else {
        print("I hope the weather is nice near you.")
        return
    }

    print("I hope the weather is nice in \(location).")
}

greet(["name": "John"])
// Hello John!
// I hope the weather is nice near you.
greet(["name": "Jane", "location": "Cupertino"])
// Hello Jane!
// I hope the weather is nice in Cupertino.
</pre>

检查API是否可用
<pre>
if #available(iOS 9, OSX 10.10, *) {
    // 在iOS使用iOS 9 APIs on, 在OS X使用OS X v10.10 APIs
} else {
    // 老的API
}
</pre>