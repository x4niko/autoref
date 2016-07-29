---
title: swift学习笔记8：枚举 (Enumerations)
tags:
  - iOS
  - swift
  - 枚举
id: 80
categories:
  - iOS
  - swift学习笔记
date: 2015-11-27 01:02:49
---

Swift中的枚举不必给每一个枚举成员提供一个值，可以给枚举成员提供一个原始值，该值的类型可以是字符串，字符，或是一个整型值或浮点数。
枚举语法
<pre>enum SomeEnumeration {
    // 枚举定义放在这里
}
</pre>
用枚举表示指南针四个方向：
<pre>enum CompassPoint {
    case North
    case South
    case East
    case West
}
</pre>
枚举中定义的值（如 North，South，East和West）是这个枚举的成员值，使用case关键字来定义一个新的枚举成员值。在上面的CompassPoint例子中，North，South，East和West不会被隐式地赋值为0，1，2和3，这些枚举成员本身就是完整的值，这些值的类型是已经明确定义好的CompassPoint类型。
多个成员值可以出现在同一行上，用逗号隔开：
<pre>enum Planet {
    case Mercury, Venus, Earth, Mars, Jupiter, Saturn, Uranus, Neptune
}
</pre>
像Swift中其他类型一样，它们的名字（例如CompassPoint和Planet）应该以一个大写字母开头。
<pre>
var directionToHead = CompassPoint.West
</pre>
directionToHead的类型可以在它被CompassPoint的某个值初始化时推断出来，一旦directionToHead被声明为CompassPoint类型，可以使用更简短的点语法将其设置为另一个CompassPoint的值：
<pre>
directionToHead = .East
</pre>
使用switch语句匹配单个枚举值：
<pre>directionToHead = .South
switch directionToHead {
    case .North:
        print("Lots of planets have a north")
    case .South:
        print("Watch out for penguins")
    case .East:
        print("Where the sun rises")
    case .West:
        print("Where the skies are blue")
}
// 输出 "Watch out for penguins”
</pre>

关联值（Associated Values）
可以定义Swift枚举来存储任意类型的关联值，如果需要的话，每个枚举成员的关联值类型可以各不相同。
在Swift中，使用如下方式定义表示两种关联值类型的枚举：
<pre>enum Barcode {
    case UPCA(Int, Int, Int, Int)
    case QRCode(String)
}
</pre>
以上理解为：“定义一个名为Barcode的枚举类型，它的一个成员值是具有(Int，Int，Int，Int)类型关联值的UPCA，另一个成员值是具有String类型关联值的QRCode。”这个定义不提供任何Int或String类型的关联值，它只是定义了，当Barcode常量和变量等于Barcode.UPCA或Barcode.QRCode时，可以存储的关联值的类型。
然后可以使用任意一种关联值类型来创建，例如：
<pre>var productBarcode = Barcode.UPCA(8, 85909, 51226, 3)
</pre>
上面的例子创建了一个名为productBarcode的变量，并将Barcode.UPCA赋值给它，关联的元组值为(8, 85909, 51226, 3)。
再如：
<pre>productBarcode = .QRCode("ABCDEFGHIJKLMNOP")
</pre>
这时，原始的Barcode.UPCA和其整数关联值被新的Barcode.QRCode和其字符串关联值所替代。Barcode类型的常量和变量可以存储一个.UPCA或者一个.QRCode（连同它们的关联值），但是在同一时间只能存储这两个值中的一个。

可以在switch的case分支代码中提取每个关联值作为一个常量（用let前缀）或者作为一个变量（用var前缀）来使用：
<pre>switch productBarcode {
case .UPCA(let numberSystem, let manufacturer, let product, let check):
    print("UPC-A: \(numberSystem), \(manufacturer), \(product), \(check).")
case .QRCode(let productCode):
    print("QR code: \(productCode).")
}
// 输出 "QR code: ABCDEFGHIJKLMNOP."
</pre>
如果一个枚举成员的所有关联值都被提取为常量，或者都被提取为变量，为了简洁，可以只在成员名称前标注一个let或者var：
<pre>switch productBarcode {
case let .UPCA(numberSystem, manufacturer, product, check):
    print("UPC-A: \(numberSystem), \(manufacturer), \(product), \(check).")
case let .QRCode(productCode):
    print("QR code: \(productCode).")
}
// 输出 "QR code: ABCDEFGHIJKLMNOP."
</pre>

原始值（Raw Values）
作为关联值的替代选择，枚举成员可以被默认值（称为原始值）预填充，这些原始值的类型必须相同。

使用 ASCII 码作为原始值的枚举：
<pre>enum ASCIIControlCharacter: Character {
    case Tab = "\t"
    case LineFeed = "\n"
    case CarriageReturn = "\r"
}
</pre>
枚举类型ASCIIControlCharacter的原始值类型被定义为Character，并设置了一些比较常见的ASCII控制字符。原始值可以是字符串，字符，或者任意整型值或浮点型值。每个原始值在枚举声明中必须是唯一的。
注意：原始值和关联值是不同的。原始值是在定义枚举时被预先填充的值，像上述三个 ASCII 码。对于一个特定的枚举成员，它的原始值始终不变。关联值是创建一个基于枚举成员的常量或变量时才设置的值，枚举成员的关联值可以变化。

原始值的隐式赋值（Implicitly Assigned Raw Values）
在使用原始值为整数或者字符串类型的枚举时，不需要显式地为每一个枚举成员设置原始值，Swift会自动赋值。
当使用整数作为原始值时，隐式赋值的值依次递增1。如果第一个枚举成员没有设置原始值，其原始值将为0。
<pre>enum Planet: Int {
    case Mercury = 1, Venus, Earth, Mars, Jupiter, Saturn, Uranus, Neptune
}
</pre>
在上面的例子中，Plant.Mercury的显式原始值为1，Planet.Venus的隐式原始值为2，依次类推。

当使用字符串作为枚举类型的原始值时，每个枚举成员的隐式原始值为该枚举成员的名称。
<pre>enum CompassPoint: String {
    case North, South, East, West
}
</pre>
上面例子中，CompassPoint.South拥有隐式原始值South，依次类推。

使用枚举成员的rawValue属性可以访问该枚举成员的原始值：
<pre>let earthsOrder = Planet.Earth.rawValue
// earthsOrder 值为 3

let sunsetDirection = CompassPoint.West.rawValue
// sunsetDirection 值为 "West"
</pre>

使用原始值初始化枚举实例（Initializing from a Raw Value）
如果在定义枚举类型的时候使用了原始值，那么将会自动获得一个初始化方法，这个方法接收一个叫做rawValue的参数，参数类型即为原始值类型，返回值则是枚举成员或nil。可以使用这个初始化方法来创建一个新的枚举实例。
利用原始值7创建了枚举成员Uranus：
<pre>
let possiblePlanet = Planet(rawValue: 7)
// possiblePlanet类型为Planet?，值为Planet.Uranus
</pre>
并非所有Int值都可以找到一个匹配的行星。因此，原始值构造器总是返回一个可选的枚举成员。在上面的例子中，possiblePlanet是Planet?类型，或者说“可选的Planet”。
<pre>
let positionToFind = 9
if let somePlanet = Planet(rawValue: positionToFind) {
   switch somePlanet {
      case .Earth:
         print("Mostly harmless")
      default:
         print("Not a safe place for humans")
   }
} else {
   print("There isn't a planet at position \(positionToFind)")
}
// 输出 "There isn't a planet at position 9
</pre>

递归枚举（Recursive Enumerations）
递归枚举（recursive enumeration）是一种枚举类型，它有一个或多个枚举成员使用该枚举类型的实例作为关联值。使用递归枚举时，编译器会插入一个间接层。在枚举成员前加上indirect来表示该成员可递归。
下面的例子中，枚举类型存储了简单的算术表达式：

<pre>
enum ArithmeticExpression {
   case Number(Int)
   indirect case Addition(ArithmeticExpression, ArithmeticExpression)
   indirect case Multiplication(ArithmeticExpression, ArithmeticExpression)
}
</pre>
也可以在枚举类型开头加上indirect关键字来表明它的所有成员都是可递归的：
<pre>
indirect enum ArithmeticExpression {
   case Number(Int)
   case Addition(ArithmeticExpression, ArithmeticExpression)
   case Multiplication(ArithmeticExpression, ArithmeticExpression)
}
</pre>
上面定义的枚举类型可以存储三种算术表达式：纯数字、两个表达式相加、两个表达式相乘。枚举成员Addition和Multiplication的关联值也是算术表达式。

要操作具有递归性质的数据结构，可以使用递归函数。例如，下面是一个对算术表达式求值的函数：
<pre>
func evaluate(expression: ArithmeticExpression) -&gt; Int {
   switch expression {
      case .Number(let value):
         return value
      case .Addition(let left, let right):
         return evaluate(left) + evaluate(right)
      case .Multiplication(let left, let right):
         return evaluate(left) * evaluate(right)
   }
}

// 计算 (5 + 4) * 2
let five = ArithmeticExpression.Number(5)
let four = ArithmeticExpression.Number(4)
let sum = ArithmeticExpression.Addition(five, four)
let product = ArithmeticExpression.Multiplication(sum, ArithmeticExpression.Number(2))
print(evaluate(product))
// 输出 "18"
</pre>
该函数如果遇到纯数字，就直接返回该数字的值。如果遇到的是加法或乘法运算，则分别计算左边表达式和右边表达式的值，然后相加或相乘。