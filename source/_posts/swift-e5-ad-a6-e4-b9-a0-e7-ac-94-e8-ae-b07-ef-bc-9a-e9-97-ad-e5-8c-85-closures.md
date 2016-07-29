---
title: swift学习笔记7：闭包 (Closures)
tags:
  - iOS
  - swift
  - 闭包
id: 76
categories:
  - iOS
  - swift学习笔记
date: 2015-11-26 21:03:06
---

闭包（Closures）是自包含的功能代码块，可以在代码中使用或者用来作为参数传值。
用Swift标准库中的sort方法来简化闭包写法  
sort函数有两种传参
参数一：数组  
参数二：一个闭包：带有两个参数，这两个参数类型与数组中的元素类型相同，返回值是Bool  
<pre>
// 第一种方式：使用函数  
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
func backwards(s1: String, _ s2: String) -> Bool {
    return s1 > s2
}
var reversed = names.sort(backwards)
// ["Ewa", "Daniella", "Chris", "Barry", "Alex"]
</pre>

闭包表达式：
{ (parameters) -> return type in
    statements
}
<pre>
// 第二种方式：使用闭包方式  
reversed = names.sort({ (s1: String, s2: String) -> Bool in
    return s1 > s2
})
</pre>

根据上下文推断类型
因为排序闭包函数是作为sorted函数的参数进行传入的，Swift可以推断其参数和返回值的类型。 sorted期望第二个参数是类型为(String, String) -> Bool的函数，因此实际上String,String和Bool类型并不需要作为闭包表达式定义中的一部分。因为所有的类型都可以被正确推断，返回箭头(->)和围绕在参数周围的括号也可以被省略：
<pre>
reversed = names.sort( { s1, s2 in return s1 > s2 } )
</pre>
实际上任何情况下，通过内联闭包表达式构造的闭包作为参数传递给函数时，都可以推断出闭包的参数和返回值类型，这意味着几乎不需要利用完整格式构造任何内联闭包。

单表达式闭包隐式返回（Implicit Return From Single-Expression Clossures）
单行表达式闭包可以通过隐藏return关键字来隐式返回单行表达式的结果，如上版本的例子可以改写为：
<pre>
reversed = names.sort( { s1, s2 in s1 > s2 } )
</pre>

参数名称缩写（Shorthand Argument Names）
Swift 自动为内联函数提供了参数名称缩写功能，您可以直接通过$0,$1,$2来顺序调用闭包的参数。
如果您在闭包表达式中使用参数名称缩写，您可以在闭包参数列表中省略对其的定义，并且对应参数名称缩写的类型会通过函数类型进行推断。 in关键字也同样可以被省略，因为此时闭包表达式完全由闭包函数体构成：
<pre>
reversed = names.sort( { $0 > $1 } )
// 在这个例子中，$0和$1表示闭包中第一个和第二个String类型的参数。
</pre>

运算符函数（Operator Functions）
还有一种更简短的方式来撰写上面例子中的闭包表达式。Swift的String类型定义了关于大于号 (>) 的字符串实现，其作为一个函数接受两个String类型的参数并返回Bool类型的值。可以简单地传递一个大于号，Swift可以自动推断出您想使用大于号的字符串函数实现：
<pre>
reversed = names.sort(>)
</pre>

尾随闭包（Trailing Closures）
如果您需要将一个很长的闭包表达式作为最后一个参数传递给函数，可以使用尾随闭包来增强函数的可读性。 尾随闭包是一个书写在函数括号之后的闭包表达式，函数支持将其作为最后一个参数调用。
<pre>
func someFunctionThatTakesAClosure(closure: () -> Void) {
    // 函数体部分
}
// 下面是不使用尾随闭包进行函数调用
someFunctionThatTakesAClosure({
    // 闭包主体部分
})
// 下面是使用尾随闭包进行函数调用
someFunctionThatTakesAClosure() {
    // 闭包主体部分
}

// 注意： 如果函数只需要闭包表达式一个参数，当您使用尾随闭包时，至可以把()省略掉。
reversed = names.sort() { $0 > $1 }
reversed = names.sort { $0 > $1 }

// 当闭包非常长以至于不能在一行中进行书写时，尾随闭包变得非常有用。 举例来说，Swift的Array类型有一个map方法，其获取一个闭包表达式作为其唯一参数。 数组中的每一个元素调用一次该闭包函数，并返回该元素所映射的值(也可以是不同类型的值)。 具体的映射方式和返回值类型由闭包来指定。当提供给数组闭包函数后，map方法将返回一个新的数组，数组中包含了与原数组一一对应的映射后的值。
// 下例介绍了如何在map方法中使用尾随闭包将Int类型数组[16,58,510]转换为包含对应String类型的数组
let digitNames = [
    0: "Zero", 1: "One", 2: "Two",   3: "Three", 4: "Four",
    5: "Five", 6: "Six", 7: "Seven", 8: "Eight", 9: "Nine"
]
let numbers = [16, 58, 510]

let strings = numbers.map {
    (var number) -> String in
    var output = ""
    while number > 0 {
        output = digitNames[number % 10]! + output
        number /= 10
    }
    return output
}
// strings类型推断为[String]
// ["OneSix", "FiveEight", "FiveOneZero"]
// 注意： 字典digitNames下标后跟着一个叹号 (!)，因为字典下标返回一个可选值 (optional value)，表明即使该 key 不存在也不会查找失败。 在上例中，它保证了number % 10可以总是作为一个digitNames字典的有效下标 key。 因此叹号可以用于强制解析 (force-unwrap) 存储在可选下标项中的String类型值。
</pre>

捕获值（Capturing Values）
Swift最简单的闭包形式是嵌套函数，也就是定义在其他函数的函数体内的函数。 嵌套函数可以捕获其外部函数所有的参数以及定义的常量和变量。
<pre>
func makeIncrementer(forIncrement amount: Int) -> () -> Int {
    var runningTotal = 0
    func incrementer() -> Int {
        runningTotal += amount
        return runningTotal
    }
    return incrementer
}
//每次调用该函数的时候都会修改runningTotal的值，incrementor捕获了当前runningTotal变量的引用，而不是仅仅复制该变量的初始值。捕获一个引用保证了当makeIncrementor结束时候并不会消失，也保证了当下一次执行incrementor函数时，runningTotal可以继续增加。
// 定义一个叫做incrementByTen的常量，该常量指向一个每次调用会加10的incrementor函数
let incrementByTen = makeIncrementer(forIncrement: 10)
incrementByTen()      // 10
incrementByTen()      // 20
incrementByTen()      // 30
</pre>

闭包是引用类型（Closures Are Reference Types）
无论将函数/闭包赋值给一个常量还是变量，实际上都是将常量/变量的值设置为对应函数/闭包的引用。这也意味着如果将闭包赋值给了两个不同的常量/变量，两个值都会指向同一个闭包。
<pre>
let alsoIncrementByTen = incrementByTen
alsoIncrementByTen()      // 40
</pre>

非逃逸闭包(Nonescaping Closures)
当一个闭包作为参数传到一个函数中，但是这个闭包在函数返回之后才被执行，称该闭包从函数中逃逸。当定义接受闭包作为参数的函数时，你可以在参数名之前标注@noescape，用来指明这个闭包是不允许“逃逸”出这个函数的。
<pre>
func someFunctionWithNoescapeClosure(@noescape closure: () -> Void) {
    closure()
}
</pre>
举个例子，sort(_:)方法接受一个用来进行元素比较的闭包作为参数。这个参数被标注了@noescape，因为它确保自己在排序结束之后就没用了。
一种能使闭包“逃逸”出函数的方法是，将这个闭包保存在一个函数外部定义的变量中。举个例子，很多启动异步操作的函数接受一个闭包参数作为 completion handler。这类函数会在异步操作开始之后立刻返回，但是闭包直到异步操作结束后才会被调用。在这种情况下，闭包需要“逃逸”出函数，因为闭包需要在函数返回之后被调用。例如：
<pre>
var completionHandlers: [() -> Void] = []
func someFunctionWithEscapingClosure(completionHandler: () -> Void) {
    completionHandlers.append(completionHandler)
}
</pre>
someFunctionWithEscapingClosure(_:)函数接受一个闭包作为参数，该闭包被添加到一个函数外定义的数组中。如果你试图将这个参数标注为@noescape，你将会获得一个编译错误。
将闭包标注为@noescape能在闭包中隐式地引用self
<pre>
class SomeClass {
    var x = 10
    func doSomething() {
        someFunctionWithEscapingClosure { self.x = 100 }
        someFunctionWithNoescapeClosure { x = 200 }
    }
}

let instance = SomeClass()
instance.doSomething()
print(instance.x)     // "200"

completionHandlers.first?()
print(instance.x)     // "100"
</pre>

自动闭包（Autoclosures）
自动闭包是一种自动创建的闭包，用于包装传递给函数作为参数的表达式。这种闭包不接受任何参数，当它被调用的时候，会返回被包装在其中的表达式的值。这种便利语法让你能够用一个普通的表达式来代替显式的闭包，从而省略闭包的花括号。
我们经常会调用一个接受闭包作为参数的函数，但是很少实现那样的函数。举个例子来说，assert(condition:message:file:line:)函数接受闭包作为它的condition参数和message参数；它的condition参数仅会在 debug 模式下被求值，它的message参数仅当condition参数为false时被计算求值。
自动闭包让你能够延迟求值，因为代码段不会被执行直到你调用这个闭包。延迟求值对于那些有副作用（Side Effect）和代价昂贵的代码来说是很有益处的，因为你能控制代码什么时候执行。下面的代码展示了闭包如何延时求值。
<pre>
var customersInLine = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
print(customersInLine.count)
// prints "5"

let customerProvider = { customersInLine.removeAtIndex(0) }
print(customersInLine.count)
// prints "5"

print("Now serving \(customerProvider())!")
// prints "Now serving Chris!"
print(customersInLine.count)
// prints "4"
</pre>
尽管在闭包的代码中，customersInLine的第一个元素被移除了，不过在闭包被调用之前，这个元素是不会被移除的。如果这个闭包永远不被调用，那么在闭包里面的表达式将永远不会执行，那意味着列表中的元素永远不会被移除。请注意，customerProvider的类型不是String，而是() -> String，一个没有参数且返回值为String的函数。
将闭包作为参数传递给函数时，你能获得同样的延时求值行为。
<pre>
// customersInLine is ["Alex", "Ewa", "Barry", "Daniella"]
func serveCustomer(customerProvider: () -> String) {
    print("Now serving \(customerProvider())!")
}
serveCustomer( { customersInLine.removeAtIndex(0) } )
// prints "Now serving Alex!"
</pre>
serveCustomer(_:)接受一个返回@autoclosure特性暗含了@noescape特性，这个特性在非逃逸闭包一节中有描述。如果你想让这个闭包可以“逃逸”，则应该使用@autoclosure(escaping)特性.顾客名字的显式的闭包。下面这个版本的serveCustomer(_:)完成了相同的操作，不过它并没有接受一个显式的闭包，而是通过将参数标记为@autoclosure来接收一个自动闭包。现在你可以将该函数当做接受String类型参数的函数来调用。customerProvider参数将自动转化为一个闭包，因为该参数被标记了@autoclosure特性。
<pre>
// customersInLine is ["Ewa", "Barry", "Daniella"]
func serveCustomer(@autoclosure customerProvider: () -> String) {
    print("Now serving \(customerProvider())!")
}
serveCustomer(customersInLine.removeAtIndex(0))
// prints "Now serving Ewa!"
</pre>
注意：过度使用autoclosures会让你的代码变得难以理解。上下文和函数名应该能够清晰地表明求值是被延迟执行的。
<pre>
// customersInLine is ["Barry", "Daniella"]
var customerProviders: [() -> String] = []
func collectCustomerProviders(@autoclosure(escaping) customerProvider: () -> String) {
    customerProviders.append(customerProvider)
}
collectCustomerProviders(customersInLine.removeAtIndex(0))
collectCustomerProviders(customersInLine.removeAtIndex(0))

print("Collected \(customerProviders.count) closures.")
// prints "Collected 2 closures."
for customerProvider in customerProviders {
    print("Now serving \(customerProvider())!")
}
// prints "Now serving Barry!"
// prints "Now serving Daniella!"
</pre>
在上面的代码中，collectCustomerProviders(_:)函数并没有调用传入的customerProvider闭包，而是将闭包追加到了customerProviders数组中。这个数组定义在函数作用域范围外，这意味着数组内的闭包将会在函数返回之后被调用。因此，customerProvider参数必须允许“逃逸”出函数作用域。