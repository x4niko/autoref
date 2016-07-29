---
title: swift学习笔记21：扩展（Extensions）
tags:
  - iOS
  - swift
  - 扩展
id: 109
categories:
  - iOS
  - swift学习笔记
date: 2015-12-21 15:46:49
---

扩展 就是为一个已有的类、结构体、枚举类型或者协议类型添加新功能。这包括在没有权限获取原始源代码的情况下扩展类型的能力（即逆向建模 ）。
Swift 中的扩展可以：
1、添加计算型属性和计算型类型属性
2、定义实例方法和类型方法
3、提供新的构造器
4、定义下标
5、定义和使用新的嵌套类型
6、使一个已有类型符合某个协议
在 Swift 中，甚至可以对协议进行扩展，提供协议要求的实现，或者添加额外的功能，从而可以让符合协议的类型拥有这些功能。
注意：扩展可以为一个类型添加新的功能，但是不能重写已有的功能。

扩展语法（Extension Syntax）
使用关键字 extension 来声明扩展：
<pre>
extension SomeType {
    // 为 SomeType 添加的新功能写到这里
}
</pre>

可以通过扩展来扩展一个已有类型，使其采纳一个或多个协议。在这种情况下，无论是类还是结构体，协议名字的书写方式完全一样：
<pre>
extension SomeType: SomeProtocol, AnotherProctocol {
    // 协议实现写到这里
}
</pre>
注意：如果你通过扩展为一个已有类型添加新功能，那么新功能对该类型的所有已有实例都是可用的，即使它们是在这个扩展定义之前创建的。

计算型属性（Computed Properties）
扩展可以为已有类型添加计算型实例属性和计算型类型属性。下面的例子为 Swift 的内建 Double 类型添加了五个计算型实例属性，从而提供与距离单位协作的基本支持：
<pre>
extension Double {
    var km: Double { return self * 1_000.0 }
    var m : Double { return self }
    var cm: Double { return self / 100.0 }
    var mm: Double { return self / 1_000.0 }
    var ft: Double { return self / 3.28084 }
}

let oneInch = 25.4.mm
print("One inch is \(oneInch) meters")
// 打印 “One inch is 0.0254 meters”
let threeFeet = 3.ft
print("Three feet is \(threeFeet) meters")
// 打印 “Three feet is 0.914399970739201 meters”

// 可以用于所有接受 Double 值的数学计算
let aMarathon = 42.km + 195.m
print("A marathon is \(aMarathon) meters long")
// 打印 “A marathon is 42195.0 meters long”
</pre>
扩展可以添加新的计算型属性，但是不可以添加存储型属性，也不可以为已有属性添加属性观察器。

构造器（Initializers）
扩展可以为已有类型添加新的构造器。这可以扩展其它类型，将定制类型作为其构造器参数，或者提供该类型的原始实现中未提供的额外初始化选项。
扩展能为类添加新的便利构造器，但是它们不能为类添加新的指定构造器或析构器。指定构造器和析构器必须总是由原始的类实现来提供。

注意：如果你使用扩展为一个值类型添加构造器，且该值类型的原始实现中未定义任何定制的构造器时，你可以在扩展中的构造器里调用逐一成员构造器。如果该值类型为所有存储型属性提供了默认值，还可以在扩展中的构造器里调用默认构造器。
正如在值类型的构造器代理中描述的，如果把定制的构造器写在值类型的原始实现中，上述规则将不再适用。
下面的例子定义了一个用于描述几何矩形的结构体 Rect。这个例子同时定义了两个辅助结构体 Size 和 Point，它们都把 0.0 作为所有属性的默认值：
<pre>
struct Size {
    var width = 0.0, height = 0.0
}
struct Point {
    var x = 0.0, y = 0.0
}
struct Rect {
    var origin = Point()
    var size = Size()
}
</pre>

因为结构体 Rect 未提供定制的构造器，因此它会获得一个逐一成员构造器。又因为它为所有存储型属性提供了默认值，它又会获得一个默认构造器。这些构造器可以用于构造新的 Rect 实例：
<pre>
let defaultRect = Rect()
let memberwiseRect = Rect(origin: Point(x: 2.0, y: 2.0),
    size: Size(width: 5.0, height: 5.0))

// 提供一个额外的接受指定中心点和大小的构造器来扩展 Rect 结构体：
extension Rect {
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}

// 调用该结构体的逐一成员构造器 init(origin:size:)
let centerRect = Rect(center: Point(x: 4.0, y: 4.0),
    size: Size(width: 3.0, height: 3.0))
// centerRect 的原点是 (2.5, 2.5)，大小是 (3.0, 3.0)
</pre>

方法（Methods）
扩展可以为已有类型添加新的实例方法和类型方法。下面的例子为 Int 类型添加了一个名为 repetitions 的实例方法：
<pre>
extension Int {
    func repetitions(task: () -> Void) {
        for _ in 0..<self {
            task()
        }
    }
}

3.repetitions({
    print("Hello!")
})
// Hello!
// Hello!
// Hello!

// 可以使用尾随闭包让调用更加简洁
3.repetitions {
    print("Goodbye!")
}
// Goodbye!
// Goodbye!
// Goodbye!
</pre>

可变实例方法（Mutating Instance Methods）
通过扩展添加的实例方法也可以修改该实例本身。结构体和枚举类型中修改 self 或其属性的方法必须将该实例方法标注为 mutating，正如来自原始实现的可变方法一样。
下面的例子为 Swift 的 Int 类型添加了一个名为 square 的可变方法，用于计算原始值的平方值：
<pre>
extension Int {
    mutating func square() {
        self = self * self
    }
}

var someInt = 3
someInt.square()
// someInt 的值现在是 9
</pre>

下标（Subscripts）
扩展可以为已有类型添加新下标。这个例子为 Swift 内建类型 Int 添加了一个整型下标。该下标 [n] 返回十进制数字从右向左数的第 n 个数字：
123456789[0] 返回 9
123456789[1] 返回 8
……以此类推。
<pre>
extension Int {
    subscript(var digitIndex: Int) -> Int {
        var decimalBase = 1
        while digitIndex > 0 {
            decimalBase *= 10
            --digitIndex
        }
        return (self / decimalBase) % 10
    }
}

746381295[0]
// 返回 5
746381295[1]
// 返回 9
746381295[2]
// 返回 2
746381295[8]
// 返回 7
</pre>

如果该 Int 值没有足够的位数，即下标越界，那么上述下标实现会返回 0，犹如在数字左边自动补 0：
<pre>
746381295[9]
// 返回 0，即等同于：
0746381295[9]
</pre>

嵌套类型（Nested Types）
扩展可以为已有的类、结构体和枚举添加新的嵌套类型：
<pre>
extension Int {
    enum Kind {
        case Negative, Zero, Positive
    }
    var kind: Kind {
        switch self {
        case 0:
            return .Zero
        case let x where x > 0:
            return .Positive
        default:
            return .Negative
        }
    }
}

func printIntegerKinds(numbers: [Int]) {
    for number in numbers {
        switch number.kind {
        case .Negative:
            print("- ", terminator: "")
        case .Zero:
            print("0 ", terminator: "")
        case .Positive:
            print("+ ", terminator: "")
        }
    }
    print("")
}
printIntegerKinds([3, 19, -27, 0, -6, 0, 7])
// 打印 “+ + - 0 - 0 +”
</pre>
由于已知 number.kind 是 Int.Kind 类型，因此在 switch 语句中，Int.Kind 中的所有成员值都可以使用简写形式，例如使用 .Negative 而不是 Int.Kind.Negative。